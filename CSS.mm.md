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

















