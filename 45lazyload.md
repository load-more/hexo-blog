---
title: 45 “幸福外卖”项目【8】
date: 2021-03-21 15:43:34
tags: [Vuejs, lazyload]
categories: 项目
---

为了提高用户体验，加入图片懒加载功能。

<!-- more -->

## 方法一

使用 v-if 判断 food 有没有数据，如果没有则用加载图片替代；若有则加载图片。

```javascript
    <carousel class="swiper">
      <div v-if="food.length"> 
        <ul class="swiper-slide" v-for="(arr, index) in foodArr" :key="index">
          <li v-for="(item, index) in arr" :key="index">
            <img :src="item.pic" alt="">
            <span>{{item.name}}</span>
          </li>
        </ul>
      </div>
      <img src="@/assets/img/loading.png" alt="back" v-else>
    </carousel>
```

这种方法较简单，但需要专门制作加载图片。

## 方法二

使用插件 `vue-lazyLoad`

具体使用教程参考：https://www.jianshu.com/p/451b25df869f

由于项目采用 vue3，而这个插件暂不支持 vue3，所以会报错。

目前尚未采用这种方法。