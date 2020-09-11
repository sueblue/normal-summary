## 1. [Web Storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)

Storage 提供了访问<font color='red'>特定域名</font>下的会话存储或本地存储的功能，例如，可以[添加、修改或删除](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage)存储的数据项。

存储对象是简单的键值存储，类似于对象，但是它们在页面加载时保持完整。<font color='red'>键和值始终是字符串</font>（请注意，与对象一样，整数键将自动转换为字符串）

### 2. Web Storage 包含如下两种机制：

+ sessionStorage 为<font color='red'>每一个给定的源（given origin）维持一个独立的存储区域</font>，该存储区域在页面会话期间可用（即只要浏览器处于打开状态，包括页面重新加载和恢复）。
+ localStorage 同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。

<font color='red'>同一域名下的localStorage,sessionStorage是共享的</font>

#### 2.1 Window.sessionStorage

+ 存储在 sessionStorage 里面的数据在页面会话结束时会被清除。
+ 页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话
+ <font color='red'>打开多个相同的URL的Tabs页面，会创建各自的sessionStorage。</font>
+ 关闭对应浏览器tab，会清除对应的sessionStorage。

#### 2.2 Window.localStorage

+ 只读的localStorage 属性允许你访问一个Document 源（origin）的对象 Storage
+ 存储在 localStorage 的数据可以长期保留
  
