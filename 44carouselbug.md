---
title: “幸福外卖”项目【7】
date: 2021-03-21 12:12:38
tags: [Vuejs, $nextTick]
categories: 项目
---

使用$nextTick()解决轮播图bug

<!-- more -->

由于没有写后端 API 接口，所以我就随便找了一个接口，在请求这个接口后，自己添加响应数据，用来模拟异步请求。

actions.js 文件：

```javascript
import {
  RECEIVE_TITLE,
  RECEIVE_SORT,
  RECEIVE_PAGE,
  RECEIVE_LIST,
  RECEIVE_FOOD,
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
        context.commit(RECEIVE_FOOD, {food: [ // 模拟响应数据
          {name: '板栗', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E6%9D%BF%E6%A0%97.png'},
          {name: '橙子', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E6%A9%99%E5%AD%90.png'},
          {name: '汉堡', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E6%B1%89%E5%A0%A1.png'},
          {name: '鸡蛋', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E9%B8%A1%E8%9B%8B.png'},
          {name: '披萨', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E6%8A%AB%E8%90%A8.png'},
          {name: '薯条', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E8%96%AF%E6%9D%A1.png'},
          {name: '香肠', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E9%A6%99%E8%82%A0.png'},
          {name: '小鱼', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81_%E5%B0%8F%E9%B1%BC.png'},
          {name: '蚕豆', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E8%9A%95%E8%B1%86.png'},
          {name: '干粮', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81%E5%B9%B2%E7%B2%AE.png'},
          {name: '红枣', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E7%BA%A2%E6%9E%A3.png'},
          {name: '花生', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E8%8A%B1%E7%94%9F.png'},
          {name: '芒果', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E8%8A%92%E6%9E%9C.png'},
          {name: '面包', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E9%9D%A2%E5%8C%85.png'},
          {name: '肉铺', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E8%82%89%E9%93%BA.png'},
          {name: '山楂', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E5%B1%B1%E6%A5%82.png'},
          {name: '松子', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E6%9D%BE%E5%AD%90.png'},
          {name: '甜品', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E7%94%9C%E5%93%81.png'},
          {name: '腰果', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81-%E8%85%B0%E6%9E%9C.png'},
          {name: '饮料', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E9%A3%9F%E5%93%81%E9%A5%AE%E6%96%99.png'},
          {name: '西餐', pic: 'https://gitee.com/gainmore/imglib/raw/master/img/%E8%A7%86%E9%A2%91_%E8%A5%BF%E9%A4%90.png'},
        ]})
      }
    })
  },
}

```

这里的 food 数据是轮播图里所有标签里的数据，想要的效果如下：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210321121926.png)

为了得到这种效果，我们就需要将获得的 food 数组转换成二维数组（即这个二维数组的每一项都是有八个对象的数组），所以我们可以添加一个计算属性 foodArr，用于将一维数组转化成二维数组。

```javascript
  computed: {
    ...mapState(['title', 'food']),
    foodArr() { // 将一维数组转换为二维数组
      let arr = []
      let item = []
      for (let i of this.food) {
        item.push(i)
        if (item.length === 8) {
          arr.push(item)
          item = []
        }
      }
      if (item) {
        arr.push(item)
      }
      return arr
    }
  }
```

之后，我们就可以在 Home 组件里，使用 foodArr 得到一个轮播图了。

```vue
    <carousel class="swiper">
      <ul class="swiper-slide" v-for="(arr, index) in foodArr" :key="index">
        <li v-for="(item, index) in arr" :key="index">
          <img :src="item.pic" alt="">
          <span>{{item.name}}</span>
        </li>
      </ul>
    </carousel>
```

但是，结果往往没有这么简单。

我们的轮播图还是出现了bug：

![](https://gitee.com/gainmore/imglib/raw/master/img/test.gif)

可以看到，轮播图滚动到最后一页时出现了空白。

这是为什么呢？

主要是 swiper 加载顺序的问题。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210321141015.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210321141407.png)

正确的加载顺序应该是：当页面没挂载时，food 和 foodArr 为空，之后检测 foodArr 属性，当 foodArr 一发生改变且DOM元素更新时，创建 swiper。

因此，我们需要 watch 属性 foodArr，并添加 $nextTick() 方法（**当数据更新了，在dom中渲染后，自动执行$nextTick中的回调函数**）

于是，我们应该删去 Carousel 组件里创建 Swiper 的代码，并在 Home 组件中添加 watch 属性。

```javascript
  watch: {
    foodArr(newVal, oldVal) {
      this.$nextTick(() => {
        new Swiper ('.swiper-container', {
          direction: 'horizontal', 
          loop: true, 
          pagination: {
            el: '.swiper-pagination',
            clickable: true
          },
          observer: true,
          observeParents: true,
        })
      })
    }
  },
```

执行程序，可以发现轮播图滚动正常，bug解决！

![](https://gitee.com/gainmore/imglib/raw/master/img/test2.gif)