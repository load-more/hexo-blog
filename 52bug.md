---
title: 52 “幸福外卖”项目【14】
date: 2021-03-27 21:57:43
tags: [Vuejs]
categories: 踩坑
headimg: https://gitee.com/gainmore/imglib/raw/master/img/20210327215913.png
---

解决 `TypeError: Cannot read property '0' of undefined` 问题。

<!-- more -->

这是一个很常见的问题，一般发生在使用索引访问数组的时候，如：

```javascript
    <div>{{storeInfo.supports[0]}}</div>
```

出现这种问题的原因何在？

其实很简单，由于这里的 storeInfo 是存储在 Vuex 里的状态量，而且在 state.js 文件中给它设定了初始值（为一个空对象），当发送网络请求并得到响应后，才会通过 action 更新 storeInfo。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210327220806.png)

由于异步操作执行顺序的问题，就会导致 storeInfo 还没得到数据就已经访问里面的内容了，所以相当于 `storeInfo.supports[0]` 访问的是一个空对象，这样就会导致报错。

那么，既然 storeInfo 是一个空对象，为什么 `storeInfo.supports` 不会报错？

我们可以来测试一下：

```javascript
const a = {}, b = []
console.log(a.test) // undefined
console.log(a['test']) // undefined
console.log(b[0]) // undefined

const b = undefined
console.log(b.test) // Cannot read property 'test' of undefined
console.log(b['test']) // Cannot read property 'test' of undefined
```

不难发现，一个空对象或一个空数组，访问一个不存在的键值或索引，返回的都是 undefined（程序不会报错，只是返回值为 undefined）；

而访问一个值为 undefined 的数据中的属性，程序就会直接报错。

---

那么，应该如何解决这个问题呢?

方法很简单，就是给要访问的外层标签添加一个 v-if 的判断语句，当访问对象存在时才会访问，不存在时节点就会消失，不会访问，也就不会报错。