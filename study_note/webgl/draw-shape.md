### 1. ring [demo](https://codepen.io/sueblue/pen/poodmPQ)
```
    var canvas=document.getElementById('canv');
    var ctx=canvas.getContext('2d');
    ctx.beginPath(); 
    ctx.arc(100,100,50,0,2*Math.PI,false); //圆的结束弧度2*Math.PI
    ctx.moveTo(140,100); //移动笔触，到内圆的边上
    ctx.arc(100,100,40,0,2*Math.PI,false);
    ctx.strokeStyle = "red"; 
    ctx.stroke(); 
```
### 2. 渐变 [demo](https://codepen.io/sueblue/pen/LYYQRpm)
```
    var canvas=document.getElementById('canv');
    var ctx=canvas.getContext('2d');
    
    var gradient = ctx.createLinearGradient(30, 30, 70, 70); 
    gradient.addColorStop(0, "white"); 
    gradient.addColorStop(1, "black");

    ctx.fillStyle = gradient; 
    ctx.fillRect(30, 30, 50, 50); 
```

### 3. getImageData时常会遇到cross-origin 问题
mdn有相关资料--[方案](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_enabled_image)

