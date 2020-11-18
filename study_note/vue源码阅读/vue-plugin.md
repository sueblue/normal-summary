首先，一点小内容要讲：

插件对于vue来说不是特有的，jq也有，你会发现有大量不同的插件做不同的事情。

下一个定义就是：它们都提供一个接口去做扩展。

 + tips:插件其实就是一种全局方法插入到app中进行扩展，以便自己的使用。

[vue的插件文档](https://vuejs.org/v2/guide/plugins.html)尽可能的详细的描述了插件并且提供了关于插件的常用类别：

+ 添加一些全局方法或者属性
+ 添加一个或者多个全局资源：directives/filters/transitions等等
+ 利用全局混入(mixin)添加一些组件选项
+ 增加一些vue实例方法，通过Vue.prototype去进行附加
+ 提供一个自己的所需的api库，同时可以注入以上的组合
+ 我们做什么
  
## 简单实战  

目标：做一个评论组件，可以让用户在任何地方点击并且留下评论。

#### **第一步，准备代码库**

vue插件包含安装方法，只需要两个参数即可。

 1. 一个全局vue变量

 2. 一个包含用户自定义选项的对象

新建一个vue项目其实非常简单，感谢vue-cli3 
 
 使用以下命令即可：
```
$ vue create vue-comments-overlay
# Answer the few questions
$ cd vue-comments-overlay
$ npm run serve
```
一个经典的hello-world项目就启动了，我们需要一个简单的test app去测试我们的插件。

#### **第二步，创建插件目录**

我们的插件必须存在于某个目录中，所以我们创建一个目录去写插件，然后定位到新目录。

```
$ mkdir src/plugins
$ mkdir src/plugins/CommentsOverlay
$ cd src/plugins/CommentsOverlay
```
#### **第三步，测试一下基本流程**

一个vue插件是基于一个带有install function的对象，这个function可以被执行，而app则使用Vue.use() 去装载此对象。

这个install function接受一个全局vue对象和自定义选项对象作为参数。
```
// src/plugins/CommentsOverlay/index.js
// 
export default {
  install(vue, opts){   
    console.log('Installing the CommentsOverlay plugin!')
    // Fun will happen here
  }
}
```
 现在，让我们的testapp使用此插件。
```
// src/main.js
import Vue from 'vue'
import App from './App.vue'
import CommentsOverlay from './plugins/CommentsOverlay' // import the plugin

Vue.use(CommentsOverlay) // put the plugin to use!

Vue.config.productionTip = false

new Vue({ render: createElement => createElement(App)}).$mount('#app')
```
#### **第四步，提供options支持**

我们想要此插件是可配置。这可以让任何人在他们中的app中进行调整，让我们的插件更加通用。

我们使用options作为install function的第二个参数。可以创建一个默认的options来指定插件的表现情况，以下代码展示默认options
```
// src/plugins/CommentsOverlay/index.js

const optionsDefaults = {
  // Retrieves the current logged in user that is posting a comment
  commenterSelector() {
    return {
      id: null,
      fullName: 'Anonymous',
      initials: '--',
      email: null
    }
  },
  data: {
    // Hash object of all elements that can be commented on
    targets: {},
    onCreate(created) {
      this.targets[created.targetId].comments.push(created)
    },
    onEdit(editted) {
      // This is obviously not necessary
      // It's there to illustrate what could be done in the callback of a remote call
      let comments = this.targets[editted.targetId].comments
      comments.splice(comments.indexOf(editted), 1, editted);
    },
    onRemove(removed) {
      let comments = this.targets[removed.targetId].comments
      comments.splice(comments.indexOf(removed), 1);
    }
  }
}
```
然后，我们可以合并options到install 函数中设置默认：
```
// src/plugins/CommentsOverlay/index.js

export default {
  install(vue, opts){
    // Merge options argument into options defaults
    const options = { ...optionsDefaults, ...opts }
    // ...
  }
}
```
#### **第五步，为评论插件创建一个实例**

作为一个插件要避免的事情就是，它的dom和样式不能够干扰到所装载它的app。为了能够最小化这种情况的发生，一种方式就是让这个插件活在另一个根vue实例当中，在主app的组件树之外。

在install 函数中加入以下：
```
// src/plugins/CommentsOverlay/index.js

export default {
  install(vue, opts){
    ...
  // Create plugin's root Vue instance
      const root = new Vue({
        data: { targets: options.data.targets },
        render: createElement => createElement(CommentsRootContainer)
      })

      // Mount root Vue instance on new div element added to body
      root.$mount(document.body.appendChild(document.createElement('div')))

      // Register data mutation handlers on root instance
      root.$on('create', options.data.onCreate)
      root.$on('edit', options.data.onEdit)
      root.$on('remove', options.data.onRemove)

      // Make the root instance available in all components
      vue.prototype.$commentsOverlay = root
      ...
  }
}
```
 以上代码的基本要点：

     1.这个插件的存在于body的新div层里

     2.在options对象中定义的事件处理会被挂接到根实例上的匹配事件

     3.  $commentsOverlay 属性将会增加到Vue原型上，而Vue原型对于app来说暴露出来根实例并公开所有组件

#### **第六步，创建自定义指令**

最后，对于app来说，我们需要一种方式去利用这个插件，去告诉插件哪个节点需要使用此插件功能。这种情况就适合于使用自定义vue directive  。因为这个组件是全局Vue 对象，那我们可以定义一个新的directives。

我们命名为  comments-enabled  ，以下代码定义此指令：
```
// src/plugins/CommentsOverlay/index.js

export default {
  install(vue, opts){

    ...

    // Register custom directive tha enables commenting on any element
    vue.directive('comments-enabled', {
      bind(el, binding) {

        // Add this target entry in root instance's data
        root.$set(
          root.targets,
          binding.value,
          {
            id: binding.value,
            comments: [],
            getRect: () => el.getBoundingClientRect(),
          });

        el.addEventListener('click', (evt) => {
          root.$emit(`commentTargetClicked__${binding.value}`, {
            id: uuid(),
            commenter: options.commenterSelector(),
            clientX: evt.clientX,
            clientY: evt.clientY
          })
        })
      }
    })
  }
}
```
这个指令只做两件事：

      1.将此target增加到根实例的data上。这个关键就是定义一个绑定值。 它可以因目标元素去区分它们各自的id。就像这样：
```
<img v-comments-enabled="imgFromDb.id" src="imgFromDb.src" />
```

      2.在目标元素上注册一个click事件，继而，在目标元素的根元素上emit 一个事件

这个install 函数现在已经完成了，现在我们使用一下这个评论功能并且进行组件渲染。

#### **第七步，创建一个评论层组件**

我们去创建一个 CommentsRootContainer 并且利用它到根组件使用这个。
```
<!-- 
 src/plugins/CommentsOverlay/CommentsRootContainer.vue -->

<template>
  <div>
    <comments-overlay
        v-for="target in targets"
        :target="target"
        :key="target.id">
    </comments-overlay>
  </div>
</template>

<script>
import CommentsOverlay from "./CommentsOverlay";

export default {
  components: { CommentsOverlay },
  computed: {
    targets() {
      return this.$root.targets;
    }
  }
};
</script>
```
 这在做什么？我们创建了一个wrapper去包裹住这个
还未做好的CommentsOverlay组件。你可以看到这个组件内部接收的 target 和 target .id 。注意 target 计算属性是来源于根组件的 target 数据。

现在，Overlay组件就是魔法发生的地方。我们开始吧！


#### **第八步，制作 Comments Overlay组件来创造魔法**

现在我抛出了一大串代码，但是我们会一步一步解读它。

```
<!--  src/plugins/CommentsOverlay/CommentsRootContainer.vue -->

<template>
  <div class="comments-overlay">

    <div class="comments-overlay__container" v-for="comment in target.comments" :key="comment.id" :style="getCommentPostition(comment)">
      <button class="comments-overlay__indicator" v-if="editing != comment" @click="onIndicatorClick(comment)">
        {{ comment.commenter.initials }}
      </button>
      <div v-else class="comments-overlay__form">
        <p>{{ getCommentMetaString(comment) }}</p>
        <textarea ref="text" v-model="text" />        
        <button @click="edit" :disabled="!text">Save</button>
        <button @click="cancel">Cancel</button>
        <button @click="remove">Remove</button>
      </div>
    </div>

    <div class="comments-overlay__form" v-if="this.creating" :style="getCommentPostition(this.creating)">
      <textarea ref="text" v-model="text" />
      <button @click="create" :disabled="!text">Save</button>
      <button @click="cancel">Cancel</button>
    </div>

  </div>
</template>

<script>
export default {
  props: ['target'],

  data() {
    return {
      text: null,
      editing: null,
      creating: null
    };
  },

  methods: {
    onTargetClick(payload) {
      this._resetState();
      const rect = this.target.getRect();

      this.creating = {
        id: payload.id,
        targetId: this.target.id,
        commenter: payload.commenter,
        ratioX: (payload.clientX - rect.left) / rect.width,
        ratioY: (payload.clientY - rect.top) / rect.height
      };
    },
    onIndicatorClick(comment) {
      this._resetState();
      this.text = comment.text;
      this.editing = comment;
    },
    getCommentPostition(comment) {
      const rect = this.target.getRect();
      const x = comment.ratioX  <em> rect.width + rect.left;
      const y = comment.ratioY  <em> rect.height + rect.top;
      return { left: `${x}px`>, top: `${y}px` };
    },
    getCommentMetaString(comment) {
      return `${
        comment.commenter.fullName
      } - ${comment.timestamp.getMonth()}/${comment.timestamp.getDate()}/${comment.timestamp.getFullYear()}`;
    },
    edit() {
      this.editing.text = this.text;
      this.editing.timestamp = new Date();
      this._emit("edit", this.editing);
      this._resetState();
    },
    create() {
      this.creating.text = this.text;
      this.creating.timestamp = new Date();
      this._emit("create", this.creating);
      this._resetState();
    },
    cancel() {
      this._resetState();
    },
    remove() {
      this._emit("remove", this.editing);
      this._resetState();
    },
    _emit(evt, data) {
      this.$root.$emit(evt, data);
    },
    _resetState() {
      this.text = null;
      this.editing = null;
      this.creating = null;
    }
  },

  mounted() {
    this.$root.$on(`commentTargetClicked__${this.target.id}`, this.onTargetClick
    );
  },

  beforeDestroy() {
    this.$root.$off(`commentTargetClicked__${this.target.id}`, this.onTargetClick
    );
  }
};
</script>
```
我知道，有点吓人。但是它只做一些基本的事情。

首先，在 template 标签内建立一个评论 popover ，它会显示在屏幕上，有个表单以供提交评论。换句话说，html 内渲染出 我们的评论

接下来，我们编写脚本去支持我们的评论行为。此组件定义了target 属性，接受一个完整的target对象作为参数。它存储了comments数组和位置信息。

然后，魔法时刻。我们定义一些方法，在触发时执行一些重要操作：

+ 监听click
+ 单击的时候，在单击处渲染出评论的box
+ 存储用户提交的数据，包括姓名和评论
+ 功能有创建、修改、移除、取消评论

在 mounted 和 beforeDestroy 中进行commentTargetClicked 事件的监听和销毁。

这里值得注意的是，root 实例被作为事件总线。即使这种做法是不被提倡的，但是我认为在这里是合理的，因为此组件没有公开内部，可以看做一个整体。

#### **认识更多vue插件**

我们花了大章的篇幅来描述创建一个vue插件，其原因是想让人了解到如何使用插件的这么一个过程。整理了一些非常流行的vue插件列表，以便你随时访问。

+ [Vue-router](https://github.com/vuejs/vue-router)如果正在构建单页面应用程序，毫无疑问需要Vue-router。官方路由，高度集成，例如路由嵌套和组件映射。
+ [Vuex](https://github.com/vuejs/vuex) 创建复杂大型应用必备，以集中式的存储可向所有组件服务。

+ [vee-validate](https://github.com/baianat/vee-validate) 构建典型业务应用必备，一旦处理不当，表单验证就是个麻烦。vee-validate以一种优雅的方式来处理。它使用directives，并且构建的时候还考虑了本地化。


[demo例子 github](https://github.com/snipcart/vue-comments-overlay)

原文：https://css-tricks.com/getting-started-with-vue-plugins/