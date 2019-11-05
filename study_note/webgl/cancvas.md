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
       - 以(x,y)为圆心绘 制一条弧线，弧线半径为 radius，起始和结束角度（用[弧度](https://baike.baidu.com/item/%E5%BC%A7%E5%BA%A6/1533188?fr=aladdin)表示）分别为 startAngle 和 endAngle。一周的弧度数为2πr/r=2π，360°角=2π弧度。
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
 
 + 由于路径的使用很频繁，所以就有了一个名为 isPointInPath()的方法。这个方法接收 x和 y坐标作为参数，用于在路径被关闭之前确定画布上的某一点是否位于路径。
 
 + moveTo和lineTo结合起来就是两点之间画一条直线

### 5. 绘制文本

 + fillText()和 strokeText() -- 参数：要绘制的文本字符串、x 坐 标、y坐标和可选的大像素宽度
 
 + fillText()方法使用 fillStyle 属性绘制文本，而 strokeText()方法使用 strokeStyle 属性为文本描边。相对来说，还 是使用 fillText()的时候更多，因为该方法模仿了在网页中正常显示文本
 
 + font,textAlign,textBaseline都有默认值，fillText和strokeText都以此三个属性为基础。

### 6. 变换
 
 + rotate(angle)---围绕原点旋转图像 angle 弧度。 
 
 + scale(scaleX, scaleY)---缩放图像，在 x方向乘以 scaleX，在 y方向乘以 scaleY。scaleX 和 scaleY 的默认值都是 1.0。  

 + translate(x, y)--将坐标原点移动到(x,y)。执行这个变换之后，坐标(0,0)会变成之前由(x,y) 表示的点。
 
 +  transform(m1_1, m1_2, m2_1, m2_2, dx, dy)：直接修改变换矩阵，方式是乘以如下矩阵：

    m1_1|m1_2|dx
    --|:--:|--:
    m1_2|m2_2|dy
    0|0|1     
 + setTransform(m1_1, m1_2, m2_1, m2_2, dx, dy)：将变换矩阵重置为默认状态，然后 再调用 transform()。 

### 7. 跟踪上下文的状态变化 

 + 如果你知道将来还要返回某组属性与变换的组合，可以调用 save()方法。 
 + 调用这个方法后，当时的所有设置都会进入一个栈结构，得以妥善保管。
 + 然后可以对上下文进行其他修改。
 + 等想要回到之前保存的设置时，可以调用 restore()方法，在保存设置的栈结构中向前返回一级，恢复之前的状态。

### 8. 绘制图像
 + 2D绘图上下文内置了对图像的支持。把一幅图像绘制到画布上，可以使用 drawImage() 方法。
 + drawImage()方法的这种调用方式总可传入 9个参数：要绘制的图像、源图像的 x坐标、源图像的 y坐标、源图像的宽度、源 图像的高度、目标图像的 x 坐标、目标图像的 y 坐标、目标图像的宽度、目标图像的高度。这样调用 drawImage()方法可以获得多的控制,把图像中的某个区域绘制到上下文中。
 + 除了给 drawImage()方法传入 HTML img 元素外，还可以传入另一个 canvas 元素作为其第一 个参数。这样，就可以把另一个画布内容绘制到当前画布上。 
 + 

### 9. 绘制阴影
 
 + 2D上下文会根据以下几个属性的值，自动为形状或路径绘制出阴影。 
 + shadowColor：用 CSS颜色格式表示的阴影颜色，默认为黑色。 
 + shadowOffsetX：形状或路径 x轴方向的阴影偏移量，默认为 0。 
 + shadowOffsetY：形状或路径 y轴方向的阴影偏移量，默认为 0。 
 + shadowBlur：模糊的像素数，默认 0，即不模糊。 
 + 这些属性都可以通过 context 对象来修改。只要在绘制前为它们设置适当的值，就能自动产生阴影

### 10. 渐变
 
 + 渐变由 CanvasGradient 实例表示，很容易通过 2D上下文来创建和修改。
 + 要创建一个新的线性渐变，可以调用 createLinearGradient()方法。
 + 这个方法接收 4个参数：起点的 x坐标、起点的 y坐 标、终点的 x 坐标、终点的 y 坐标。调用这个方法后，它就会创建一个指定大小的渐变，并返回 CanvasGradient 对象的实例。 
 + 创建了渐变对象后，下一步就是使用 addColorStop()方法来指定色标。
 + 这个方法接收两个参数： 色标位置和 CSS颜色值。色标位置是一个 0（开始的颜色）到 1（结束的颜色）之间的数字
 + 就可以把 fillStyle 或 strokeStyle 设置为这个对象，从而使用渐变来绘制 形状或描边。

### 11. 使用图像数据
 + 2D上下文的一个明显的长处就是，可以通过 getImageData()取得原始图像数据。
 + 这个方法接收 4个参数：要取得其数据的画面区域的 x和 y坐标以及该区域的像素宽度和高度。
 + 每个 ImageData 对象都有三个属性：width、height 和 data。其中 data 属性是一个数组，保存着图像中每一个像素的数据。在 data 数组中，每一个像素用4个元素来保存，分别表示红、绿、蓝和透明度值。
 + 操作原始像素值不仅能实现灰阶过滤，还能实现其他功能。  

### 12. 模式
 + 模式其实就是重复的图像，可以用来填充或描边图形。
 + 要创建一个新模式，可以调用 createPattern()方法并传入两个参数：一个 HTML img元素和一个表示如何重复图像的字符串。 其中，第二个参数的值与 CSS的 background-repeat 属性值相同，包括"repeat"、"repeat-x"、 "repeat-y"和"no-repeat"。
 + createPattern()方法的第一个参数也可以是一个video元素，或者另一个canvas元素。 
  

