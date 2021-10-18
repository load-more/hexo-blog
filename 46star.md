---
title: 46 “幸福外卖”项目【9】
date: 2021-03-23 22:43:22
tags: [Vuejs, 五星评分]
categories: 项目
---

实现根据评分大小显示星星个数的功能

<!-- more -->

## 规则

- 小数位小于0.5，向下取整；
- 小数位大于或等于0.5，取0.5；

例如，4.2 取 4；4.7 取 4.5 。

## 思路

一共有五颗星星，将这五颗星星分成三类：全星、半星、无星。

![image-20210323230539550](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210323230539550.png)

根据评分的值，利用计算属性计算三类星星的个数，并将个数放入一个数组中。

```javascript
  computed: {
    starArr() {
      const arr = []
      const {score} = this
      const onNum = Math.floor(score)
      const halfNum = (score - onNum) * 10 < 5 ? 0 : 1
      const offNum = 5 - onNum - halfNum
      for (let i = 0; i < onNum; i++) {
        arr.push(START_ON)
      }
      for (let i = 0; i < halfNum; i++) {
        arr.push(START_HALF)
      }
      for (let i = 0; i < offNum; i++) {
        arr.push(START_OFF)
      }
      return arr
    }
  }
```

利用动态属性，遍历数组，得到一排星星。

```vue
<template>
  <div class="container">
    <i v-for="(item, index) in starArr" :key="index" :class="item"></i>
  </div>
</template>
```



