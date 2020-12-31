### [progress](https://css-tricks.com/html5-progress-element/)

以下是progress的基本标签
```
<progress></progress>
```
根据[w3c](https://html.spec.whatwg.org/multipage/form-elements.html)的定义，progress元素代表任务的完成度。一个progress元素标签有开始标签和结束标签。


除了通用的全局属性外，它还拥有两个自己的属性

+ max  -表示有多少任务需要完成。如果未设置，默认为1.0
+ value -表示现在的完成的进度。value的取值最好是 0.0<=value<=1.0
  
#### 进度条的状态

一个进度条有两种状态：不确定和已确定

1. 不确定-即进行中
   基于不同的浏览器和操作系统，进度条的展现都是不一样的。
   
   设置不确定进度条的样式是很简单的，因为它不包括value属性。可以利用css选择器not() 去设置它。

    ```
    progress:not([value]) {
            /* Styling here */
        }
    ```
2. 已确定-即已完成
   
   在文本中，一般将只关注如何设计进度条的确定状态，因此一般通过添加max和value属性来更改状态。

   ```
   <progress max="100" value="80"></progress>
   ```
   #### 设置进度条样式

   使用progress[value]选择器可以很容易的去设置已确定的进度条。在html中使用progress，一般只有不确定和已确定行两种状态。我更倾向于使用已确定的进度条，因为它明确了提供两种状态的区别。就像任何其他元素一样，我们可以使用width和height为进度条添加尺寸：
   
   ```
   progress[value] {
        width: 250px;
        height: 20px;
    }
   ```
   ##### webkit/blink(Chrome/Safari/Opera)

Google Chrome, Apple Safari , Opera 浏览器都是webkit内核，设置progress element 元素样式基于 -webkit-appearance: progress-bar 

设置progress默认样式，一般简单的设置为 -webkit-appearance: none

```
progress[value] {
  /* Reset the default appearance */
  -webkit-appearance: none;
   appearance: none;

  width: 250px;
  height: 20px;
}
```
+ -webkit-progress-bar  用于设置progress容器样式的伪类。在这个范例中，可以设置背景色，圆角，阴影
  
```
  progress[value]::-webkit-progress-bar {
  background-color: #eee;
  border-radius: 2px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.25) inset;
}
```
+ -webkit-progress-value 用于设置progress进度条的样式。
  
```
progress[value]::-webkit-progress-value {
  background-image:
	   -webkit-linear-gradient(-45deg, 
	                           transparent 33%, rgba(0, 0, 0, .1) 33%, 
	                           rgba(0,0, 0, .1) 66%, transparent 66%),
	   -webkit-linear-gradient(top, 
	                           rgba(255, 255, 255, .25), 
	                           rgba(0, 0, 0, .25)),
	   -webkit-linear-gradient(left, #09c, #f44);

    border-radius: 2px; 
    background-size: 35px 20px, 100% 100%, 100% 100%;
}
```
##### 增加动画

对 -webkit-progress-value 属性的背景增加动画
```
@-webkit-keyframes animate-stripes {
   100% { background-position: -100px 0px; }
}

@keyframes animate-stripes {
   100% { background-position: -100px 0px; }
}
```
对 -webkit-progress-value 选择器使用动画
```
-webkit-animation: animate-stripes 5s linear infinite;
        animation: animate-stripes 5s linear infinite;
```
##### 伪元素

使用::before 和 ::after 可以在progress元素上展示进度条的百分比或者准确的数字。

```
progress[value]::-webkit-progress-value::before {
  content: '80%';
  position: absolute;
  right: 0;
  top: -125%;
}

progress[value]::-webkit-progress-value::after {
  content: '';
  width: 6px;
  height: 6px;
  position: absolute;
  border-radius: 100%;
  right: 7px;
  top: 7px;
  background-color: white;
}
```