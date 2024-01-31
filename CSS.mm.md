# CSS 复习

![image](https://github.com/XieZongChen/review-notes/assets/46394163/febba1c4-f6d7-4cf9-86a8-489967f4c3a3)

## CSS 选择器及其优先级

| 选择器 | 格式 | 优先级权重 |
| - | - | - |
| id 选择器 | #id | 100 |
| 类选择器 | #classname | 10 |
| 属性选择器 | a[ref=“eee”] |	10 |
| 伪类选择器 | li:last-child | 10 |
| 标签选择器  | div | 1 |
| 伪元素选择器 | li::after | 1 |
| 相邻兄弟选择器 | h1+p | 0 |
| 子选择器 | ul>li | 0 |
| 后代选择器 | li a | 0 |
| 通配符选择器 | * | 0 |

对于选择器的优先级：
- 标签选择器、伪元素选择器：1
- 类选择器、伪类选择器、属性选择器：10
- id 选择器：100
- 内联样式：1000
- !important > 行内样式 > ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性

注意事项：
- !important 声明的样式的优先级最高；
- 如果优先级相同，则最后出现的样式生效；
- 继承得到的样式的优先级最低；
- 通用选择器（*）、子选择器（>）和相邻同胞选择器（+）并不在这四个等级中，所以它们的权值都为 0 ；
- 样式表的来源不同时，优先级顺序为：内联样式 > 内部样式 > 外部样式 > 浏览器用户自定义样式 > 浏览器默认样式。

## CSS 中可继承与不可继承属性有哪些

### 无继承性的属性

1. display：规定元素应该生成的框的类型
2. 文本属性：
   - vertical-align：垂直文本对齐
   - text-decoration：规定添加到文本的装饰
   - text-shadow：文本阴影效果
   - white-space：空白符的处理
   - unicode-bidi：设置文本的方向
3. 盒子模型的属性：width、height、margin、border、padding
4. 背景属性：background、background-color、background-image、background-repeat、background-position、background-attachment
5. 定位属性：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index
6. 生成内容属性：content、counter-reset、counter-increment
7. 轮廓样式属性：outline-style、outline-width、outline-color、outline
8. 页面样式属性：size、page-break-before、page-break-after
9. 声音样式属性：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

### 有继承性的属性

1. 字体系列属性
   - font-family：字体系列
   - font-weight：字体的粗细
   - font-size：字体的大小
   - font-style：字体的风格
2. 文本系列属性
   - text-indent：文本缩进
   - text-align：文本水平对齐
   - line-height：行高
   - word-spacing：单词之间的间距
   - letter-spacing：中文或者字母之间的间距
   - text-transform：控制文本大小写（就是 uppercase、lowercase、capitalize 这三个）
   - color：文本颜色
3. 元素可见性
   - visibility：控制元素显示隐藏
4. 列表布局属性
   - list-style：列表风格，包括 list-style-type、list-style-image 等
5. 光标属性
   - cursor：光标显示为何种形态
  
## display 的属性值及其作用

| 属性值 | 作用 |
| ----- | --- |
| none | 元素不显示，并且会从文档流中移除 |
| block | 块类型。默认宽度为父元素宽度，可设置宽高，换行显示 |
| inline | 行内元素类型。默认宽度为内容宽度，不可设置宽高，同行显示 |
| inline-block | 默认宽度为内容宽度，可以设置宽高，同行显示 |
| list-item | 像块类型元素一样显示，并添加样式列表标记 |
| table | 此元素会作为块级表格来显示 |
| inherit | 规定应该从父元素继承 display 属性的值 |

## display 的 block、inline 和 inline-block 的区别

1. block：会独占一行，多个元素会另起一行，可以设置 width、height、margin 和 padding 属性；
2. inline：元素不会独占一行，设置 width、height 属性无效。但可以设置水平方向的 margin 和 padding 属性，不能设置垂直方向的 padding 和 margin；
3. inline-block：将对象设置为 inline 对象，但对象的内容作为 block 对象呈现，之后的内联对象会被排列在同一行内。

对于行内元素和块级元素，其特点如下：

1. 行内元素
   - 设置宽高无效；
   - 可以设置水平方向的 margin 和 padding 属性，不能设置垂直方向的 padding 和 margin；
   - 不会自动换行；
2. 块级元素
   - 可以设置宽高；
   - 设置 margin 和 padding 都有效；
   - 可以自动换行；
   - 多个块状，默认排列从上到下。

## 隐藏元素的方法有哪些

- overflow:hidden：用来隐藏元素溢出部分，占据空间，无法响应点击事件。
- display: none：渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。
- visibility: hidden：元素在页面中仍占据空间，但是不会响应绑定的监听事件。
- opacity: 0：将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。
- position: absolute：通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。
- z-index: 负值：来使其他元素遮盖住该元素，以此来实现隐藏。
- clip/clip-path ：使用元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。
- transform: scale(0,0)：将元素缩放为 0，来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

## display:none 与 visibility:hidden 的区别

这两个属性都是让元素隐藏，不可见。两者区别如下：

1. 在渲染树中
   - display:none 会让元素完全从渲染树中消失，渲染时不会占据任何空间；
   - visibility:hidden 不会让元素从渲染树中消失，渲染的元素还会占据相应的空间，只是内容不可见。
2. 是否是继承属性
   - display:none 是非继承属性，子孙节点会随着父节点从渲染树消失，通过修改子孙节点的属性也无法显示；
   - visibility:hidden 是继承属性，子孙节点消失是由于继承了 hidden，通过设置 visibility:visible 可以让子孙节点显示；
3. 修改常规文档流中元素的 display 通常会造成文档的重排，但是修改 visibility 属性只会造成本元素的重绘；
4. 如果使用读屏器，设置为 display:none 的内容不会被读取，设置为 visibility:hidden 的内容会被读取。

## link 和 @import 的区别

两者都是外部引用 CSS 的方式，它们的区别如下：
- link 是 XHTML 标签，除了加载 CSS 外，还可以定义 RSS 等其他事务；@import 属于 CSS 范畴，只能加载 CSS。
- link 引用 CSS 时，在页面载入时同时加载；@import 需要页面网页完全载入以后加载。
- link 是 XHTML 标签，无兼容问题；@import 是在 CSS2.1 提出的，低版本的浏览器不支持。
- link 支持使用 Javascript 控制 DOM 去改变样式；而 @import 不支持。 

## transition 和 animation 的区别

- transition 是过度属性，强调过度，它的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。它类似于 flash 的补间动画，设置一个开始关键帧，一个结束关键帧。
- animation 是动画属性，它的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。它也类似于 flash 的补间动画，但是它可以设置多个关键帧（用 @keyframe 定义）完成动画。

## 伪元素和伪类的区别和作用

伪元素：在内容元素的前后插入额外的元素或样式，但是这些元素实际上并不在文档中生成。它们只在外部显示可见，但不会在文档的源代码中找到它们，因此，称为“伪”元素。例如：

```css
p::before { content:"第一章："; }
p::after { content:"Hot!"; }
p::first-line { background:red; }
p::first-letter { font-size:30px; }
```

伪类：将特殊的效果添加到特定选择器上。它是已有元素上添加类别的，不会产生新的元素。例如：

```css
a:hover { color: #FF00FF }
p:first-child { color: red }
```

总结：伪类是通过在元素选择器上加⼊伪类改变元素状态，⽽伪元素通过对元素的操作进⾏对元素的改变。

## 对 requestAnimationframe 的理解






















