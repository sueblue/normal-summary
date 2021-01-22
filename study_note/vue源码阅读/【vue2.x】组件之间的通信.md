### 1. 组件之间的通信

 1.1 事件处理模式
  
  +  子组件向父组件通信 => 使用 emit , 触发事件
  +  父组件接收信息 => 使用v-on，监听事件
  +  在一个组件的根元素上直接监听一个原生事件 - [$listeners](https://cn.vuejs.org/v2/guide/components-custom-events.html)
  +   
  
 1.2  属性传递 
  
  + props  父组件使用属性，对子组件通信
  

 1.3 非 Prop 的 [Attribute](https://cn.vuejs.org/v2/guide/components-props.html#%E9%9D%9E-Prop-%E7%9A%84-Attribute)


  +  一个非 prop 的 attribute 是指传向一个组件，但是该组件并没有相应 prop 定义的 attribute
  + 因为显式定义的 prop 适用于向一个子组件传入信息，然而组件库的作者并不总能预见组件会被用于怎样的场景。这也是为什么组件可以接受任意的 attribute，而这些 attribute 会被添加到这个组件的根元素上。
  + 如果你不希望组件的根元素继承 attribute，你可以在组件的选项中设置 inheritAttrs: false。  
  
1.4 访问元素 & 组件
 
  + 访问根实例-可通过 [$root](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E8%AE%BF%E9%97%AE%E6%A0%B9%E5%AE%9E%E4%BE%8B) property 进行访问
  + 访问父级组件实例 - [$parent](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E8%AE%BF%E9%97%AE%E7%88%B6%E7%BA%A7%E7%BB%84%E4%BB%B6%E5%AE%9E%E4%BE%8B) property 可以用来从一个子组件访问父组件的实例
  + 访问子组件实例或子元素 - [ref](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E8%AE%BF%E9%97%AE%E5%AD%90%E7%BB%84%E4%BB%B6%E5%AE%9E%E4%BE%8B%E6%88%96%E5%AD%90%E5%85%83%E7%B4%A0) 这个 attribute 为子组件赋予一个 ID 引用
  
1.5 [依赖注入](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)

  +  把依赖注入看作一部分“大范围有效的 prop”

    +  祖先组件不需要知道哪些后代组件使用它提供的 property
    +  后代组件不需要知道被注入的 property 来自哪里
```
    <div id="app">
      <google-map>
        <google-map-marker v-bind:places="vueConfCities"></google-map-marker>
      </google-map>
    </div>

    <script>
      Vue.component("google-map", {
        provide: function() {
          return {
            getMap: this.getMap
          };
        },
        data: function() {
          return {
            map: null
          };
        },
        mounted: function() {
          this.map = new google.maps.Map(this.$el, {
            center: { lat: 0, lng: 0 },
            zoom: 1
          });
        },
        methods: {
          getMap: function(found) {
            var vm = this;
            function checkForMap() {
              if (vm.map) {
                found(vm.map);
              } else {
                setTimeout(checkForMap, 50);
              }
            }
            checkForMap();
          }
        },
        template: '<div class="map"><slot></slot></div>'
      });

      Vue.component("google-map-marker", {
        inject: ["getMap"],
        props: ["places"],
        created: function() {
          var vm = this;
          vm.getMap(function(map) {
            vm.places.forEach(function(place) {
              new google.maps.Marker({
                position: place.position,
                map: map
              });
            });
          });
        },
        render(h) {
          return null;
        }
      });

      new Vue({
        el: "#app",
        data: {
          vueConfCities: [
            {
              name: "Wrocław",
              position: {
                lat: 51.107885,
                lng: 17.038538
              }
            },
            {
              name: "New Orleans",
              position: {
                lat: 29.951066,
                lng: -90.071532
              }
            }
          ]
        }
      });
    </script>

```
### 2. 组件自由交流-[事件中心](https://cn.vuejs.org/v2/guide/migration.html#dispatch-%E5%92%8C-broadcast-%E6%9B%BF%E6%8D%A2)

+ 通过使用事件中心，允许组件自由交流，无论组件处于组件树的哪一层。由于 Vue 实例实现了一个事件分发接口，你可以通过实例化一个空的 Vue 实例来实现这个目的。

+ Vue 通过事件发射器接口执行实例，实际上你可以使用一个空的 Vue 实例。

```
比如，假设我们有个 todo 的应用结构如下：

Todos
├─ NewTodoInput
└─ Todo
   └─ DeleteTodoButton
可以通过单独的事件中心管理组件间的通信：

// 将在各处使用该事件中心
// 组件通过它来通信
var eventHub = new Vue()
然后在组件中，可以使用 $emit，$on，$off 分别来分发、监听、取消监听事件：

// NewTodoInput
// ...
methods: {
  addTodo: function () {
    eventHub.$emit('add-todo', { text: this.newTodoText })
    this.newTodoText = ''
  }
}
// DeleteTodoButton
// ...
methods: {
  deleteTodo: function (id) {
    eventHub.$emit('delete-todo', id)
  }
}
// Todos
// ...
created: function () {
  eventHub.$on('add-todo', this.addTodo)
  eventHub.$on('delete-todo', this.deleteTodo)
},
// 最好在组件销毁前
// 清除事件监听
beforeDestroy: function () {
  eventHub.$off('add-todo', this.addTodo)
  eventHub.$off('delete-todo', this.deleteTodo)
},
methods: {
  addTodo: function (newTodo) {
    this.todos.push(newTodo)
  },
  deleteTodo: function (todoId) {
    this.todos = this.todos.filter(function (todo) {
      return todo.id !== todoId
    })
  }
}
```
### 3. 复杂情况通信-专用的状态管理层 Vuex。