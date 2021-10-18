---
title: 33 解决跨域问题
date: 2021-03-12 15:41:18
tags: [Vuejs, Express]
categories: Express
---

> 通常情况下，两个不在同一域名下的页面无法进行正常通信，或者无法获取其他域名下的数据，这种现象就成为跨域。
>
> 主要原因是浏览器出于安全问题考虑，采用了**同源策略**，通过浏览器对 Javascript 的限制，防止用户恶意获取非法数据。

<!-- more -->

Vue.js 和 Express 提供了两种支持跨域的方式。

## Vue.js跨域

对于不支持跨域的服务器端的请求，客户端不能配置影响到服务器端的代码。

如果需要完成该功能，可以使用 vue-resource 的 jsonp() 方法，具体请求代码如下：

```javascript
this.$http.jsonp('/someUrl', [data], [options].then(successCallback, errorCallback));
```

*jsonp() 不能发送 post 请求，不管是否跨域，只要使用 jsonp() 方式则请求一定是 get 方式，因为本质是 script 方式加载的*

使用 jsonp() 这种方式比较麻烦，一般使用下面这种方法。

## Express跨域

在 app.js 种进行全路由的配置：

```javascript
app.all("*",function(req,res,next){
  //设置允许跨域的域名，*代表允许任意域名跨域
  res.header("Access-Control-Allow-Origin","http://localhost:8081");
  //允许的header类型
  res.header("Access-Control-Allow-Headers","Content-Type, Content-Length, Authorization, Accept, X-Requested-With, yourHeaderField");
  //跨域允许的请求方式
  res.header("Access-Control-Allow-Methods","DELETE,PUT,POST,GET,OPTIONS");
  if (req.method == 'OPTIONS')
    res.send(200);  //让options尝试请求快速结束
  else
    next();
});

// =============

app.all("*",function(req,res,next){
  res.header("Access-Control-Allow-Origin","*");
  res.header("Access-Control-Allow-Headers","Content-Type, Content-Length, Authorization, Accept, X-Requested-With");
  res.header("Access-Control-Allow-Methods","DELETE,PUT,POST,GET,OPTIONS");
  if (req.method == 'OPTIONS')
    res.send(200);
  else
    next();
});
```

此代码要在所有的路由路径配置之前执行，否则代码之前定义的路由不接受此跨域的头部配置。

如果开发者不需要所有的路由都支持跨域，也可以选择在单个路由中配置相关的头部信息。

## 使用插件

如果上面的方法都没有，可以使用谷歌商店的一款插件：

**Allow CORS: Access-Control-Allow-Origin**