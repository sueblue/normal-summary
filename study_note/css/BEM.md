### 1. 什么是BEM?
+ Block

    将所有东西都划分为一个独立的模块,

    一个header是block,

    header里嵌套的搜索框是block

+ Element

    !误区:如果一个Element-son是另一个Element-father的子元素,那么写法是 Block__Element-father__Element-son_Modifer,嵌套多了会很长么?不是的!!!
    
    一个Block下的所有Element无论相互层级如何,都要摊开扁平的属于Block所以 BEM 最多只有 B+E+M 三级,不可能出现 B+E+E+..+E+M 超长class名,也要求E不能同名

+ Modifier

    经常写的 .current .active 等表达状态

### 2. BEM的优点与缺点？
+ 优点
 
  - 代码复用    
  - 结构简单，不会让他人进行多余的思考
  - 避免父级元素内的标签的受影响，举个例子,商品详情页是允许商家自定义标签的,那么商家展示区域标签的祖先元素一旦用标签选择器定义了样式,子子孙孙都要背负.
  
例如，将这个网页拆分成BEM的写法  

无BEM写法：  
```
<section>
    <h1>Sterling Calculator</h1>
    <form action="process.php" method="post">
        <p>Please enter an amount: (e.g. 92p, &pound;2.12)</p>
        <p>
            <input name="amount"> 
            <input type="submit" value="Calculate">
        </p>
    </form>
</section>
```
BEM 写法：
```
<section class="widget">
    <h1 class="widget__header">Sterling Calculator</h1>
    <form class="widget__form" action="process.php" method="post">
        <p>Please enter an amount: (e.g. 92p, &pound;2.12)</p>
        <p>
            <input name="amount" class="widget__input widget__input--amount"> 
            <input type="submit" value="Calculate" class="widget__input widget__input--submit">
        </p>
    </form>
</section>
```
元素清单：

+ widget
+ widget__header
+ widget__form
+ widget__input

这样就形成了一个可复用的块

注意其中的 **widget__input--amount** 和 **widget__input--submit**

这就是Modifier，表示元素当时的状态

+ 缺点
  - 类名变的更长
  - Block的抽象至关重要
 
### 3. 谁适用于BEM
 项目复杂，复用模块较多,多人协作团队使用

### 4. BEM命名规范

一般的BEM命名方式：
```
block-name_modifier-name

block-name__element-name_modifier-name

```

用它的思想，不一定要用它的命名方式。改成这样更简洁些
            
    .blockName-elName.modifier

.modifier 不单独使用，所以也不会冲突

例1：
```
.menu
.menu-item
.menu-item.active
```


### 摘取文章
+ https://en.bem.info/methodology/quick-start/
+ http://getbem.com/introduction/
+ https://www.zhihu.com/question/21935157
+ https://www.integralist.co.uk/posts/bem/#4
+ https://www.smashingmagazine.com/2016/06/battling-bem-extended-edition-common-problems-and-how-to-avoid-them/
+ https://docs.emmet.io/filters/bem/
+ https://github.com/Tencent/tmt-workflow/wiki/%E2%92%9B-%5B%E8%A7%84%E8%8C%83%5D--CSS-BEM-%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83

### 题外话：关于[css Modules](https://github.com/css-modules/css-modules) 

css Modules 不是将 CSS 改造成编程语言，而是功能很单纯，只加入了局部作用域和模块依赖

参考文章：
 + http://www.ruanyifeng.com/blog/2016/06/css_modules.html
 + https://blog.csdn.net/qq_26733915/article/details/54313492