# 记一次项目内存异常的排查过程与解决

## 问题描述与分析

### 问题场景

我司在做一款 AI 问答应用，在 A 同学的一个新功能上线后，问答页面在问多轮问题后，会出现页面崩溃的问题，在内存不高的老旧电脑上尤其明显。在 A 同学排查无果后，我决定伸出援手。

在观察反复复现的过程后，我总结了以下问题规律：

1. 新功能的范围在问答页面回答问题的辅助功能，其中包含对回答问题的 Markdown 的样式定制。
2. 页面崩溃发生在询问会返回代码的问题后，且需要大量的类似问题回答才会触发。
3. 页面奔溃的错误代码为 Out of Memory，这能确定是内存溢出引起的。

### 问题分析

由观察到的现象和规律，我分析出新功能范围内可能造成问题的地方：

1. 回答打字机效果的实现

这个功能是我在最初版本实现的，所以对这印象很深。由于实现用了 `setTimeout` 这个 api，确实存在泄露的风险。但考虑到奔溃问题在这次新版本出现，并未在此前的版本出现过，所以需要去确定一下新版本是否有动到这个功能 `clearTimeout` 的地方。

2. 代码样式渲染相关代码

这个功能既有大量我们的代码逻辑，又使用了很多第三方库，所以需要借助 chrome 的 memory 相关工具确定问题源。

## 问题排查与定位

### 确定打字机效果

通过阅读新版本代码，确定并未动到 `clearTimeout` 的地方，可暂时排除此处。

### 排查代码样式渲染相关代码

定位引起问题的源头，则需要用到 chrome 的开发者工具。

**1. 先打开开发者工具中的 Performance monitor 面板，将所有指标勾选，再次进行问题复现，观察哪些指标在复现过程中有异常**

![image](https://github.com/user-attachments/assets/85008f7a-dff9-4380-8fae-5cac5f22ca83)

![image](https://github.com/user-attachments/assets/ffa3e87a-f44d-44b5-a4c8-25212779259b)

在复现过程中，我发现 DOM nodes 和 JS event listeners 两个指标都有很明显的上升：

- DOM nodes 指标在上升后，通过滚动页面，会发现指标有动态变化。当屏幕滚动到有代码的地方时会明显升高，其他地方会明显下降。结合代码样式渲染原理就是将代码分词后每个词生成一个 dom，确定此指标的上升在可接受范围内，问题源不在此。

- JS event listeners 指标在上升后，当停止一切页面操作，仅会有少量下降。当回答的代码出现时，会有很明显的飙升，且代码渲染后也得不到释放（下降）。所以确定问题在事件监听方面。

**2. 使用开发者工具 Memory 面板中的 Heap snapshot 功能，给复现过程做个快照**

![image](https://github.com/user-attachments/assets/ecb164a7-697c-47e2-9172-c8dd6786662e)

快照的时间长度比较短，所以需要在复现操作的关键操作时点击 Take snapshot，让快照覆盖回答代码出现时的全过程。

观察快照，会发现 EventListener 和 V8EventListener 确实很高。再次印证是事件监听新增很多而未卸载的原因。

![image](https://github.com/user-attachments/assets/52bf6705-2ae4-47b3-a3b2-695fe5df8775)

为什么我不会去怀疑 system/Context 呢？因为这是运行时的上下文，EventListener 也在其中，可以将它看成很多指标的总合，怀疑它没太大的意义。

**3. 在开发者工具 Console 面板中使用 `getEventListeners(XXX)` api 查找有哪些监听是大量到不正常的**

![image](https://github.com/user-attachments/assets/e48c0eae-a720-4f4e-8a60-3079e9b4ded6)

幸运的是我在 window 中就发现了大量到不正常的监听事件，不用一个一个去找其他节点上的监听。

问题的根源在 DOMContentLoaded 事件的大量监听，而这个事件在我们项目代码里是没有监听的，所以开始排查代码样式相关的第三方库的源码。

**4. 在代码样式渲染相关的第三方库里排查 DOMContentLoaded 事件的监听**

当我们展开之前在 Console 面板中打印的 DOMContentLoaded 事件监听，是可以找到其所在位置的：

![image](https://github.com/user-attachments/assets/b7ffe84e-2a74-4aaf-bb9f-8cbf12c79add)

注意，这只能说明在打包后，这个代码在 rehype-highlight 中。在这个项目项目打包时，我将第三方依赖单独分了 chunk，所以在 sourcemap 中会指向这个 chunk。

而这个第三方库在打包时，其所依赖的 dependencies 有可能是直接打包进一个 chunk 的。所以在排查依赖包源码时，如果没有发现目标代码也不用太心急，需要顺着 dependencies 链去继续向下找。

在第三方库中排查问题是一个很麻烦的事，毕竟每个库都有可能在 dependencies 中大量运用其他的库，一个一个排查会很浪费时间，所以我们一定要挑重点去排查。既然是代码样式的渲染，那我们一定要顺着这方面的库去排查，从而节省时间。

经过 `'rehype-highlight'` => `'lowlight'` => `'highlight.js'` 路线的排查，最终发现 highlight.js 这个库中有 DOMContentLoaded 事件。

## 问题解决与总结

### 问题解决

当发现第三方库有问题时，最好先去 issues 列表看看有没有人提到过这个问题：

![image](https://github.com/user-attachments/assets/3d791241-c1f9-4e4f-9286-6e545077f060)

[具体 issue 链接](https://github.com/highlightjs/highlight.js/issues/4086)

很幸运，这个问题不仅被别人提到过，而且被官方解决了，看来我们项目用的版本太旧了。

而更惊喜的是，提 issue 的开发者也写了 [排查问题的博客](https://blog.imken.moe/archives/847)，我们的排查路线略有不同，看完他的排查路线，我受益匪浅。


