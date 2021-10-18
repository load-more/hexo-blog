---
title: 40 “幸福外卖”项目【4】
date: 2021-03-19 22:00:47
tags: [Vuejs, axios, ajax]
categories: 项目
---

封装ajax请求函数

<!-- more -->

在 vue.js 中使用 axios 请求数据，我们得到的往往是一个 promise 对象，如何更加方便地取出响应数据？

## 引言

我们先看一个使用 axios 请求数据的例子：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210319220607.png)

ajax.js 文件：

```javascript
const axios = require('axios')	// 引入axios

module.exports = function(url, type='get', params={}) {	
  const instance = axios.create({
    baseURL: 'http://152.136.185.210:7878/api/m5',  
    timeout: 5000
  })
  
  const res = instance({ // 没有传入params，默认为get请求
    url
  })
  
  return res // 返回的是一个promise对象
}
```

getData.js 文件：

```javascript
const ajax = require('./ajax.js') // 引入封装函数

const res = ajax('/home/multidata') // 传入url，返回一个promise对象

res.then(res => {
  console.log(res.data); // 打印成功响应后的结果中的data
}).catch(err => {
  console.log(err)	// 失败则打印错误信息
})
```

可以看出，ajax 函数返回的是一个 promise 对象，而只有通过 `res.data` 的形式才能获取其中的数据，如果我们想直接通过 `res` 的形式获取数据该怎么办呢？

这里介绍三种方式。

## 1.外层套上promise

修改 ajax.js 中的代码：

```javascript
const axios = require('axios')

module.exports = function(url, type='get', params={}) {
  return new Promise((resolve, reject) => {	// 函数返回一个promise对象
    const instance = axios.create({
      baseURL: 'http://152.136.185.210:7878/api/m5',  //http://123.207.32.32:8000 
      timeout: 5000
    })
    const res = instance({
      url
    })
    res.then(response => { // 成功则返回response.data
      resolve(response.data) // resolve对应getData中的then
    }).catch(err => { // 失败则返回err
      reject(err) // reject对应getData中的catch
    })
  })
}
```

getData.js：

```javascript
const ajax = require('./ajax.js')

const res = ajax('/home/multidta')

res.then(res => {
  console.log(res); // 直接使用res即可，不需要.data
}).catch(err => {
  console.log(err);
})
```



## 2.使用拦截器interceptors

修改 ajax.js 文件：

```javascript
const axios = require('axios')

module.exports = function(url, type='get', params={}) {
  const instance = axios.create({
    baseURL: 'http://152.136.185.210:7878/api/m5',  //http://123.207.32.32:8000 
    timeout: 5000
  })

  instance.interceptors.request.use(config => {
    // 添加请求拦截器，在发送请求之前做些什么
    return config
  }, err => {
    console.log(err);
  })
  
  instance.interceptors.response.use(res => {
    // 添加响应拦截器，对响应数据做点什么
    return res.data // 这里我们将响应的res换成了res.data
  }, err => {
    console.log(err);
  })

  const res = instance({
    url
  })
  return res
}
```

getData.js：

```javascript
const ajax = require('./ajax.js')

const res = ajax('/home/multidta')

res.then(res => {
  console.log(res); // 直接使用res即可，不需要.data
}).catch(err => {
  console.log(err);
})
```



## 3.使用对象解构

直接修改 getData.js 文件：

```javascript
const ajax = require('./ajax.js')

const res = ajax('/home/multidta')

res.then(res => {
  const {data} = res
  console.log(data); // 直接使用data即可，不需要res.data
}).catch(err => {
  console.log(err);
})
```

