---
title: 26 Vue 状态管理
date: 2021-03-06 09:22:33
tags: [Vuejs, Vuex]
categories: Vuejs
---

>在 Vue.js 组件开发中，经常需要将当前组件的状态传递给其他组件。父子组件进行通信时，通常会采用 Props 的方式传递数据。
>
>在一些大型应用中，单页面中可能会包含大量的组件，数据结构也比较复杂。当通信双方不是父子组件甚至不存在联系时，需要将一个状态共享给多个组件就会变得十分麻烦。
>
>为了解决这种情况，就需要引入**状态管理**这一种设计模式。而 Vuex 就是一个专门为 Vue.js 设计的状态管理模式。

<!-- more -->

## Vuex简介

- Vuex 是一个专门为 Vue.js 应用程序开发的状态管理模式
- 它以插件的形式和 Vue.js 配合使用
- 使用 Vuex 保存需要管理的状态值，值一旦被修改，所有引用该值的组件就会自动更新

## Vuex核心概念

Vuex 主要由五部分组成，分别是 state、getters、mutations、actions 和 modules。

- state
  - 存储项目中需要多组件共享的数据或状态
- getters
  - 从 state 中派生出状态，类似于 Vue 实例中的 computed 选项
- mutations
  - 存储更改状态的方法
  - 是 Vuex 中唯一修改 state 的方式
  - 但不支持异步操作，类似于 Vue 实例的 methods 选项
- actions
  - 可以通过提交 mutations 的方法改变状态
  - 与 mutations 不同的是，它可以进行异步操作
- modules
  - store 的子模块，内容相当于 store 的一个实例

### 定义state

store/index.js 中：

```javascript
  state: {
    message: 'Hello Vuex',
  },
```

App.vue 中获取定义的数据：

```javascript
  computed: {
    msg() { // 获取state中的message数据
      return this.$store.state.message;
    },
  },
```

当一个组件需要获取多个状态时，将这些状态都声明为计算属性会有些繁琐。

为了解决这一问题，可用 mapState 辅助函数生成计算属性：

```javascript
import { mapState } from 'vuex';

export default {
  computed: mapState({
    msg: (state) => state.message,
    title: (state) => state.title,
    count: (state) => state.count,
  }),
};
```

当映射的计算属性名称和对应的状态名称相同时，也可以为 mapState 传入一个字符串数组：

```javascript
import { mapState } from 'vuex';

export default {
  computed: mapState([
    'message', // this.message映射为this.$store.state.message
    'title',
    'count',
  ]),
};
```

由于 mapState 函数返回的是一个对象，因此还可以将它与局部计算属性 混合使用。使用对象展开运算符可以实现这种方式：

```javascript
  computed: {
    ...mapState([
      'message',
      'title',
      'count',
    ]),
    test() {
      return 'just test...';
    },
  },
```

实际开发中，经常采用对象展开运算符这种方式来简化代码

### 定义getters

有时需要从 state 中派生出一些状态，例如，对某个数值进行计算、对数组进行过滤等操作，这时就需要使用 getters。

getters 相当于 Vue 中的 computed 计算属性， getters 返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生改变才会被重新计算。

getters 会接收 state 作为第一个参数。

```javascript
  getters: {
    getCount(state) {
      return state.count + 10;
    },
  },
```

同样，访问定义的 getters 也有简化写法，即通过 mapGetters 辅助函数将 store 中的 getters 映射到局部计算属性中：

```javascript
import { mapState, mapGetters } from 'vuex';

  computed: {
    ...mapState([
      'message',
      'title',
      'count',
    ]),
    test() {
      return 'just test...';
    },
    ...mapGetters([
      'getCount',
    ]),
  },
```

### 定义mutations

**如果需要修改 Vuex 的 store 中的状态，唯一方法就是提交 mutations。**

每个 mutations 都有一个字符串事件类型（type）和一个回调函数（handler）。

这个回调函数可用实现状态更改，并且它会接收 state 作为第一个参数。

在 store 实例的 mutations 定义修改 state 状态的函数，然后在组件中使用 commit 方法提交到对应的 mutation，实现更改 state 状态的目的。

store/index.js：

