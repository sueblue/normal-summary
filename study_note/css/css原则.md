### 1.尽量减少代码重复
灵活的 CSS 通常更容易扩展： 在写出基础样式之后，只用极少的代码就可以扩展出不同的变体，因为只需覆盖一些变量就可以

### 2.善用继承
inherit 可以用在任何 CSS 属性中，而且它总是绑定到父元素的计 算值（对伪元素来说，则会取生成该伪元素的宿主元素）。举例来说，要把 表单元素的字体设定为与页面的其他部分相同，你并不需要重复指定字体属 性，只需利用 inherit 的特性即可

### 3.避免不必要的媒体查询
    1 使用百分比长度来取代固定长度。如果实在做不到这一点，也应该 尝试使用与视口相关的单位（vw、vh、vmin 和 vmax），它们的值解 析为视口宽度或高度的百分比。 

    2.当你需要在较大分辨率下得到固定宽度时，使用 max-width 而不是 width，因为它可以适应较小的分辨率，而无需使用媒体查询。 

    3.不要忘记为替换元素（比如 img、object、video、iframe 等）设 置一个 max-width，值为 100%。 假如背景图片需要完整地铺满一个容器，不管容器的尺寸如何变化， background-size: cover 这个属性都可以做到。但是，我们也要时 刻牢记——带宽并不是无限的，因此在移动网页中通过 CSS 把一张大图缩小显示往往是不太明智的。 

    4.当图片（或其他元素）以行列式进行布局时，让视口的宽度来决定 列的数量。弹性盒布局（即 Flexbox）或者 display: inline-block 加上常规的文本折行行为，都可以实现这一点。  在使用多列文本时，指定 column-width（列宽）而不是指定 column-count（列数），这样它就可以在较小的屏幕上自动显示为单列布局。

