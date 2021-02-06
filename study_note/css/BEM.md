### 1. 什么是BEM?

BEM（Block，Element，Modifier）是一种基于组件的web开发思想。这种开发思想主张将用户界面划分为独立的块。这使得网页开发变得简单快捷，即使是复杂的用户界面，也可以重用现有的代码，而无需复制和粘贴。

+ Block - 一个功能独立的页面组件，可以重用。

   - block名称描述了此模块的用途，它是什么？

   - 各个模块可以相互嵌套，嵌套层级数量不受限

   例如：
    ```
    <!-- `header` block -->
    <header class="header">
        <!-- Nested `logo` block -->
        <div class="logo"></div>

        <!-- Nested `search-form` block -->
        <form class="search-form"></form>
    </header>
    ```
    header 模块嵌套了logo模块和搜索表单模块

+ Element - 块的组成模块，不能与块分开使用，也不能自己单独使用。

    - element名称描述了在这模块中的用途，它是什么？
    
    - element名称的语法结构为  **block-name__element-name**，使用双下划线将block名称和element名称连接起来。
    - element 元素彼此之间可以相互嵌套，嵌套层级数量不受限
    - 一个element元素里可以嵌套包含一个block块，这就意味着，element名称定义不能为多层级结构，如 block__elem1__elem2 ，这种是不被允许的。

    例如：
    ```
    <form class="search-form">
        <div class="search-form__content">
            <input class="search-form__input">

            <button class="search-form__button">Search</button>
        </div>
    </form>
    ```
    search-form__input,search-form__button,search-form__content 即为element元素。

    search-form__content 元素中嵌套了element元素。

    但是search-form__content__input 这种多层级命名element元素是不被允许的，类名过长，层级结构过多，不清晰。

+ Modifier - 定义block块或element元素的外观、状态或行为。

    - modifier 的名称一般描述了block块或element元素的外观，它的大小？它的状态？它的颜色？

    - modifier 的名称语法结构为

        block-name_modifier-name

        block-name__element-name_modifier-name

        一般使用单下划线将它跟block元素或者element元素连接起来

    - 布尔形式，区分是或不是的状态，完整的语法结构为：

        block-name_modifier-name

        block-name__element-name_modifier-name

    例如：
    ```
    <form class="search-form search-form_theme_islands">
        <input class="search-form__input">

        <!-- The `button` element has the `size` modifier with the value `m` -->
        <button class="search-form__button search-form__button_disabled">Search</button>
    </form>
    ```
    search-form__button_disabled 这种命名结构是布尔形式

    - key-value键值对的形式，区分不同的状态。完整的语法结构则为：

        block-name_modifier-name_modifier-value

        block-name__element-name_modifier-name_modifier-value

    例如：
    ```
    <form class="search-form search-form_theme_islands">
        <input class="search-form__input">

        <!-- The `button` element has the `size` modifier with the value `m` -->
        <button class="search-form__button search-form__button_size_m">Search</button>
    </form>
    ```
    search-form__button_size_m 这种命名结构就是键值对的形式
    
    - modifier 不能被单独使用，必须与block元素或者element元素联合使用。因为一个modifier就是用来描述此元素的外观、大小、一个实体的状态。
### 2. BEM的优点与缺点？
+ 优点
 
  - 结构简单，一目了然
  - 组件化，代码复用    
  - 不使用标签选择器，避免父级元素内的标签的受影响。举个例子,商品详情页是允许商家自定义标签的,那么商家展示区域标签的祖先元素一旦用标签选择器定义了样式,子子孙孙都要背负.
  
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
            <input name="amount" class="widget__input widget__input-amount"> 
            <input type="submit" value="Calculate" class="widget__input widget__input-submit">
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

注意其中的 **widget__input-amount** 和 **widget__input-submit**为Modifier

+ 缺点
  - 类名变的更长，一个元素可能拥有多个class
  - id选择器无用武之地
  - class命名不能重复
  - Block的抽象至关重要
 
### 3. 谁适用于BEM
 项目复杂，复用模块较多,多人协作团队使用


### 4. 题外话：关于[css Modules](https://github.com/css-modules/css-modules) 

css Modules 不是将 CSS 改造成编程语言，功能很单纯，只加入了局部作用域和模块依赖

参考文章：
 + http://www.ruanyifeng.com/blog/2016/06/css_modules.html
 + https://blog.csdn.net/qq_26733915/article/details/54313492

### 摘取文章
+ https://en.bem.info/methodology/quick-start/
+ http://getbem.com/introduction/
+ https://www.zhihu.com/question/21935157
+ https://www.integralist.co.uk/posts/bem/#4
+ https://www.smashingmagazine.com/2016/06/battling-bem-extended-edition-common-problems-and-how-to-avoid-them/
+ https://docs.emmet.io/filters/bem/
+ https://github.com/Tencent/tmt-workflow/wiki/%E2%92%9B-%5B%E8%A7%84%E8%8C%83%5D--CSS-BEM-%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83

