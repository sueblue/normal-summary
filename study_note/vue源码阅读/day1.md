### 目录设计 ([电子版书籍](https://ustbhuangyi.github.io/vue-analysis/v2/prepare/directory.html#compiler))
```
src
├── compiler        # 编译相关 
├── core            # 核心代码 
├── platforms       # 不同平台的支持
├── server          # 服务端渲染
├── sfc             # .vue 文件解析
├── shared          # 共享代码
```

目录设计可以看到，作者把功能模块拆分的非常清楚，相关的逻辑放在一个独立的目录下维护，并且把复用的代码也抽成一个独立目录.

目录设计让代码的阅读性和可维护性都变强，是非常值得学习和推敲的。

####  个人note:

```
├── shared          # 共享代码
```
constants.js 文件中把钩子和常用的都抽出来了
```
export const SSR_ATTR = 'data-server-rendered'

export const ASSET_TYPES = [
  'component',
  'directive',
  'filter'
]

export const LIFECYCLE_HOOKS = [
  'beforeCreate',
  'created',
  'beforeMount',
  'mounted',
  'beforeUpdate',
  'updated',
  'beforeDestroy',
  'destroyed',
  'activated',
  'deactivated',
  'errorCaptured',
  'serverPrefetch'
]

```
#### Q: why-通常我们更推荐使用 Runtime-Only 的 Vue.js

因为在 Vue.js 2.0 中，最终渲染都是通过 render 函数，如果写 template 属性，

则需要编译成 render 函数，那么这个编译过程会发生运行时，所以需要带有编译器的版本。

```
// 需要编译器的版本
new Vue({
  template: '<div>{{ hi }}</div>'
})

// 这种情况不需要
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})

```
#### Q.vuejs的打包构建过程是怎样的？

  1. 构建脚本 package.json  
  2. 对package.json文件的认识
   
### Q.VUE的真面目是什么？[代码片段](src/core/instance/index.js)

  vue实际上就是一个function。
  1. 为啥用new Vue去实例化它呢？
  有很多 xxxMixin 的函数调用，并把 Vue 当参数传入，它们的功能都是给 Vue 的 prototype 上扩展一些方法，
  Vue 按功能把这些扩展分散到多个模块中去实现，而不是在一个模块里实现所有，这种方式是用 Class 难以实现的。这么做的好处是非常方便代码的维护和管理



