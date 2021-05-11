javasrcipt组成一般是由三部分组成的。
ECMAScript、BOM、DOM

###  1. ECMAScript -提供核心语言功能
与web浏览器没有任何的依赖关系，
只是这门语言的基础，
对实现该标准规定的各个方面内容的语言描述。
 + 语法
 + 类型
 + 语句
 + 关键字
 + 保留字
 + 操作符
 + 对象

 ### 2. 浏览器对象模型BOM（browser　Object　Model）- 提供与浏览器交互的方法与接口
 BOM　只处理浏览器窗口和框架，习惯上也把针对浏览器所有的JavaScript扩展算作BOM的一部分。
 + 弹出新浏览器窗口的功能
 + 移动、缩放和关闭浏览器窗口的功能
 + 提供浏览器所加载页面的详细信息的navigator对象
 + 提供浏览器所加载页面的详细信息的location对象
 + 提供用户显示器分辨率详细信息的screen对象
 + 对cookies的支持
 + 像XMLHttpRequest和IE的ActiveXObject这样的自定义对象
 + window对象
 
 虽然没有相关的标准，但是HTML5致力于把很多BOM功能写入正式规范
 
 ### 3.文档对象模型（Document Object Model）- 提供访问和操作网页内容的方法和接口　
 是针对XML但经过扩展用于HTML的应用程序编程接口。DOM把整个页面映射为一个多层节点结构。HTML或者XML页面中的每个组成部分都是某种类型的节点。
 + DOM视图：定义了跟踪不同文档
 + DOM事件：定义了事件和事件处理的接口
 + DOM样式：定义了基于CSS元素应用样式的接口
 + DOM遍历和范围：定义了遍历和操作文档树的接口