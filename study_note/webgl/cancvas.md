### 1. canvas基本用法
   ```
       <canvas id="drawing" width=" 200" height="200">A drawing of something.
       </canvas> 
   ```
  取得绘图上下文对象的引用，需要调用 getContext()方法并传入上下文的名字。传入"2d"
  + canvas坐标
  
    ![坐标图](./../img/Canvas_default_grid.png)

### 2. 填充和描边--fillStyle 和 strokeStyle
  
  + 两个属性的值可以是字符串、渐变对象或模式对象，而且它们的默认值都是"#000000"。

### 3. 绘制矩形 
  
  + 矩形是唯一一种可以直接在 2D 上下文中绘制的形状
  + fillRect()、 strokeRect()和 clearRect()
    都能接收 4个参数：矩形的 x坐标、矩形的 y坐标、矩形 宽度和矩形高度。这些参数的单位都是像素。 

### 4. 绘制路径
  
  + 要绘制路径，首先必须调用 beginPath()方法，表示要开始 
     - arc(x, y, radius, startAngle, endAngle, counterclockwise)：
       - 以(x,y)为圆心绘 制一条弧线，弧线半径为 radius，起始和结束角度（用弧度表示）分别为 startAngle 和 endAngle。endAngle通常使用数字*Math.PI。
       - 后一个参数表示 startAngle 和 endAngle 是否按逆时针方向计算，值为 false 表示按顺时针方向计算。 

     - arcTo(x1, y1, x2, y2, radius)：
     从上一点开始绘制一条弧线，到(x2,y2)为止，并且以 给定的半径 radius 穿过(x1,y1)

     - bezierCurveTo(c1x, c1y, c2x, c2y, x, y)：
       - 从上一点开始绘制一条曲线，到(x,y)为 止，并且以(c1x,c1y)和(c2x,c2y)为控制点

     - lineTo(x, y)：从上一点开始绘制一条直线，到(x,y)为止。 
     
     - moveTo(x, y)：将绘图游标移动到(x,y)，不画线

     - quadraticCurveTo(cx, cy, x, y)：从上一点开始绘制一条二次曲线，到(x,y)为止，并 且以(cx,cy)作为控制点
     
     - rect(x, y, width, height)：
       - 从点(x,y)开始绘制一个矩形，宽度和高度分别由 width 和 height 指定。
       - 这个方法绘制的是矩形路径，而不是 strokeRect()和 fillRect()所绘制的独 立的形状
     
 + 如果想绘制一条连接到路径起点的线条，可以调用 closePath()。

 + 如果路径已经完成，你想用 fillStyle 填充它，可以调用 fill()方法。

 + 另外，还可 以调用 stroke()方法对路径描边，描边使用的是 strokeStyle。后还可以调用 clip()，这个方法 可以在路径上创建一个剪切区域。


  





