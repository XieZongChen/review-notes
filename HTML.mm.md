# HTML 复习

![image](https://github.com/XieZongChen/review-notes/assets/46394163/4aaeee9f-9400-45ae-b1c0-618c322aadad)

## src 和 href 的区别

都是 **用来引用外部的资源**

| src                                                                                               | href                                                                                 |
| ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 表示对资源的引用，它指向的内容会嵌入到当前标签所在的位置                                                    | 表示超文本引用，它指向一些网络资源，建立和当前元素或本文档的链接关系                            |
| 当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执⾏完毕，所以⼀般 js 脚本会放在页面底部 | 当浏览器识别到它他指向的⽂件时，就会并行下载资源，不会停⽌对当前⽂档的处理。 常用在 a、link 等标签上 |

## 对 HTML 语义化的理解

语义化是指 **根据内容的结构化（内容语义化），选择合适的标签（代码语义化）**。通俗来讲就是用正确的标签做正确的事情。

- 语义化的优点
  - 对机器友好，适合爬虫
  - 对开发者友好，可读性
- 常见标签：header 头部、nav 导航栏、section 区块（有语义化的 div）、main 主要区域、article 主要内容、aside 侧边栏、footer 底部

## DOCTYPE(文档类型) 的作⽤

告诉浏览器（解析器）应该以什么样（html 或 xhtml）的文档类型定义来解析文档，不同的渲染模式会影响浏览器对 CSS 代码甚⾄ JavaScript 脚本的解析

- 浏览器渲染页面的两种模式
  - CSS1Compat：标准模式（Strick mode），默认模式，浏览器使用 W3C 的标准解析渲染页面。在标准模式中，浏览器以其支持的最高标准呈现页面
  - BackCompat：怪异模式(混杂模式)(Quick mode)，浏览器使用自己的怪异模式解析渲染页面。在怪异模式中，页面以一种比较宽松的向后兼容的方式显示
 
## 文档声明（Doctype）和 `<!Doctype html>` 有何作用? 严格模式与混杂模式如何区分？它们有何意义

- 文档声明的作用：文档声明是为了告诉浏览器，当前 HTML 文档使用什么版本的 HTML 来写的，这样浏览器才能按照声明的版本来正确的解析。
- `<!Doctype html>` 的作用：`<!doctype html>` 的作用就是让浏览器进入标准模式，使用最新的 HTML5 标准来解析渲染页面；如果不写，浏览器就会进入混杂模式，我们需要避免此类情况发生。
- 严格模式与混杂模式的区分：
  - 严格模式： 又称为标准模式，指浏览器按照W3C标准解析代码；
  - 混杂模式： 又称怪异模式、兼容模式，是指浏览器用自己的方式解析代码。混杂模式通常模拟老式浏览器的行为，以防止老站点无法工作；
- 区分：网页中的 DTD（Document Type Definition，文档类型定义），直接影响到使用的是严格模式还是浏览模式，可以说 DTD 的使用与这两种方式的区别息息相关。
  - 如果文档包含严格的 DOCTYPE ，那么它一般以严格模式呈现（严格 DTD —— 严格模式）；
  - 包含过渡 DTD 和 URI 的 DOCTYPE ，也以严格模式呈现，但有过渡 DTD 而没有 URI （统一资源标识符，就是声明最后的地址）会导致页面以混杂模式呈现（有 URI 的过渡 DTD —— 严格模式；没有 URI 的过渡 DTD —— 混杂模式）；
  - DOCTYPE 不存在或形式不正确会导致文档以混杂模式呈现（DTD 不存在或者格式不正确——混杂模式）；
  - HTML5 没有 DTD ，因此也就没有严格模式与混杂模式的区别，实现时，已经尽可能大的实现了向后兼容(HTML5 没有严格和混杂之分)。
 
总之，**严格模式让各个浏览器统一执行一套规范兼容模式保证了旧网站的正常运行**。

## script 标签中 defer 和 async 的区别

如果没有 defer 或 async 属性，浏览器会立即加载并执行相应的脚本。它不会等待后续加载的文档元素，**读取到就会开始加载和执行**，这样就阻塞了后续文档的加载。

![image](https://github.com/XieZongChen/review-notes/assets/46394163/99fcdaa4-9a13-4c29-8466-a10e14790fce)

注：上图中蓝色代表 js 脚本网络加载时间，红色代表 js 脚本执行时间，绿色代表 html 解析

|               | defer                                                                                                                                | async                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------- |
| 执行顺序        | 多个带 defer 属性的标签，按照加载顺序执行                                                                                                 | 多个带 async 属性的标签，不能保证加载的顺序 |
| 脚本是否并行执行 | 加载后续文档的过程和 js 脚本的加载(此时仅加载不执行)是并行进行的(异步)，js 脚本需要等到文档所有元素解析完成之后才执行，DOMContentLoaded 事件触发执行之前 | 后续文档的加载和执行与 js 脚本的加载和执行是并行进行的，即异步执行 |

## 常用的 meta 标签有哪些

meta 标签由 name 和 content 属性定义，用来描述网页文档的属性（网页的作者，网页描述，关键词等），除了 HTTP 标准固定了一些 name 作为大家使用的共识，开发者还可以自定义 name

- charset，用来描述 HTML 文档的编码类型 `<meta charset="UTF-8" >`
- keywords，页面关键词 `<meta name="keywords" content="关键词" />`
- description，页面描述 `<meta name="description" content="页面描述内容" />`
- refresh，页面重定向和刷新 `<meta http-equiv="refresh" content="0;url=" />`
- viewport，适配移动端，可以控制视口的大小和比例 `<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">`，其中 content 参数有以下几种：
  - width viewport ：宽度(数值/device-width)
  - height viewport ：高度(数值/device-height)
  - initial-scale ：初始缩放比例
  - maximum-scale ：最大缩放比例
  - minimum-scale ：最小缩放比例
  - user-scalable ：是否允许用户缩放(yes/no）

## HTML5 有哪些更新

### [语义化标签](#对-html-语义化的理解)

### 媒体标签

1. [audio 音频](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/audio)
  - `<audio src='' controls autoplay loop='true'></audio>`
  - 属性：controls-控制面板、autoplay-自动播放、loop=‘true’-循环播放
2. [video 视频](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/video)
  - `<video src='' poster='imgs/aa.jpg' controls></video>`
  - 属性：poster-封面、controls-控制面板、width-宽、height-高
3. [source 标签](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/source)，指定视频源，以适配不同浏览器所支持的视频格式
  - ```
     <video>
        <source src='aa.flv' type='video/flv'></source>
        <source src='aa.mp4' type='video/mp4'></source>
     </video>
    ```

### 表单

1. 表单类型
  - email ：能够验证当前输入的邮箱地址是否合法
  - url ： 验证 URL
  - number ： 只能输入数字，其他输入不了，而且自带上下增大减小箭头，max 属性可以设置为最大值，min 可以设置为最小值，value 为默认值。
  - search ： 输入框后面会给提供一个小叉，可以删除输入的内容，更加人性化。
  - range ： 可以提供给一个范围，其中可以设置 max 和 min 以及 value，其中 value 属性可以设置为默认值
  - color ： 提供了一个颜色拾取器
  - time ： 时分秒
  - data ： 日期选择年月日
  - datatime ： 时间和日期(目前只有 Safari 支持)
  - datatime-local ：日期时间控件
  - week ：周控件
  - month：月控件
2. 表单属性
  - placeholder ：提示信息
  - autofocus ：自动获取焦点
  - autocomplete=“on” 或者 autocomplete=“off” 使用这个属性需要有两个前提：
    - 表单必须提交过
    - 必须有 name 属性。
  - required：要求输入框不能为空，必须有值才能够提交。
  - pattern=" " 里面写入想要的正则模式，例如手机号 patte="^(+86)?\d{10}$"
  - multiple：可以选择多个文件或者多个邮箱
  - form=" form 表单的 ID" 
3. 表单事件
  - oninput 每当 input 里的输入框内容发生变化都会触发此事件
  - oninvalid 当验证不通过时触发此事件 

### 进度条、度量器

- [progress 标签](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/progress) 用来表示任务的进度（IE、Safari 不支持）
  - max 用来表示任务的进度
  - value 表示已完成多少
- [meter 属性](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meter) 用来显示剩余容量或剩余库存（IE、Safari 不支持）
  - high/low：规定被视作高/低的范围
  - max/min：规定最大/小值
  - value：规定当前度量值
- 设置规则：min < low < high < max

### DOM 查询操作

1. [document.querySelector()](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelector)
2. [document.querySelectorAll()](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelectorAll)

它们选择的对象可以是标签，可以是类(需要加点)，可以是 ID(需要加#)

### Web 存储

1. localStorage - 没有时间限制的数据存储
2. sessionStorage - 针对一个 session 的数据存储

[文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API)

### 其他

1. 拖放：[拖放](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/draggable) 是一种常见的特性，即抓取对象以后拖到另一个位置：`<img draggable="true" />`
2. 画布（canvas）：[canvas 元素](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 使用 JavaScript 在网页上绘制图像。画布是一个矩形区域，可以控制其每一像素。canvas 拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法
3. SVG：[SVG](https://developer.mozilla.org/zh-CN/docs/Web/SVG) 指可伸缩矢量图形，用于定义用于网络的基于矢量的图形，使用 XML 格式定义图形，图像在放大或改变尺寸的情况下其图形质量不会有损失，它是万维网联盟的标准
4. 地理定位：[Geolocation（地理定位）](https://developer.mozilla.org/zh-CN/docs/Web/API/Geolocation_API) 用于定位用户的位置

### 总结

- 新增：
  - 语义化标签：nav、header、footer、aside、section、article
  - 音频、视频标签：audio、video
  - 数据存储：localStorage、sessionStorage
  - canvas（画布）、Geolocation（地理定位）、websocket（通信协议）
  - input 标签新增属性：placeholder、autocomplete、autofocus、required
  - history API：go、forward、back、pushstate
- 移除：
  - 纯表现的元素：basefont，big，center，font, s，strike，tt，u
  - 对可用性产生负面影响的元素：frame，frameset，noframes 

## img 的 srcset 属性的作⽤

srcset 属性用于设置不同屏幕密度下，img 自动加载的不同图片。

```html
// 屏幕密度为 1x 的情况下加载 image-128.png, 屏幕密度为 2x 时加载 image-256.png
<img src="image-128.png" srcset="image-256.png 2x" />
```

按照上面的实现，不同的屏幕密度都要设置图片地址，假设目前的屏幕密度有 1x,2x,3x,4x 四种，如果每一个图片都设置 4 张图片，加载就会很慢。所以就有了新的 srcset 标准。代码如下：

```html
// 默认显示 128px, 如果视区宽度大于 360px, 则显示 340px
<img src="image-128.png"
     srcset="image-128.png 128w, image-256.png 256w, image-512.png 512w"
     sizes="(max-width: 360px) 340px, 128px" />
```

其中 srcset 指定图片的地址和对应的图片质量。sizes 用来设置图片的尺寸零界点。对于 srcset 中的 w 单位，可以理解成图片质量。如果可视区域小于这个质量的值，就可以使用。浏览器会自动选择一个最小的可用图片。

sizes 语法：`sizes="[media query] [length], [media query] [length] ... "`

详情见 [响应式图片](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)

## 行内元素有哪些？块级元素有哪些？ 空(void)元素有那些？

- 行内元素有：a b span img input select strong；
- 块级元素有：div ul ol li dl dt dd h1 h2 h3 h4 h5 h6 p；
- 空元素，即没有内容的 HTML 元素。空元素是在开始标签中关闭的，也就是空元素没有闭合标签：
  - 常见的有：`<br>、<hr>、<img>、<input>、<link>、<meta>`；
  - 鲜见的有：`<area>、<base>、<col>、<colgroup>、<command>、<embed>、<keygen>、<param>、<source>、<track>、<wbr>`。

## 说一下 web worker

在 HTML 页面中，如果在执行脚本时，页面的状态是不可响应的，直到脚本执行完成后，页面才变成可响应。web worker 是运行在后台的 js，使用独立线程独立于主线程，使主线程（通常是 UI 线程）的运行不会被阻塞/放慢。主线程与 worker 线程间使用 postMessage 方法发送消息。这样在进行复杂操作的时候，就不会阻塞主线程了。不能直接在 worker 线程中操作 DOM 元素，或使用 window 对象中的某些方法和属性。worker 可以通过 XMLHttpRequest 来访问网络，但 XMLHttpRequest 的 responseXML 和 channel 属性始终返回 null。详情见 [Web Worker API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API)

如何创建 web worker：
1. 检测浏览器对于 web worker 的支持性
2. 创建 web worker 文件（js，回传函数等）
3. 创建 web worker 对象

## HTML5 的离线储存怎么使用，它的工作原理是什么

离线存储是指：在用户没有与因特网连接时，可以正常访问站点或应用，在用户与因特网连接时，更新用户机器上的缓存文件。

原理：
- 早期的 html5 manifest 离线存储缓存技术：是基于一个新建的 .appcache 文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像 cookie 一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。现在 html 的 manifest 已废弃，转而使用 link 的 manifest，接收一个 json 缓存目录，总体来说开发难度大，维护难度大，需要服务端配合。详情见 [Web app manifests](https://developer.mozilla.org/en-US/docs/Web/Manifest)
- 灵巧方案：借助 Service Worker 和 cacheStorage 来实现，详情见 [借助Service Worker和cacheStorage缓存及离线开发](https://www.zhangxinxu.com/wordpress/2017/07/service-worker-cachestorage-offline-develop/)、[通过 Service workers 让 PWA 离线工作](https://developer.mozilla.org/zh-CN/docs/Web/Progressive_web_apps/Tutorials/js13kGames/Offline_Service_workers)、[前端离线缓存之 “ Service Worker ”](https://juejin.cn/post/6976553406890508295)

<img src="https://github.com/XieZongChen/review-notes/assets/46394163/2e990009-b6b3-46e9-a70e-04a811cf618a" alt="image" style="zoom: 40%;" />

<img src="https://github.com/XieZongChen/review-notes/assets/46394163/d5a91fee-d835-4db2-b5ab-c1ac3ffe8fe2" alt="image" style="zoom: 40%;" />

<img src="https://github.com/XieZongChen/review-notes/assets/46394163/c5805738-94f4-43f4-880d-745bab00fb08" alt="image" style="zoom: 40%;" />

## title 与 h1 的区别、b 与 strong 的区别、i 与 em 的区别？

- strong 标签有语义，是起到加重语气的效果，strong 标签加强字符的语气都是通过粗体来实现的，搜索引擎更侧重 strong 标签。b 标签是没有语义的，只是一个简单加粗标签。b 标签之间的字符都设为粗体。
- title 属性没有明确意义只表示是个标题。H1 则表示层次明确的标题，对页面信息的抓取有很大的影响
- i 内容展示为斜体。em 表示强调的文本

## iframe 有那些优点和缺点

iframe 元素会创建包含另外一个文档的内联框架（即行内框架）。

优点：
- 用来加载速度较慢的内容（如广告）
- 可以使脚本可以并行下载
- 可以实现跨子域通信

缺点：
- iframe 会阻塞主页面的 onload 事件
- 无法被一些搜索引擎索识别
- 会产生很多页面，不容易管理

## label 的作用是什么？如何使用

label 标签来定义表单控件的关系，当用户选择 label 标签时，浏览器会自动将焦点转到和 label 标签相关的表单控件上。

使用方法一：

```html
<label for="mobile">Number:</label>
<input type="text" id="mobile"/>
```

使用方法二：

```html
<label>Date:<input type="text"/></label>
```

## Canvas 和 SVG 的区别

### SVG：

SVG 可缩放矢量图形（Scalable Vector Graphics）是基于可扩展标记语言 XML 描述的 2D 图形的语言，SVG 基于 XML 就意味着 SVG DOM 中的每个元素都是可用的，可以为某个元素附加 Javascript 事件处理器。在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。

其特点如下：

- 不依赖分辨率
- 支持事件处理器
- 最适合带有大型渲染区域的应用程序（比如谷歌地图）
- 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）
- 不适合游戏应用

### Canvas：

Canvas 是画布，通过 Javascript 来绘制 2D 图形，是逐像素进行渲染的。其位置发生改变，就会重新进行绘制。

其特点如下：

- 依赖分辨率
- 不支持事件处理器
- 弱的文本渲染能力
- 能够以 .png 或 .jpg 格式保存结果图像
- 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

注：矢量图，也称为面向对象的图像或绘图图像，在数学上定义为一系列由线连接的点。矢量文件中的图形元素称为对象。每个对象都是一个自成一体的实体，它具有颜色、形状、轮廓、大小和屏幕位置等属性。

## head 标签有什么作用，其中什么标签必不可少

`<head>` 标签用于定义文档的头部，它是所有头部元素的容器。`<head>` 中的元素可以引用脚本、指示浏览器在哪里找到样式表、提供元信息等。

文档的头部描述了文档的各种属性和信息，包括文档的标题、在 Web 中的位置以及和其他文档的关系等。绝大多数文档头部包含的数据都不会真正作为内容显示给读者。
下面这些标签可用在 head 部分：`<base>, <link>, <meta>, <script>, <style>, <title>`。
其中 `<title>` 定义文档的标题，它是 head 部分中唯一必需的元素。

## 浏览器乱码的原因是什么？如何解决

产生乱码的原因：
- 网页源代码是 gbk 的编码，而内容中的中文字是 utf-8 编码的，这样浏览器打开即会出现 html 乱码，反之也会出现乱码；
- html 网页编码是 gbk，而程序从数据库中调出呈现是 utf-8 编码的内容也会造成编码乱码；
- 浏览器不能自动检测网页编码，造成网页乱码。

解决办法：
- 使用软件编辑 HTML 网页内容；
- 如果网页设置编码是gbk，而数据库储存数据编码格式是UTF-8，此时需要程序查询数据库数据显示数据前进程序转码；
- 如果浏览器浏览时候出现网页乱码，在浏览器中找到转换编码的菜单进行转换。

## 渐进增强和优雅降级之间的区别

- 渐进增强（progressive enhancement）：主要是针对低版本的浏览器进行页面重构，保证基本的功能情况下，再针对高级浏览器进行效果、交互等方面的改进和追加功能，以达到更好的用户体验。
- 优雅降级（graceful degradation）： 一开始就构建完整的功能，然后再针对低版本的浏览器进行兼容。
- 两者区别：
  - **优雅降级** 是从复杂的现状开始的，并试图减少用户体验的供给；而 **渐进增强** 是从一个非常基础的，能够起作用的版本开始的，并在此基础上不断扩充，以适应未来环境的需要；
  - **优雅降级**（功能衰竭）意味着往回看，而 **渐进增强** 则意味着往前看，同时保证其根基处于安全地带。
  - **优雅降级** 观点认为应该针对那些最高级、最完善的浏览器来设计网站。而将那些被认为“过时”或有功能缺失的浏览器下的测试工作安排在开发周期的最后阶段，并把测试对象限定为主流浏览器（如 IE、Mozilla 等）的前一个版本。 在这种设计范例下，旧版的浏览器被认为仅能提供“简陋却无妨 (poor, but passable)” 的浏览体验。可以做一些小的调整来适应某个特定的浏览器。但由于它们并非我们所关注的焦点，因此除了修复较大的错误之外，其它的差异将被直接忽略。
  - **渐进增强** 观点则认为应关注于内容本身。内容是建立网站的诱因，有的网站展示它，有的则收集它，有的寻求，有的操作，还有的网站甚至会包含以上的种种，但相同点是它们全都涉及到内容。这使得“渐进增强”成为一种更为合理的设计范例。这也是它立即被 Yahoo 所采纳并用以构建其“分级式浏览器支持 (Graded Browser Support)”策略的原因所在。

## 说一下 HTML5 drag API

- dragstart：事件主体是 **被拖放元素**，在开始拖放被拖放元素时触发。
- darg：事件主体是 **被拖放元素**，在正在拖放被拖放元素时触发。
- dragend：事件主体是 **被拖放元素**，在整个拖放操作结束时触发。
- dragenter：事件主体是 **目标元素**，在被拖放元素进入目标元素时触发。
- dragleave：事件主体是 **目标元素**，在被拖放元素移出目标元素是触发。
- dragover：事件主体是 **目标元素**，在被拖放在某元素内移动时触发。
- drop：事件主体是 **目标元素**，在目标元素完全接受被拖放元素时触发。


















