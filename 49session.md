---
title: 49 “幸福外卖”项目【12】
date: 2021-03-25 19:14:36
tags: [Vuejs, sessionStorge, localStorge, beforeEach]
categories: 项目
---

实现三个功能：设置登录后访问、页面刷新后保持登录状态、退出页面后保持登录状态。

<!-- more -->

## 设置登录后访问

例如，我们访问一个网站的评论数据时，往往会要求我们登录后才能查看。

要实现这个功能，常见做法是添加全局导航守卫：beforeEach()，

这个钩子函数的作用是，在路由跳转之前执行回调函数，一般用于登录验证。

首先，我们可以给要访问的路由设置 `meta` 属性，如果要求该路由必须登录才能访问，就可以设置一个 meta 属性： `needAuth:true`。

然后，在 beforeEach 中判断 `needAuth` 的状态，如果为 true，说明需要登录验证，则跳转到登录页面，否则直接完成跳转。

```javascript
router.beforeEach((to, from, next) => {
    if (to.meta.needAuth) {
        // 跳转到登录页面进行验证
    } else {
        next()
    }
})
```



## 页面刷新后保持登录状态

当我们登录成功之后，如果不小心刷新了页面，谁也不希望登录状态消失，还要再重新登录一遍。

所以，为了解决这个问题，我们需要用到 `sessionStorge`。

主要用到两个方法：

- `window.sessionStorge.setItem('xxx', data)`
- `window.sessionStorge.getItem('xxx')`

通过这两种方法，分别可以设置和获取要存储状态的值。

**案例演示**：

- 当我们登录成功后，利用 `window.sessionStorge.setItem()` 保存用户信息
- 每当刷新后，利用 `window.sessionStorge.getItem()` 获取用户信息

![](https://gitee.com/gainmore/imglib/raw/master/img/20210325204344.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210325204422.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210325204452.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210325204529.png)

*注意，setItem() 传入的数据都会转换成字符串，所以如果要传入一个json数据，要先用 `JSON.stringfy()` 将对象转换成字符串，然后用 `JSON.parse()` 将结果转换成 json 数据。 *

## 退出页面后保持登录状态

`sessionStorge` 中存储的数据只在当前会话中有效，即如果退出了页面，其中的数据将会被全部清空。

如果想要实现在特定时间内，保持登录状态，则需要用到 `localStorge` ，它会永久保存数据。

它的使用方式和 `sessionStorge` 基本一致，也有三种方法：

- `localStorge.getItem('xxx')`
- `localStorge.setItem('xxx', data)`
- `localStorge.removeItem('xxx')`

这里就不再演示方法了。

*注意这里还需要一个后台的校验接口，如果有3天自动登录期限，就要在进入true 的分支后再请求后台去校验token是否过期，如果过期就要重新登录了，否则就一切照常*

---

参考教程：https://zhuanlan.zhihu.com/p/138583621