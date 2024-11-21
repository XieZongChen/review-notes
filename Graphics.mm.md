# 图形化相关复习

## CSS

### Transform 的矩阵变换（md 查看软件需要支持 LaTeX）

> 注：本节部分参考 [理解 CSS3 transform 中的 Matrix](https://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%E7%9F%A9%E9%98%B5/)

Transform 的其他属性 —— **斜拉（`skew`）、缩放（`scale`）、旋转（`rotate`）以及位移（`translate`）背后的运行原理都是用 `matrix` 实现的**，不同属性修改 matrix 对应的几个参数的值。

Transform 的变换都是基于其 **齐次坐标** 的，坐标系统的原点是 `transform-origin` 属性设定的点，默认为目标节点的中心点。在二维空间中，普通的坐标 `(x, y)` 在齐次坐标中表示为 `(x, y, 1)`。齐次坐标的引入是为了让 **平移操作也能用矩阵乘法统一表示**。

`matrix` 是用于应用仿射变换的函数。它可以将输入的参数转化成一个 **仿射变换矩阵**。

`matrix(a, b, c, d, e, f)` 转化为：

$$
\begin{bmatrix}
a & c & e \\
b & d & f \\
0 & 0 & 1
\end{bmatrix}
$$

**浏览器对变换的处理过程**：

1. 当一个节点设置这个属性和值后，浏览器会为这个节点建立一个合成层（compositing layer）并放入。
2. 取这个节点的位置、大小、`transform-origin` 信息，生成 **原始齐次坐标系**。
3. 将坐标系内的关键坐标和几何信息与 `matrix` 生成的矩阵相乘，得到变换后的齐次坐标系。变换计算是 **几何级别** 的，所以这里计算的是元素的边界和坐标。
4. 使用变换后的坐标系重新计算新的边界框（bounding box），即包含这些变换后坐标的最小矩形区域。这些新的坐标将决定元素的新位置、大小和旋转。

注：**变换矩阵应用的结果并不直接影响每个像素的计算，而是影响整个元素的合成层，这样浏览器可以将变换应用于该合成层，而不必重新计算整个页面的布局**。

**矩阵变换计算过程**：

矩阵接受一个二维向量 (x, y) 并输出一个新的向量 (x', y')：

$$
\begin{bmatrix}
a & c & e \\
b & d & f \\
0 & 0 & 1
\end{bmatrix} \cdot
\begin{bmatrix}
x \\
y \\
1
\end{bmatrix} =
\begin{bmatrix}
x' \\
y' \\
1
\end{bmatrix}
$$

复习一下计算方式：

$$
\begin{bmatrix}
a & c & e \\
b & d & f \\
0 & 0 & 1
\end{bmatrix} \cdot
\begin{bmatrix}
x \\
y \\
1
\end{bmatrix} =
\begin{bmatrix}
ax + cy + e \\
bx + dy + f \\
0 + 0 + 1
\end{bmatrix}
$$

**Transform 可以使用的矩阵有两种**：

- `matrix`
  - 用于处理二维空间内的变换效果
  - 接收六个参数，构成一个 3 x 3 的矩阵，用于处理平面上的变换，如旋转、缩放、倾斜和位移等
  - 不支持直接设置透视效果
- `matrix3d`
  - 除了包含 matrix 的所有功能外，还能处理三维空间中的变换
  - 接收十六个参数，构成一个 4 x 4 的矩阵，用于处理三维空间中的变换，包括平面变换所有功能以及深度方向的缩放、旋转和位移
  - 支持设置透视效果，这是 3D 变换的一个重要特性。matrix3d 的最后一行四个数与透视相关

**使用矩阵变换的好处**：

- 统一的数学模型： 矩阵可以表示平移、旋转、缩放和倾斜等多种变换操作。通过矩阵乘法，可以方便地将多个变换合并成一个操作。
- GPU 加速： 矩阵变换非常适合并行计算（如 GPU 运算），这使得变换的处理非常快速，尤其是在动画中。
- 可扩展性： 通过矩阵乘法，可以轻松实现复杂的复合变换，而不用为每种变换单独处理逻辑。

#### 矩阵平移操作

观察之前提到的矩阵计算结束坐标的公式：

$$
\begin{bmatrix}
a & c & e \\
b & d & f \\
0 & 0 & 1
\end{bmatrix} \cdot
\begin{bmatrix}
x \\
y \\
1
\end{bmatrix} =
\begin{bmatrix}
ax + cy + e \\
bx + dy + f \\
0 + 0 + 1
\end{bmatrix}
$$

可以发现参数 `a`、`c`、`e` 控制着结束坐标的 x，参数 `b`、`d`、`f` 控制着结束坐标的 y。

假设我们要进行 `(0, 0)` => `(30, 30)` 的移动，那么最简单的方式可以这样建立方程：`x' = ax + cy + e = 1*0 + 0*0 + 30 = 30`、`y' = bx + dy + f = 0*0 + 1*0 + 30 = 30`。也就是 `transform: matrix(1, 0, 0, 1, 30, 30);`

**浏览器在底层处理 `transform: translate(30px, 30px);` 时，实际上会将其转换为等效的 `transform: matrix(1, 0, 0, 1, 30, 30);` 来计算和执行**。`translate`, `rotate` 等方法都是需要单位的，而 `matrix` 方法 `e`、`f` 参数的单位可以省略。

#### 矩阵缩放操作

在矩阵平移操作时，会发现 `matrix` 有两个参数需要传 `1`，这是因为只进行平移操作时，需要结束的图形与起始的图形保持一致，也就是比例一致（1：1）。

所以 `matrix(s1, 0, 0, s2, 0, 0)` 套用之前的公式后：`x' = ax + cy + e = s1*x + 0*y + 0 = s1*x`、`y' = bx + dy + f = 0*x + s2*y + 0 = s2*y`。这个操作等效于 `scale(s1, s2)`。

#### 矩阵旋转操作

矩阵的旋转操作要比平移和缩放复杂很多，需要用到三角函数的 `cos` 和 `sin`。

设需要旋转的角度为 `θ`，`matrix` 的参数则写为：`matrix(cosθ, sinθ, -sinθ, cosθ, 0, 0)`。结合矩阵公式后：`x' = x*cosθ - y*sinθ + 0 = x*cosθ - y*sinθ`、`y' = x*sinθ + y*cosθ + 0 = x*sinθ + y*cosθ`。这个操作等效于 `rotate(θdeg)`。

**注意**，`rotate(θdeg)` 中，参数是**无需计算**，例如旋转 30° 写为：`transform:rotate(30deg);`。而 `matrix` 的参数是要**传入值**的，也就是说需要计算出传入的 `cos`、`sin` 值：`transform: matrix(0.866025,0.500000,-0.500000,0.866025,0,0);`。**而在 Sass、Less 等 CSS 预处理器中，有提供包括 `cos`、`sin` 的数学函数帮助计算**。

#### 矩阵拉伸操作

矩阵的旋转操作需要用到三角函数 `tan`。

设需要旋转的角度为 `θ`，`matrix` 的参数则写为：`matrix(1, tan(θ2), tan(θ1), 1, 0, 0)`，`θ2` 为 y 轴倾斜角度，`θ1` 为 x 轴倾斜角度。结合矩阵公式后：`x' = x + y*tan(θ1) + 0 = x + y*tan(θ1)`、`y' = x*tan(θ2) + y + 0 = x*tan(θ2) + y`。这个操作等效于 `skew(θ1deg, θ2deg)`。

这里 `matrix` 和 `skew` 的参数也同旋转操作中提到的一样，前者需要传值，后者只需要传角度。

#### matrix3d

matrix3d 的原理与 matrix 大同小异，其生成的是 **4x4 的仿射变换矩阵**，它比 matrix 多了透视投影的变换。

在 3D 空间中，齐次坐标的形式为 `(x, y, z, w)`。`(x, y, z)` 是三维空间中的笛卡尔坐标。**`w` 是用于透视投影的齐次因子，通常为 1，但在某些透视变换中可能不是 1**（二维中由于不涉及透视，因此此因子始终为 1）。

`matrix3d()` 的参数是一个包含 16 个值的 4x4 矩阵：

```
transform: matrix3d(
    a1, b1, c1, d1, // 矩阵第一列
    a2, b2, c2, d2, // 矩阵第二列
    a3, b3, c3, d3, // 矩阵第三列
    a4, b4, c4, d4  // 矩阵第四列
);
```

写作矩阵为：

$$
\begin{bmatrix}
a_1 & a_2 & a_3 & a_4 \\
b_1 & b_2 & b_3 & b_4 \\
c_1 & c_2 & c_3 & c_4 \\
d_1 & d_2 & d_3 & d_4
\end{bmatrix}
$$

矩阵计算方式：

$$
\begin{bmatrix}
a_1 & a_2 & a_3 & a_4 \\
b_1 & b_2 & b_3 & b_4 \\
c_1 & c_2 & c_3 & c_4 \\
d_1 & d_2 & d_3 & d_4
\end{bmatrix} \cdot
\begin{bmatrix}
x \\
y \\
z \\
w
\end{bmatrix} =
\begin{bmatrix}
a_1x + a_2y + a_3z + a_4w \\
b_1x + b_2y + b_3z + b_4w \\
c_1x + c_2y + c_3z + c_4w \\
d_1x + d_2y + d_3z + d_4w
\end{bmatrix} =
\begin{bmatrix}
x' \\
y' \\
z' \\
w'
\end{bmatrix}
$$

**与二维相比，三维在渲染前还需透视投影的计算（归一化）：**

$$
x_{\text {final}}=\frac{x^{\prime}}{w^{\prime}}, \quad
y_{\text {final}}=\frac{y^{\prime}}{w^{\prime}}, \quad
z_{\text {final}}=\frac{z^{\prime}}{w^{\prime}}
$$

#### 矩阵的透视操作

透视本质上是将三维空间中的点投影到二维平面上的操作。其矩阵定义为：

$$
\mathbf{P}= \begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & -1 / d \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

其中：

- 𝑑 是透视距离。
- 变换后坐标需要通过齐次坐标 𝑤′ 的归一化完成映射。
- 这个变换等效于 `perspective(𝑑)`

`perspective(𝑑)` 的参数 𝑑 代表透视点，在透视绘画和图形设计中，透视点也被称为灭点或消失点。它是在透视变换中用于确定物体在视觉空间中的位置和相对关系的点。透视点的参数越大，越接近于正交透视

假设需要对三维坐标为 (x, y, z, 1) 的点进行 500px 的透视设置，可以使用 `transform: perspective(500px)` 来设置。而使用 `matrix3d` 则：

```
transform: matrix3d(
    1, 0, 0, 0,
    0, 1, 0, 0,
    0, 0, 1, -0.002, // -1/500 = -0.002
    0, 0, 0, 1
);
```

计算过程：

$$
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & -0.002 \\
0 & 0 & 0 & 1
\end{bmatrix} \cdot\begin{bmatrix}
x \\
y \\
z \\
1
\end{bmatrix}=\begin{bmatrix}
x \\
y \\
z \\
z \cdot(-0.002)+1
\end{bmatrix} =
\begin{bmatrix}
x^{\prime} \\
y^{\prime} \\
z^{\prime} \\
w^{\prime}
\end{bmatrix}
$$

透视归一化：

$$
x_{\text {final}}=\frac{x^{\prime}}{w^{\prime}}=\frac{x}{1-0.002z}, \quad
y_{\text {final}}=\frac{y^{\prime}}{w^{\prime}}=\frac{y}{1-0.002z}, \quad
z_{\text {final}}=\frac{z^{\prime}}{w^{\prime}}=\frac{z}{1-0.002z}
$$

**归一化** 深度值 $z_{\text {final}}$ 是为了映射点的深度范围到标准化的区间内。不同的 3D 图形 API（如 OpenGL 或 DirectX）可能会使用不同的深度范围。OpenGL 默认深度范围是 [−1, 1]，DirectX、WebGL 默认深度范围是 [0, 1]。

归一化深度值 $z_{\text {final}}$ 的用途：

1. **深度缓冲区（z-buffer）写入**：归一化后，深度值可以方便地存储在深度缓冲区中，记录当前像素的最近深度
2. **深度测试（Depth Test）**：当新像素与已有像素重叠时，比较新像素的 $z_{\text {final}}$ 值与深度缓冲区中的值。如果新像素更近（$z_{\text {final}}$ 更小），覆盖已有像素；否则，保留已有像素，丢弃新像素
3. **透明度排序**：在处理半透明物体时，渲染引擎需要根据深度值对物体排序，确保透明物体从后到前依次绘制

#### 矩阵的深度操作

`matrix3d()` 比 `matrix()` 多了深度的维度（z 轴），这从前文两个函数的传参就能看出。在 `transform` 中，提供了 `translate3d` 来进行三维的移动操作。`translate3d(0, 0, z)` 的等效 matrix3d 为：

$$
\mathbf{T}= \begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

具体的矩阵计算可以参考前文 [矩阵平移操作](#矩阵平移操作)。

**translate3d 与 perspective 的对比**：

- translate3d
  - **作用**： translate3d 可以通过第三个参数沿着 z 轴移动元素，即改变元素相对于观察者的深度位置。
    - **正值**：元素远离屏幕（“向后”）。
    - **负值**：元素靠近屏幕（“向前”）。
  - **本质**：调整元素在三维坐标系中的 z 值。
- perspective
  - **作用**： 用于设置三维场景的透视投影效果，模拟远近不同的视觉效果（类似相机的视场距离）。
    - **小值**：透视效果更明显（类似短焦镜头）。
    - **大值**：透视效果更平缓（类似长焦镜头）。
  - **本质**：定义了一个**视锥**的焦点到投影平面的距离，影响了元素在屏幕上的投影缩放。

**三维空间遮挡关系机制**：

- perspective 定义了视锥体的范围和透视缩放程度，间接影响物体的投影大小和 **相对深度值**。
- translate3d 直接改变物体的 z 坐标，影响 **深度值 $z_{\text {final}}$**，当多个物体的像素重叠时，浏览器根据深度值判断遮挡关系。
- `perspective` 决定了投影的透视效果（如何从 3D 映射到 2D），`translate3d` 决定了物体的深度（屏幕上的前后关系）。最终，**遮挡关系由投影后的深度值确定**。

**其他影响遮挡关系的因素**：

- **透明度**：对于半透明物体，需要额外排序，通常采用从远到近的顺序进行绘制。
- **深度缓冲区精度**：深度缓冲区有固定精度（如 16 位或 24 位）。如果两个物体的深度值差距很小，可能出现 **深度冲突（Z-fighting）**。
- **非线性深度映射**：深度值的映射通常是非线性的，更远的物体深度变化更敏感，这由透视矩阵的设计决定。

## WebGL 绘制一个立方体

1. 准备 WebGL 上下文

   ```html
   <body onload="main()">
     <canvas id="glcanvas" width="640" height="480">
       你的浏览器似乎不支持或者禁用了 HTML5 <code>&lt;canvas&gt;</code> 元素。
     </canvas>
   </body>
   ```

   ```javascript
   const canvas = document.querySelector('#glcanvas');
   // 初始化 WebGL 上下文
   const gl = canvas.getContext('webgl');

   // 确认 WebGL 支持性
   if (!gl) {
     alert('无法初始化 WebGL，你的浏览器、操作系统或硬件等可能不支持 WebGL。');
     return;
   }

   // 使用完全不透明的黑色清除所有图像
   gl.clearColor(0.0, 0.0, 0.0, 1.0);
   // 用上面指定的颜色清除缓冲区
   gl.clear(gl.COLOR_BUFFER_BIT);
   ```

   矩阵计算是一个很复杂的运算。通常使用第三方库 glMatrix 来处理运算。

   ```html
   <script
     src="https://cdnjs.cloudflare.com/ajax/libs/gl-matrix/2.8.1/gl-matrix-min.js"
     integrity="sha512-zhHQR0/H5SEBL3Wn6yYSaTTZej12z0hVZKOv3TwCUXT1z5qeqGcXJLLrbERYRScEDDpYIJhPC1fk31gqR783iQ=="
     crossorigin="anonymous"
     defer
   ></script>
   ```
