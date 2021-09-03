# Vuex

## 介绍

Vuex是Vue.js中的一个**状态管理模式+库**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。用简单的话语来说Vuex就是用来统一存储并管理项目中的一些数据（配置信息、数据、用户状态等），而其他的Vue组件来直接调用里面的数据即可。

## 为什么需要Vuex

一个简单的 Vue 计数组件：

```vue
const Counter = {
  // 状态
  data () {
    return {
      count: 0
    }
  },
  // 视图
  template: `
    <div>{{ count }}</div>
  `,
  // 操作
  methods: {
    increment () {
      this.count++
    }
  }
}

createApp(Counter).mount('#app')
```

这个组件包含以下几个部分：

- **状态**，驱动应用的数据源；
- **视图**，以声明方式将**状态**映射到视图；
- **操作**，响应在**视图**上的用户输入导致的状态变化。

![](https://next.vuex.vuejs.org/flow.png)

但当我们需要将其中的 状态(State)传递到其他模块呢？

普通情况下，我们都会将数据传递通过computed来传递到Prors属性去，然后再传递Prors属性。

这种方法可能对于一二个层级的组件传递来说比较简单，但对于跨多个层级，甚至跨分支来传递数据呢？

所以这时Vue需要一个管理Vue中的数据的专门的工具来解决数据传递，并且还必须要是支持响应式的，否则就会出现数据内容不统一的情况。所有Vuex就是为Vue中的状态管理模式+库。

## 入门了解

每一个 Vuex 应用的核心就是**store（仓库）**。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。

Vuex 和单纯的全局对象有以下两点不同：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 你不能通过组件调用来直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

首先在项目中导入Vuex4模块（使用NPM、Yarn都可以）：

```bash
yarn add vuex@next --save
```

当然也可以使用CDN引用\直接下载:

Vuex的CDN地址:https://unpkg.com/vuex@4



我们通常下会在项目src中新建一个store文件夹,用来表示仓库(Store),在文件夹下面创建一个index.js/index.ts (取决于Vue项目使用的语言)

```tsx
import { createStore } from "vuex";

export default createStore({
  state: {
  },
  mutations: {
  },
  actions: {},
  modules: {}
});  
```

除外还需要在main.js/main.ts中加载使用store，这样才让Vue项目全局使用store仓库。

```tsx
...
import store from './store'
...
createApp(App).use(store)
```

这样下来就构建起了一个Vuex模块框架了。下面我们来讲解下Store中的主要含义以及用法。

## 核心概念

### State

上面的store模块中createStore函数中拥有一个state元素。state内部定义的就是我们需要存储数据的数据。

Vuex 使用**单一状态树**——用一个对象就包含了全部的应用层级状态。这也意味着，每个应用将仅仅包含一个 store 实例。

由于是内部定义的是需要存储的数据，所以存储在 Vuex 中的数据和 Vue 实例中的 `data` 遵循相同的规则，例如状态对象必须是纯粹 (plain) 的。

```js
state () {
    return {
      count: 0
    }
},
```

当然如果使用TS语言的话则可以指定定义其元素类型。

```tsx
state: {
    todos: [] as string[],
    dones: [] as string[],
    deletes: [] as string[]
 },
```

对于其中的Store存储数据，我们在Vue模块中需要使用读写Store来读取，由于Vuex的状态存储是响应式的，我们需要使用computed来读取。

```js
import store from "../store";
...
computed: {
    count () {
      return store.state.count
    }
}
```

当然这时vue2.x的computed读取方法，对于Vue3中响应式API则在setup中使用Es6的写法：

```tsx
import store from "../store";

setup() {
    ...
	return reactive({
      	todos: computed(() => store.state.deletes)
	});
}    
```

由于Vuex存储是响应式的，所以每当 `store.state` 中的值变化的时候, 都会重新求取计算属性，并且触发更新相关联的 DOM。

如果学习过Vue3的人，就会知道Vue3中提供了一个`Provide / Inject`的机制，用Provide来全局提供元素，用Inject来提取对于元素。从这一点来说Vuex和`Provide / Inject`是相似的，甚至可以替换使用，但是`Provide / Inject`存储元素默认不是响应式，需要被存储的元素本身为响应式元素才行，这一点和Vuex是不一样的。



对于一个组件需要连续多次引用多个Vuex中的存储状态时，每个元素都需要为计算属性。为了解决这个重复繁琐的情况，Vuex提供了`mapState`辅助函数来帮助我们生成计算属性,让你少按几次按键:

```js
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```

最后使用 Vuex 并不意味着你需要将**所有的**状态放入 Vuex。虽然将所有的状态放到 Vuex 会使状态变化更显式和易调试，但也会使代码变得冗长和不直观。如果有些状态严格属于单个组件，最好还是作为组件的局部状态。你应该根据你的应用开发需要进行权衡和确定,而不是无脑的将全部状态元素全放在在State内。

### Mutation

在前面`入门了解`中说了，更改 Vuex 的 store 中的状态元素的唯一方法是提交 mutation。而其中mutation非常类似于一个事件方法，每个mutation都有一个字符串的**事件类型 (type)**和一个**回调函数 (handler)**。而这个回调函数就是需要实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```js
const store = createStore({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

上面的代码，很明显看出increment回调函数调用就会将其state中的count加一。但**实际在Vue模块中你不能直接调用一个 mutation 处理函数**。

在Vue模块中，需要通过调用**store.commit**方法来进行触发对应的mutation。

```js
store.commit('increment')
```

所以它更像一个事件处理，类似于switch判断，当触发了一个类型为increment的事件后就会调用它内部increment的操作。

我们可以在mutation中加入更多参数来实现state值的更新，即 mutation 的**载荷（payload）**，但最终第一个参数`state`不可少。

```js
mutations: {
	delete(state, index) {
    	  const item =state.todos[index]
	      state.todos.splice(index,1)
    	  state.deletes.push(item)
	}
}    
```

这样我们就可以在Vue模块中轻松上传参数了。

```js
store.commit("delete", index);
```

当然在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读：

```js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

```js
store.commit('increment', {
  amount: 10
})
```

当然除了这种方式，提交 mutation 的另一种方式还可以是直接使用包含 `type` 属性的对象：

```js
store.commit({
  type: 'increment',
  amount: 10
})
```

### Action

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

使用mutation  来改变state状态的话，会采用**同步事务**，所以会导致你的程序很难调试。例如，当你调用了两个包含异步回调的 mutation 来改变状态，你怎么知道什么时候回调和哪个先回调呢？

而Action则可以采用异步事务操作。

```js
state: {
    count: 0
},
mutations: {
    increment (state) {
      state.count++
    }
},
actions: {
    increment (context) {
      context.commit('increment')
    }
  }
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 `context.commit` 提交一个 mutation。或者通过 `context.state`来获取 state 。

实践中，我们会经常用到 ES6 的参数解构来简化代码（特别是我们需要调用 `commit` 很多次的时候）：

```js
actions: {
  increment ({ commit }) {
    commit('increment')
    commit('increment')
    ...
  }
}
```

和Mutation一样，Action也不能直接在Vue模块中调用，需要使用`store.dispatch`方法来触发:

```js
store.dispatch('increment')
```

乍一眼看上去感觉多此一举，我们直接commit触发 mutation 岂不更方便？实际上并非如此， **mutation 必须同步执行**！
而Action 就不受约束！我们可以在 action 内部执行**异步**操作：

```js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

Action更多是调用外部的Api，而mutation 则是单纯的做state的处理。

同样action 也可以使用类似mutation 载荷（payload）操作：

```js
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

### Module

如果一个中大型项目使用一个Store对象的话，全部存储到一个状态树下，那么store就会变得相当臃肿。

为了便于开发和使用store，Vuex允许将Store分割为多个模块（Module）。每个模块都拥有自己的state、mutation、action...甚至嵌套子模块。

```js

const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

对于模块内部的 mutation 的话，其中接受的第一个函数是**模块的局部状态对象**，而不是整个store的状态对象。

```js
const moduleA = {
  state: () => ({
    count: 0
  }),
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  }
```

同样，对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：

```js
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```



在 store 创建**之后**，你可以使用 `store.registerModule` 方法注册模块，从而达到动态注册模块：

```js
import { createStore } from 'vuex'

const store = createStore({ /* 选项 */ })

// 注册模块 `myModule`
store.registerModule('myModule', {
  // ...
})
// 注册嵌套模块 `nested/myModule`
store.registerModule(['nested', 'myModule'], {
  // ...
})
```

之后就可以通过 `store.state.myModule` 和 `store.state.nested.myModule` 访问模块的状态。

模块动态注册功能使得其他 Vue 插件可以通过在 store 中附加新模块的方式来使用 Vuex 管理状态。例如，[`vuex-router-sync`](https://github.com/vuejs/vuex-router-sync) 插件就是通过动态注册模块将 Vue Router 和 Vuex 结合在一起，实现应用的路由状态管理。

当然你也可以使用 `store.unregisterModule(moduleName)` 来动态卸载模块。但注意，不能使用此方法来卸载静态模块（即在创建store声明的模块）。



默认情况下，模块内部的state是嵌套的，拥有独立空间，模块间互不干扰，但模块内部的 action 和 mutation 仍然是注册在**全局命名空间**的——这样使得多个模块能够对同一个 action 或 mutation 作出响应。如果希望模块具有更高的封装度和复用性，你可以通过添加 `namespaced: true` 的方式使其成为带命名空间的模块。

当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。

```js
account: {
      namespaced: true,
      // 模块内容（module assets）
      state: () => ({ ... }), // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },
     // 子嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: () => ({ ... }),
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },
       ...
      }  
}      
```

被命名后，访问的时候需要加上模块名，如dispatch('account/login')。



## 总体结构

Vuex官方表示Vuex并不限制用户的代码结构，但规定了一些需要遵守的规则：

1. 应用层级的多个状态应该集中到单个 store 对象存储，而不是分开为多个属性存储。
2. 提交 **mutation** 是更改状态的唯一方法，并且这个过程是同步的。
3. 异步逻辑都应该封装到 **action** 里面。

建议：如果你的 store 文件太大，只需将 action、mutation 和 getter 分割到单独的文件。

对于大型应用，官方希望把 Vuex 相关代码分割到模块中。下面是官方推荐项目结构示例：

```js
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我们组装模块并导出 store 的地方
    ├── actions.js        # 根级别的 action
    ├── mutations.js      # 根级别的 mutation
    └── modules
        ├── cart.js       # 购物车模块
        └── products.js   # 产品模块
```

Vuex官方的购物车案例源码：https://github.com/vuejs/vuex/tree/4.0/examples/classic/shopping-cart



## Vue3组合式API

Vue3官方推荐使用组合式API（Composition API），所以在组合式API中，Vuex也有相应的方法。

可以通过调用 `useStore` 函数，来在 `setup` 钩子函数中访问 store。这与在组件中使用选项式 API 访问 `this.$store` 是等效的。

有了store的实例变量后，为了访问 state 和 getter，需要创建 `computed` 引用以保留响应性，这与在选项式 API 中创建计算属性等效。

要使用 mutation 和 action 时，只需要在 `setup` 钩子函数中调用 `commit` 和 `dispatch` 函数。

```js
import { useStore } from 'vuex'

export default {
  setup () {
    const store = useStore()
    return {
      // 在 computed 函数中访问 state
      count: computed(() => store.state.count),
      // 在 computed 函数中访问 getter
      double: computed(() => store.getters.double)
    }
  }
}
```



## 关于双向绑定

一般情况下我们都可能会面临这样的情况，需要将store获取过来的状态进行双向绑定。

```html
<input v-model="obj.message">
```

这样操作是不被允许的，在用户输入时，`v-model` 会试图直接修改 `obj.message`。在严格模式中，由于这个修改不是在 mutation 函数中执行的, 这里会抛出一个错误。

所以解决方法就是：给 `<input>` 中绑定 value，然后侦听 `input` 或者 `change` 事件，在事件回调中调用一个方法:

```js
<input :value="message" @input="updateMessage">
    
// ...
computed: {
  ...mapState({
    message: state => state.obj.message
  })
},
methods: {
  updateMessage (e) {
    this.$store.commit('updateMessage', e.target.value)
  }
}
```

这是常会想到的方法，但是如果当存在大量这种情况就会显得这种操作非常繁琐，而且代码上会变得臃肿不堪。

为了方便，另一个方法就是使用带有 setter 的双向绑定计算属性：

```js
<input v-model="message">

// ...
computed: {
  message: {
    get () {
      return this.$store.state.obj.message
    },
    set (value) {
      this.$store.commit('updateMessage', value)
    }
  }
}
```

