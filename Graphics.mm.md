# 图形化相关复习

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
   const canvas = document.querySelector("#glcanvas");
   // 初始化 WebGL 上下文
   const gl = canvas.getContext("webgl");

   // 确认 WebGL 支持性
   if (!gl) {
     alert("无法初始化 WebGL，你的浏览器、操作系统或硬件等可能不支持 WebGL。");
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
      defer>
   </script>
   ```




















 
