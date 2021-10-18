---
title: 43 “幸福外卖”项目【6】
date: 2021-03-21 09:30:31
tags: [Vuejs, vuex]
categories: 项目
---

项目使用 vuex 管理状态的基本用法。

<!-- more -->

首先，我们要使用 vuex 一定要安装 vuex，这里我们可以在利用 vue-cli 生成项目的时候，勾选使用 vuex 的选项，这样项目里就会自动安装并使用 vuex。

然后，我们对 store 文件进行文件划分，为了遵循`模块化`的编码思想，这里我们分别创建六个文件，用于存放作用不同的代码。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210321093709.png)

之后，我们分别对每个文件写入相应的代码。

首先是 state.js 文件，主要存放组件之间共用的属性。

```javascript
export default {
  title: '',
  sort: '',
  page: null,
  list: [],
}
```

然后，定义要修改这些属性的方法。如果要使用计算属性，就在 getters.js 文件进行写入相应函数；如果要修改属性，就在 mutations.js 文件中写入相应函数；如果要使用异步操作修改属性，就在 actions.js 文件中写相应函数。

这里，我们要对属性进行初始化，所以在 mutations.js 文件中写初始化的函数。

```javascript
import {
  RECEIVE_TITLE,
  RECEIVE_SORT,
  RECEIVE_PAGE,
  RECEIVE_LIST,
} from './mutation-types'

export default {
  [RECEIVE_TITLE](state, payload) {
    state.title = payload.title
  },
  [RECEIVE_SORT](state, payload) {
    state.sort = payload.sort
  },
  [RECEIVE_PAGE](state, payload) {
    state.page = payload.page
  },
  [RECEIVE_LIST](state, payload) {
    state.list = payload.list
  },
}
```

*注意，mutations.js 中的函数在其他文件中也可能用到，为了便于维护代码，可以将涉及到的函数名放在一个 mutation-type.js 文件用常量的形式命名，这样一旦要修改其中的函数名，只要在 mutation-type.js 文件中修改即可。*

mutation-type.js：

```javascript
export const RECEIVE_TITLE = 'receive_title'
export const RECEIVE_SORT = 'receive_sort'
export const RECEIVE_PAGE = 'receive_page'
export const RECEIVE_LIST = 'receive_list'
```

**mutation 是修改 state 状态的唯一方式，如果需要用到异步操作，可以通过 action 对 mutation 进行操作，间接修改 state 状态。**

这里，我们需要获取 API 接口的数据，所以需要用到 ajax 进行数据请求，所以对数据的初始化可以放在 ajax 请求完成后执行，也就是在 actions.js 中完成。

```javascript
import {
  RECEIVE_TITLE,
  RECEIVE_SORT,
  RECEIVE_PAGE,
  RECEIVE_LIST,
} from './mutation-types'

import {getMultiData, getData} from '@/api/home.js'

export default {
  // 异步获取数据
  getAll(context) {
    getData('sell', 1).then(res => {
      if (res.returnCode === '1001') {
        console.log('获取成功！');
        context.commit(RECEIVE_TITLE, res.data)
        context.commit(RECEIVE_SORT, res.data)
        context.commit(RECEIVE_PAGE, res.data)
        context.commit(RECEIVE_LIST, res.data)
      }
    })
  },
}
```

最后，我们只需要在 index.js 入口文件中引用这几个属性即可。

```javascript
import { createStore } from 'vuex'
import state from './state'
import getters from './getters'
import mutations from './mutations'
import actions from './actions'

export default createStore({
  state,
  getters,
  mutations,
  actions,
})

```

当我们需要在组件中使用到这些属性时，可以使用一些映射函数简化代码。

```javascript
import {mapActions, mapState} from 'vuex'
export default {
  ...
  methods: {
    ...mapActions(['getAll'])
  },
  mounted() {
    this.getAll()
  },
  computed: {
    ...mapState(['title'])
  }
  ...
}
```

state 和 getter 放在 computed里，mutation 和 action 放在 methods 里。

