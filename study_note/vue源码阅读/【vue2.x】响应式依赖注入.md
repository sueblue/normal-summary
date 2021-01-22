### [响应式依赖注入](https://blog.logrocket.com/how-to-make-provide-inject-reactive/)

在vue框架的设计中，依赖注入并不是响应式的。即使是有很多人包括我自己也希望它是。

可以使用data()方法或者Vue.observable创建一个响应式对象，这样在使用依赖注入的时候也是响应式的。

#### 1. 使用 data() 方法创建一个依赖注入的响应式对象

```
export default {
  provide() {
    return {
    reactive: this.reactive
    };
  },

  data() {
    return {
    reactive: {
        value: "Hello there"
    }
    };
  }
};
```

现在，当reactive被注入到其他组件的时候，它将被表现成为一种属性，并且reactive进行改变的时候，在你的vue应用中将会触发updates。

#### 2. 使用Vue.observable

[vue 2.6](https://github.com/vuejs/vue/releases/tag/v2.6.0)介绍了observable方法，让我们创建自己的响应式对象。

``` 
import Vue from 'vue';
const state = Vue.observable({ message: "Hello!" });
```

事实上，这就是一样的方法。只是在vue内部中使用了data()方法而已。但是这样可以显式的暴露给我们，以便我们随时随地的使用它。

在Vue3中，这个方法被重新命名为reactive,[Composition API](https://github.com/vuejs/rfcs/pull/78)有介绍。

让我们使用Vue.observable来重写先前的例子:

```
import Vue from 'vue';

const reactive = Vue.observable({ value: 'Hello there' });

export default {
  provide() {
    return {
    reactive: reactive,
    };
  },
};
```
这个方法给了我们很大的灵活性让我们去创建响应式对象，因为我们不在依赖data()方法去创建。

#### 3. 使用插件[vue-reactive-provide](https://github.com/LinusBorg/vue-reactive-provide)

有两种方式供你使用此插件进行响应式依赖注入对象的创建

+ 装载插件
  
  ```
    import Vue from 'vue';
    import ReactiveProvide from 'vue-reactive-provide';

    Vue.use(ReactiveProvide);
  ```
假如使用了脚手架，应该在main.js文件使用。

在组件中，你想注入一个值，你应该使用reactiveProvide 去创建:

```
export default {
  reactiveProvide: {
    name: 'injectedName',
    include: ['reactive'],
  }

  data() {
    return {
    reactive: 'hello',
    };
  },
};

```

你应该给你的响应式对象命名即name值不能为空，这样我们就知道是哪个值被注入到后代中。使用include 数组，可以放置一系列的注入对象。


接受注入对象到组件中，直接设置命名的name即可。
```
export default {
  inject: ['injectedName']
};

```

+ Mixin 方式

```
import { ReactiveProvideMixin } from 'vue-reactive-provide'

export default {
  mixins: [
    ReactiveProvideMixin({
    name: 'injectedName',
    include: ['reactive'],
    })
  ],

  data() {
    return {
    reactive: 'hello',
    };
  },
};

```
### 什么时候去使用响应式的依赖注入

在一般的开发中，你都应该避免使用依赖注入，使用props 和 events 去传递数据。
这样就避免了必要的复杂性，保持代码的简洁，更容易被理解。

然而，在一些特殊的情况下，当使用依赖注入的时候，你应该问自己是否满足以下条件：

1. 你需要避免prop的向下传递吗？
2. 组件是否紧密耦合？
3. 使用vuex是否开销太过？
4. 只在少许组件中包含data？