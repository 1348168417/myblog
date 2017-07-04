---
title: 项目中的常见问题题_vue_store
date: 2017-04-26 10:47:16
tags:
---
我会把我做关于模仿微博的这个项目遇到的一些问题公开在这里，遇到不对的地方请指教，发送[邮件wang1348168417@gmail.com](https://www.wang1348168417@gmail.com)，或者扫我的微信二维码。
一个简单的状态管理
### 单一数据源:
~~~html
const sourceOfTruth = {}
const vmA = new Vue({
  data: sourceOfTruth
})
const vmB = new Vue({
  data: sourceOfTruth
})
~~~
每当 sourceOfTruth 发生变化, vmA 和 vmB 都会自动更新它们的视图. 子组件可以通过 this.$root.$data 访问数据. 现在我们有了单一的数据源, 但是调试会很困难. 因为无论何时数据源发生变化都会改变程序, 但是没有任何迹象表明变化发生.<!--more-->

### store pattern

为了解决上述问题, 我们可以引入 store pattern:
~~~html
var store = {
  debug: true,
  state: {
    message: 'Hello!'
  },
  setMessageAction (newValue) {
    this.debug && console.log('setMessageAction triggered with', newValue)
    this.state.message = newValue
  },
  clearMessageAction () {
    this.debug && console.log('clearMessageAction triggered')
    this.state.message = 'action B triggered'
  }
}
~~~
所有的数据改变都发生 store 内. 这种集中的状态管理模式使我们很容易记录变化发生, 如何发生.
除了单一的数据源外, 每个 vue 实例或组件也可以有其私有状态:
~~~html
var vmA = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})
var vmB = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})
~~~

### 使用Vuex
~~~html
// 如果 Vuex 不是全局的, 那么确保调用 Vue.use(Vuex) 使 Vuex 生效.

const store = new Vuex.Store({
  // 数据源
  state: {
    count: 0
  },
  // 数据操作
  mutations: {
    increment (state) {
      state.count++
    }
  }
})

// 触发数据变化操作
store.commit('increment')

console.log(store.state.count) // -> 1
~~~
### State
store 自动注入到子组件中

通常我们通过计算属性来访问 store 中的数据, 这样就能感知到数据发生变化.
根组件的 store 属性会注入到其所有的子组件中. (通过 Vue.use(Vuex) 生效)
~~~html
const Counter = {
    template: `<div>{{ count }}</div>`,
    computed: {
        count() {
            // 子组件通过 this.$store 访问父组件的 store
            return this.$store.state.count
        }
    }
}

new Vue({
    // 父对象中的 store 自动注入到子组件
    store,
    componets: {
        Counter
    }
})
~~~
### mapState

如果 store 中有许多数据需要访问, 每个数据都需要定义一个计算属性会非常麻烦. Vuex 提供了 mapState 来简化计算属性的定义.
~~~html
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // es6 箭头函数更加简洁
    count: state => state.count,

    // 字符串 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了访问组件的 `this`, 必须使用普通的函数
    // 箭头函数会绑定 `this` 到 `mapState` 的参数这个对象
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
~~~
如果计算属性和 store 中数据是一一对应的, 可以使用更简单的字符串数组:
~~~html
computed: mapState([
  // map this.count to store.state.count
  'count'
])
~~~
### es6 的扩展操作符

使用 mapState 返回一个对象, 如果组件还有私有的计算属性, 通常我们可以使用 _.extend({localComputed}, mapState(...)) 这种方式合并对象已得到最终的 computed. 使用 es6 的扩展操作符可以简化:
~~~html
computed: {
    localComputed(){ /* ... */},
    // 通过扩展操作符扩展 computed 对象
    ...mapState({
        // ...
    })
}
~~~
### Getters
通常计算属性是基于一段 store 数据的代码, 比如过滤一个列表并计数:
~~~html
computed: {
    doneTodoCount() {
        return this.$store.state.todos.filter(todo => todo.done).length
    }
}
~~~
如果我们需要复用这段代码, 基本就是重构提取出一个函数, 但是这样还不是很理想.
~~~html
Vuex 在 store 中提供了 getters:

const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    },
    doneTodosCount: (state, getters) => {
        return getters.doneTodos.length
    }
  }
})

// 通过 `store.getters` 访问
store.getters.doneTodosCount
~~~
上面的计算属性就可以改成:
~~~html
computed: {
    doneTodoCount() {
        return this.$store.getters.doneTodoCount
    }
}
~~~
### mapGetters

同 state 的 mapState 类似, getters 也有 mapGetters 来简化计算属性的定义
~~~html
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
    // mix the getters into computed with object spread operator
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
~~~
使用对象可以自定义对应关系
~~~html
mapGetters({
  // map this.doneCount to store.getters.doneTodosCount
  doneCount: 'doneTodosCount'
})
~~~
### Mutations
Vuex 中的 state 只能通过 mutations 来改变. mutations 很像事件, 都有一个类型和处理函数. 处理函数是真正改变 state 的地方, 并以 state 作为第一个参数.
~~~html
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 改变 state
      state.count++
    }
  }
})
~~~
就是事件一样, 我们不能直接调用处理函数, 而是要通过 store.commit(type) 来触发 mutation 处理函数.

