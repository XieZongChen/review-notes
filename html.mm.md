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









