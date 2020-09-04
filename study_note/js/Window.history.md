### 1.Window.history 是啥？

Window.history 是一个<font color='red'>只读</font>属性，用来获取History 对象的引用，History 对象提供了<font color="red">操作浏览器会话历史(session history)</font>的接口

+ tip:
  
  1. 浏览器的回退和前进按钮旁的下拉菜单显示了可以通过History对象访问到的页面会话历史（session history）列表
  2. **未授权代码无法清除会话历史**（session History），也不能禁用回退/前进功能。最快捷的可用方式是使用location.replace()方法，**提供指定的URL来替换当前的会话历史**（session history）
  3. History 接口
  ```mermaid
   classDiagram
        History-- back
        History-- forward
        History-- go
        History-- pushState
        History--replaceState
        History: +length  readonly
        History: +scrollRestoration
        History: +state readonly
  ```
### 2.History.length

返回一个整数，该整数表示会话历史中元素的数目，**包括当前加载的页**。例如，在一个新的选项卡加载的一个页面中，这个属性返回1。

### 3.[History.state](https://html.spec.whatwg.org/multipage/history.html#serialized-state)
返回一个表示历史<font color='red'>堆栈顶部的状态的值</font>。这是一种可以不必等待popstate 事件而查看状态的方式

页面加载时，或许会有个非null的状态对象。这是有可能发生的，举个例子，假如页面（通过pushState() 或 replaceState() 方法）设置了状态对象而后用户重启了浏览器。那么当页面重新加载时，页面会接收一个onload事件，但没有 popstate 事件。然而，假如你读取了history.state属性，你将会得到如同popstate 被触发时能得到的状态对象。

你可以读取当前历史记录项的状态对象state，而不必等待popstate 事件， 只需要这样使用history.state 属性： 
```
 // 尝试通过 pushState 创建历史条目,然后再刷新页面查看state状态对象变化;
  window.addEventListener('load',() => {
    let currentState = history.state;
    console.log('currentState',currentState);
  })  
```

### 4. History.go 跳转到 history 中指定的一个点

history.go(1) = History.forward()

history.go(-1) = History.back()

history.go(0) = location.reload()

### 5.添加和修改session history中的条目
1. history.pushState

    使用 history.pushState() 可以**改变referrer**，它在用户发送 XMLHttpRequest 请求时在HTTP头部使用，改变state后创建的 XMLHttpRequest 对象的referrer都会被改变。因为referrer是标识创建  XMLHttpRequest 对象时 this 所代表的window对象中document的URL。

+ pushState() 需要三个参数

    pushState() 需要三个参数: 一个状态对象, 一个标题 (目前被忽略), 和 (可选的) 一个URL. 让我们来解释下这三个参数详细内容：

    状态对象 — 状态对象state是一个JavaScript对象，通过pushState () 创建新的历史记录条目。无论什么时候用户导航到新的状态，popstate事件就会被触发，且该事件的state属性包含该历史记录条目状态对象的副本。

            状态对象可以是能被序列化的任何东西。原因在于Firefox将状态对象保存在用户的磁盘上，以便在用户重启浏览器时使用，我们规定了状态对象在序列化表示后有640k的大小限制。如果你给 pushState() 方法传了一个序列化后大于640k的状态对象，该方法会抛出异常。如果你需要更大的空间，建议使用 sessionStorage 以及 localStorage.

    标题 — Firefox 目前忽略这个参数，但未来可能会用到。在此处传一个空字符串应该可以安全的防范未来这个方法的更改。或者，你可以为跳转的state传递一个短标题。

    URL — 该参数定义了新的历史URL记录。注意，调用 pushState() 后浏览器并不会立即加载这个URL，但可能会在稍后某些情况下加载这个URL，比如在用户重新打开浏览器时。新URL不必须为绝对路径。如果新URL是相对路径，那么它将被作为相对于当前URL处理。新URL必须与当前URL同源，否则 pushState() 会抛出一个异常。该参数是可选的，缺省为当前URL。

+ pushState() 具有如下几条优点

    在某种意义上，调用 pushState() 与 设置 window.location = "#foo" 类似，二者都会在当前页面创建并激活新的历史记录。但 pushState() 具有如下几条优点：

    新的 URL 可以是与当前URL同源的任意URL 。相反，只有在修改哈希时，设置 window.location 才能是同一个 document。
    如果你不想改URL，就不用改。相反，设置 window.location = "#foo";在当前哈希不是 #foo 时， 才能创建新的历史记录项。
    你可以将任意数据和新的历史记录项相关联。而基于哈希的方式，要把所有相关数据编码为短字符串。 
    如果 标题 随后还会被浏览器所用到，那么这个数据是可以被使用的（哈希则不是）。
    注意 pushState() 绝对不会触发 hashchange 事件，即使新的URL与旧的URL仅哈希不同也是如此。

2. replaceState() 方法
   
   replaceState()  是修改了当前的历史记录项而不是新建一个。 注意这并不会阻止其在全局浏览器历史记录中创建一个新的历史记录项。

   replaceState() 的使用场景在于为了响应用户操作，你想要更新状态对象state或者当前历史记录的URL。

[MDN文档链接-部分摘要](https://developer.mozilla.org/zh-CN/docs/Web/API/History_API)

#### 小结：

replaceState 是替换当前的历史记录，back之后forward就是替换页

pushState  是在当前的历史记录插入一个记录，forward之后back就是插入记录页

其中state对象可以作为一个锚点，放入任何东西，监听load事件可以做一下取参或跳转指定

history.state始终都是堆栈顶部的值，表示当前的state