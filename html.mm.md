# html 复习

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

## script 标签中 defer 和 async 的区别

如果没有 defer 或 async 属性，浏览器会立即加载并执行相应的脚本。它不会等待后续加载的文档元素，**读取到就会开始加载和执行**，这样就阻塞了后续文档的加载。

![image](https://github.com/XieZongChen/review-notes/assets/46394163/99fcdaa4-9a13-4c29-8466-a10e14790fce)

注：上图中蓝色代表 js 脚本网络加载时间，红色代表 js 脚本执行时间，绿色代表 html 解析

|               | defer                                                                                                                                | async                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------- |
| 执行顺序        | 多个带 defer 属性的标签，按照加载顺序执行                                                                                                 | 多个带 async 属性的标签，不能保证加载的顺序 |
| 脚本是否并行执行 | 加载后续文档的过程和 js 脚本的加载(此时仅加载不执行)是并行进行的(异步)，js 脚本需要等到文档所有元素解析完成之后才执行，DOMContentLoaded 事件触发执行之前 | 后续文档的加载和执行与 js 脚本的加载和执行是并行进行的，即异步执行 |

# 常用的 meta 标签有哪些

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









