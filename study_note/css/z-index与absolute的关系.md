不使用 position这种情况下每一层都遵循 HTML 定位规则,其中的 left,right,top,bottom 定位信息对其无效,z-index 也不会发挥作用

1. 使用 absolute

**如两个绝对定位对象的 z-index 属性具有同样的值,那么将依据它们在HTML文档中声明的顺序层叠.**

1.1 未使用 z-index

这种情况下,依据它们在HTML文档中声明的顺序层叠,因为 z-index 在未设置的情况下,默认为 0 .


1.2 使用 z-index

**z-index 为无单位的整数值,可为负数.按照数值的大小排列,数值越大,越在外层.**

1. 混合使用 absolute

**对于没有设置 position:absolute 属性的元素 不管 z-index 设置多少都为 0, 但低于 position:absolute 中 z-index:0 的元素**