```javascript
import { createStore } from 'vuex';

export default createStore({
  state: {
    message: 'Hello Vuex',
    title: '这是标题',
    count: 0,
  },
  getters: {
    getCount(state) {
      return state.count + 10;
    },
  },
  mutations: {
    add(state) {
      state.count += 1;
    },
    reduce(state) {
      state.count -= 1;
    },
  },
  actions: {
  },
  modules: {
  },
});

```

App.vue：

```vue
<template>
  <div>
    <h2>{{message}}</h2>
    <p>{{title}}</p>
    <p>{{count}}</p>
    <p>{{test}}</p>
    <p>{{getCount}}</p>
    <button @click='addFun'>count+1</button>
    <button @click='reduceFun'>count-1</button>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex';

export default {
  computed: {
    ...mapState([
      'message',
      'title',
      'count',
    ]),
    test() {
      return 'just test...';
    },
    ...mapGetters([
      'getCount',
    ]),
  },
  methods: {
    addFun() {
      this.$store.commit('add'); // 提交到对应的mutation函数
    },
    reduceFun() {
      this.$store.commit('reduce');
    },
  },
};
</script>

<style>
</style>

```

同样，也有简化写法，即使用 mapMutations 辅助函数将组件中的 methods 映射为 store.commit 调用：

```javascript
  methods: {
    // addFun() {
    //   this.$store.commit('add'); // 提交到对应的mutation函数
    // },
    // reduceFun() {
    //   this.$store.commit('reduce');
    // },
    ...mapMutations({
      addFun: 'add',
      reduceFun: 'reduce',
    }),
  },
```

实际项目中，经常需要在修改状态时传值，这时就要在 mutations 中加上一个参数，这个参数又称为 mutation 的载荷（payload），在 commit 的时候传递值就可以了。

store/index.js：

```javascript
  mutations: {
    add(state, n) {
      state.count += n;
    },
    reduce(state, n) {
      state.count -= n;
    },
  },
```

App.vue：

```javascript
    <button @click='addFun(3)'>count+3</button>
    <button @click='reduceFun(3)'>count-3</button>

  methods: {
    ...mapMutations({
      addFun: 'add',
      reduceFun: 'reduce',
    }),
  },
```

多数情况下，载荷（payload）应该是一个对象，这样可以使定义的 mutations 更具有可读性

store/index.js：

```javascript
  mutations: {
    add(state, payload) {
      state.count += payload.value;
    },
    reduce(state, payload) {
      state.count -= payload.value;
    },
  },
```

App.vue：

```vue
    <button @click='addFun({value:3})'>count+3</button>
    <button @click='reduceFun({value:3})'>count-3</button>

  methods: {
    ...mapMutations({
      addFun: 'add',
      reduceFun: 'reduce',
    }),
  },
```

### 定义actions

actions 和 mutations 的功能类似。

不同的是，**actions 提交的是 mutations** ，而不是直接更改状态，而且 actions 是异步更改状态。

store/index.js：

```javascript
  actions: {
    addAction(context, payload) {
      setTimeout(() => { // 异步操作，1s后改变值
        context.commit('add', payload);
      }, 1000);
    },
    reduceAction(context, payload) {
      setTimeout(() => {
        context.commit('reduce', payload);
      }, 1000);
    },
  },
```

上述代码中，action 函数接受一个与 store 实例具有相同方法和属性的上下文对象 context，因此可以调用 `context.commit` 提交一个 mutation。

而在 App.vue 中，action 需要使用 dispatch 方法进行触发，并且同样支持载荷方式和对象方式：

```vue
    <button @click='addFunA({value:3})'>count+3</button>
    <button @click='reduceFunA({value:3})'>count-3</button>
  
  methods: {
    addFunA(payload) {
      this.$store.dispatch('addAction', payload);
    },
    reduceFunA(payload) {
      this.$store.dispatch('reduceAction', payload);
    },
  },
```

同样，也有简化写法，使用 mapActions 辅助函数：

```javascript
    <button @click='addFunA({value:3})'>count+3</button>
    <button @click='reduceFunA({value:3})'>count-3</button>

  methods: {
    ...mapActions({
      addFunA: 'addAction',
      reduceFunA: 'reduceAction',
    }),
  },
```

