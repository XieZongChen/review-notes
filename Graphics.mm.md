# 图形化相关复习

## WebGL 绘制一个立方体

1. 准备上下文
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