store.commit('increment')
带 playload commit

我们可以将处理函数的参数放到第二个参数 playload 中:
~~~html
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

store.commit('increment', {amount: 10})
对象风格 commit

store.commit({
    type: 'increment',
    playload: { amount: 10 }
})
~~~
### 静默模式

默认情况下, 每一次 commit 都会发送到插件 (比如: devtools) 中. 可能你会希望某些 commit 不被记录. 这时候可以传递第三个参数以设置为静默模式:
~~~html
store.commit('increment', {
  amount: 1
}, { silent: true })

// 对象风格 commit
store.commit({
  type: 'increment',
  amount: 1
}, { silent: true })
~~~
Mutations 要遵守 Vue 的响应式规则

即:

提前初始化所有的状态值
添加新的属性到对象时, 你应该:
使用 Vue.set(obj, 'newProp', 123) 或
直接替换新的对象: state.obj = {...state.obj, newProp: 123}
使用常量为 Mutations 命名

使用常量为 Mutations 命名是各种 Flux 实现常用的模式. 将所有常量放到一个文件中, 我们能看到整个程序有什么情况数据会发生变化.
~~~html
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // es6 特性 computed property name
    // 属性名称运行时确定
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
~~~
### Mutations 必须是同步的

异步 mutations 调用违反了所有的状态变化必须在 store 中进行的规定. 比如:
~~~html
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
~~~
当上例中状态变化时, someMutation 已经结束了. 这时候如果有其他状态变化的操作发生, devtools 记录下来的状态变化就是错误的.

### mapMutations

我们可以通过 this.$store.commit('xxx') 在组件中调用 mutations, 一般我们将这些调用分装到 methods 中, 同时 Vuex 也提供了 mapMutations 函数简化 methods 定义:
~~~html
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment' // 映射 this.increment() 到 this.$store.commit('increment')
    ]),
    ...mapMutations({
      add: 'increment' // map this.add() to this.$store.commit('increment')
    })
  }
}
~~~
### Actions
异步的 mutations 使程序的状态变化难以追踪. 为了解决异步操作, Vuex 引入了 actions.
actions 跟 mutations 非常像, 它们的不同之处在于:
~~~html
actions 不改变 state, 而是 commit mutations
actions 可以包含任意的异步操作
const store = new Vuex.Store({
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
})
~~~
actions 接收一个 context 对象作为参数, context 可以访问 commit, getters, state, 但是它不是 store 对象.
通常, 我们会使用 es6 的参数结构语法来简化代码:
~~~html
actions: {
    increment({commit}) {
        commit('increment')
    }
}
~~~
### Dispatching Actions

actions 通过 store.dispatch 来触发:

store.dispatch('increment')
dispatch 也支持 commit 中的 playload 参数以及对象风格的调用方式.
~~~html
// dispatch with a payload
store.dispatch('incrementAsync', {
  amount: 10
})

// dispatch with an object
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
~~~
### mapActions

类似 mapMutations

Actions 组合

actions 通常是异步的, 我们怎么来组合多个 actions 来执行复杂的操作?

首先我们需要知道的是 store.dispatch 返回 actions 中处理函数的返回值, 因此我们可以返回一个 Promise:
~~~html
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  },

  actionB ({ dispatch, commit }) {
    // 组合
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
使用 async/await 语法, 可以简化为:

// 假设 getData() 和 getOtherData() 返回 Promises

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // wait for actionA to finish
    commit('gotOtherData', await getOtherData())
  }
}
~~~
### Modules
当我们的程序足够大时, store 也会变得非常大, 其中的 state, getters, mutations, actions 也会非常大.

因此 Vuex 允许我们将 store 分成几个 modules, 每个 modules 都有自己的 state, getters, mutations, actions 甚至它自己的 modules.
~~~html
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA's state
store.state.b // -> moduleB's state
~~~
### Modules 当前状态

在 modules 中, getters 和 mutations 的第一个参数都是 modules 的 state, 同样 actions 的 context.state 也是 modules 的 state, 根节点的状态可以通过 context.rootState 访问到. getters 的可以通过第三个参数访问 $rootState:
~~~html
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
~~~
###　命名空间

modules 的 state 放到根节点的对应的属性中, 而 actions, mutations 和 getters 没有命名空间. 所以多个 modules 可以对同一个 commit 或 dispatch 做响应. 因此必须自己通过前缀或后缀来避免命名冲突.

动态 Modules 注册
~~~html
store.registerModule('myModule', {
  // ...
})
keep learning
~~~