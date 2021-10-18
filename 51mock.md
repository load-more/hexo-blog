---
title: 51 “幸福外卖”项目【13】
date: 2021-03-27 21:08:38
tags: [Vuejs, Mockjs, Axios]
categories: 项目
headimg: https://gitee.com/gainmore/imglib/raw/master/img/20210327211036.png
---

使用第三方插件 **Mock.js** 模拟数据请求。

<!-- more -->

在写这个项目时，由于没有后端 API 接口，所以很多数据都是静态的。如果要模拟真实的数据请求过程，就需要模拟一个真实的 API 接口。

这里用到的插件是 **Mock.js**，[官方文档](https://github.com/nuysoft/Mock/wiki) 已经对使用方法进行了详细的说明，其实使用起来并不困难，无非是一些生成随机数据的语法，结合[官方示例](http://mockjs.com/examples.html) 可以更快地帮助我们理解各个语法地作用。

通过这些语法，可以生成一些随机的数据，但是为了更好的显示整个项目的效果，这里不会使用这些数据，而是使用 **Mock.js** 的第二大功能 -- **拦截 Ajax 请求**。

正如官网上那一行大字：*生成随机数据，拦截 Ajax 请求*。

这个插件的作用也就是这两个方面。

为了尽可能地模拟真实数据，我们会将一些设计好的 JSON 数据放在一个文件里，然后使用 Mock.js 拦截某些请求，并返回文件内的数据，这就相当于构建了一个 API 接口。

过程如下：

- 在 src 文件夹下创建一个子文件夹 mock，里面创建两个文件：data.json 和 mockServer.js。

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210327212550.png)

  data.json 存放的是设计好的 json 数据；

  mockServer.js 存放的是拦截请求后的操作。

- 首先看 data.json 文件：

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210327212718.png)

  这里设计了三个字段：`storeInfo`、`commodities`、`comments`，这三个字段就包含了一个详情页里面的所有数据，它们分别代表的是商家数据、商品数据和评论信息。

  由于一个详情页面对应一个商家、包含多个商品和多条评论，所以这三个字段的类型分别是对象、数组和数组。

  对象和数组里又分别存储了相应的信息。

- 设计好 JSON 文件之后，我们再进行拦截请求操作。在 mockServer.js 文件里输入以下代码：

  ```javascript
  import data from './data.json'
  import Mock from 'mockjs'
  
  // 拦截请求，返回模拟的数据
  Mock.mock('/commodities', {code: 0, data: data.commodities})
  Mock.mock('/comments', {code: 0, data: data.comments})
  Mock.mock('/storeinfo', {code: 0, data: data.storeInfo})
  
  // export default ???  不需要向外暴露任何数据, 只需要保存能执行即可
  ```

  *返回的模拟数据通常包含两个部分：状态码 code 和 响应数据 data。*

  *由于不需要导出任何数据，所以只需要在其它文件引入该文件即可。*

- 之后，我们需要设计异步请求。

  ```javascript
  // ajax.js
  import axios from 'axios'
  export function mockAjax(url) {
    return axios.get(url)
  }
  
  // mockReq.js
  import { mockAjax } from './ajax.js'
  export const getCommodities = () => mockAjax('/commodities')
  export const getComments = () => mockAjax('/comments')
  export const getStoreInfo = () => mockAjax('/storeinfo')
  ```

- 然后，我们需要使用到这些异步请求，由于这些数据我们存放在 Vuex 里，所以需要在 `actions.js` 中操作这些异步请求。

  首先设置对应的 state：

  ```javascript
  export default {
    commodities: [],
    comments: [],
    storeInfo: {},
  }
  ```

  然后设置 mutations 直接修改这些 state：

  ```javascript
    [RECEIVE_COMMODITIES](state, payload) {
      state.commodities = payload
    },
    [RECEIVE_COMMENTS](state, payload) {
      state.comments = payload
    },
    [RECEIVE_STOREINFO](state, payload) {
      state.storeInfo = payload
    },
  ```

  之后设置 actions 通过操作 mutations 间接修改 state：

  ```javascript
    getCommodities(context, payload) {
      getCommodities().then(res => {
        res = res.data
        if (res.code === 0) {
          context.commit(RECEIVE_COMMODITIES, res.data)
        }
      })
    },
    getComments(context) {
      getComments().then(res => {
        res = res.data
        if (res.code === 0) {
          context.commit(RECEIVE_COMMENTS, res.data)
        }
      })
    },
    getStoreInfo(context) {
      getStoreInfo().then(res => {
        res = res.data
        if (res.code === 0) {
          context.commit(RECEIVE_STOREINFO, res.data)
        }
      })
    },
  ```

- 最后，我们只需要在某个地方执行这些操作即可。

  因为我们想要在页面挂载之后请求这些数据，所以可以在根组件 App.vue 中的 mounted 里，获取这些数据：

  ```javascript
    mounted() {
      this.getCommodities()
      this.getComments()
      this.getStoreInfo()
    },
    methods: {
      ...mapActions(['getCommodities', 'getComments', 'getStoreInfo'])
    }
  ```

  

