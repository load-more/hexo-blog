---
title: 04 node.js学习之路（一）
date: 2021-02-19 22:24:00
tags: [Nodejs, art-template]
categories: Nodejs
---

> 官网：[nodejs.org](nodejs.org)

## node.js是什么？

- Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine.
  - Node.js不是一门语言，也不是库、不是框架，而是 JavaScript 运行时环境
  - 简单来说就是Node.js可以解析和执行 JavaScript 代码
  - 以前只有浏览器可以解析执行 JavaScript 代码
  - 也就是说现在的 JavaScript 可以完全脱离浏览器来运行，归功于 -- Node.js
- 不同环境下的 JavaScript
  - 浏览器中的 JavaScript：
    - EcmaScript
      - 基本的语法
      - if, var, function, Object, Array...
    - BOM
    - DOM
  - Node.js中的 JavaScript：
    - **没有BOM、DOM**
    - EcomaScript
    - 为 JavaScript 提供了一些服务器级别的操作API
      - 例如文件读写
      - 网络服务的构建
      - 网络通信
      - http 服务器
      - 等等...
- Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.
  - event-driven 事件驱动
  - non-blocking I/O model 非阻塞IO模型(异步)
- Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.
  - npm是世界上最大的开源库生态系统
  - 绝大多数 JavaScript 相关的包都存放在 npm 上

## 一些资源

- 《深入浅出Node.js》
- 《Node.js权威指南》
- JavaScript 标准参考教程（alpha）：http://javascript.ruanyifeng.com/
- Node 入门：http://www.nodebeginner.org/index-zh-cn.html
- 官方API文档：http://nodejs.org/dist/latest-v6.x/docs/api/
- 中文文档（版本较旧）：http://www.nodeclass.com/api/node.html
- CNODE社区：http://cnodejs.org
- CNODE-新手入门：http://cnodejs.org/getstart

## Node.js执行JavaScript脚本文件

1. 创建编写js文件

2. 打开终端，定位到脚本文件的路径下

3. 输入 `node <fileName>` 执行对应的文件

   *注意：文件名不要使用 `node.js`*

## Node.js读取文件

```javascript
// 1.使用require加载fs模块
var fs = require("fs")

// 2.读取文件：fs.readfile(filePath, callback)
//    callback中有两个参数：data、error
//    1.当读取成功时：data为数据，error为null
//    2.当读取失败时：data为null，error为错误对象

fs.readFile("./test.txt", function(error, data) {
  console.log(data.toString());
})
```

## HTTP服务

- 基本使用

```javascript
// 1.导入http核心模块
var http = require('http')

// 2.创建server
var server = http.createServer()

// 3.监听request请求事件，设置请求处理函数
server.on('request', function() {
  console.log('收到请求');
})

// 4.绑定端口号，启动服务
server.listen(3000, function() {
  console.log('服务器启动成功！通过localhost:3000访问！');
})

/*
使用链式调用简写上面代码
let http = require('http')

http
  .createServer(function(req, res) {
    console.log('收到请求')
  })
  .listen('3000', function() {
  	consloe.log('服务器启动')
  })

*/
```

- 两个参数

  request请求事件处理函数中，可以接收两个参数：

   - Request 请求对象

     请求对象可以用来获取客户端的一些请求信息，例如请求路径

  - Response 响应对象

    响应对象可以用来给客户端发送响应消息

```javascript
server.on('request', function(request, response) {
  console.log('收到请求，路径为：' + request.url);

  // response 对象有一个方法 -- write，可以用来给客户端发送响应数据
  // write可以使用多次，但最后一定要使用end来结束响应，否则客户端会一直等待
  // response.write('Hello')
  // response.write('Node.js')
  // response.end()

  // 上述方法可以直接简写为：
  response.end('Hello Node.js')
})
```

- 根据不同路径返回不同响应

```javascript
let http = require('http')
let server = http.createServer()
server.on('request', (request, response) => {
  let url = request.url
  let products = [
    {
      name: '香蕉',
      price: 111
    },
    {
      name: '苹果',
      price: 222
    },
    {
      name: '菠萝',
      price: 333
    }
  ]
  if (url === '/home') {
    response.end('HOME page')
  } else if (url === '/profile') {
    response.end('PROFILE page')
  } else if (url === '/products') {
    // 响应数据必须是二进制或者字符串，所以要对类型进行转换
    // stringfy将json数据转换成字符串
    response.end(JSON.stringify(products))
  } else {
    response.end('404 Not Found!')
  }
})
server.listen('3000', () => {
  console.log('服务器启动！');
})
```

## 核心模块

Node为JavaScript提供了很多服务器级别的API，这些API绝大多数都包装到了一个具名的核心模块中。例如文件操作的`fs`核心模块，http服务构建的`http`模块......

官方所有核心模块：https://nodejs.org/dist/latest-v15.x/docs/api/

## 模块系统

- require是一个方法，它的作用是用来加载模块
- 在Node中，模块有三种：
  1. 具名的核心模块，如 fs、http
  2. 用户自己编写的文件模块
- 对于自定义的文件模块，相对路径必须加`./`,可以省略 js 文件的后缀名
- 在 Node 中，没有全局作用域，只有模块作用域，外部访问不到内部，内部也访问不到外部，即都是封闭的

对于`04aaa.js`：

```javascript
let bExports = require('./05bbb')
 
// console.log(a);  // 不能访问到其它文件的变量，只能通过exports的方式
console.log(bExports.info);
console.log(bExports.add(3, 5));
```

对于 `05bbb.js`

```javascript
exports.info = {
  name: 'Tom',
  age: 13
}

exports.add = function(m, n) {
  return m + n
}

var a = 'Hello'
```

## IP地址和端口号

> 1.IP地址用来定位计算机
>
> ​	输入www.baidu.com时，会通过DNS将域名解析成ip地址
>
> ​	*在终端中输入`ipconfig`来获取IP地址*
>
> 2.端口号用来定位具体的应用程序
>
> ​	所有联网通信的软件都必须具有且占用一个端口号
>
> ​	端口号的范围在 0 - 65536 之间
>
> ​	在计算机中有一些默认端口号，最好不要区使用，如http服务的80（在使用80端口时，输入地址可以不加端口，默认会选择80端口）

```javascript
let http = require('http')
let server = http.createServer()
server.on('request', (req, res) => {
  // 获取ip地址
  console.log('ip：', req.socket.remoteAddress);
  // 获取端口号
  console.log('port：', req.socket.remotePort);
})
server.listen('3000', () => {
  console.log('服务器启动！');
})
```

## 解决中文乱码问题

在服务端默认发送的数据采用 utf-8 编码，但是浏览器不知道是 utf-8 编码的内容，所以浏览器在不知道服务器响应内容的编码情况下，会按照当前操作系统的默认编码去解析；

中文操作系统默认是 GBK 编码；

所以解决方法就是告诉浏览器发送内容的编码哪种类型。

```javascript
server.on('request', (request, response) => {
  response.setHeader('Content-Type', 'text/plain; charset=utf-8')
  response.end('你好，世界！')
})
```

### text/plain和text/html

```javascript
let http = require('http')
let server = http.createServer()
server.on('request', (req, res) => {
  let url = req.url
  if (url === '/plain') {
    // text/plain用于告诉浏览器发送的文本是普通文本格式
    res.setHeader('Content-Type', 'text/plain; charset=utf-8')
    res.end('你好，世界！')
  } else if (url === '/html') {
    // text/html用于告诉浏览器发送的文本是html格式，会自动解析html代码
    res.setHeader('Content-Type', 'text/html; charset=utf-8')
    res.end('<p>这是内容<a href="">点我</a></p>')
  }
})
server.listen('3000', () => {
  console.log('服务器启动！');
})
```

{% asset_img Snipaste_2021-02-21_10-03-11.png %}

## 响应不同的数据类型

参考网址：https://tool.oschina.net/commons

```javascript
let fs = require('fs')
let http = require('http')
let server = http.createServer()
server.on('request', (req, res) => {
  let url = req.url
  // 1. 读取html类型
  if (url === '/home') {
    fs.readFile('./resource/index.html', (err, data) => {
      if (err) {
        res.setHeader('Content-Type', 'text/plain; charset=utf-8')
        res.end('文件读取失败！')
      } else {
        res.setHeader('Content-Type', 'text/html; charset=utf-8')
        res.end(data)
      }
    })
  } else if (url === '/img') {
    // 2. 读取图片类型
    fs.readFile('./resource/test.jpg', (err, data) => {
      if (err) {
        res.setHeader('Content-Type', 'text/plain; charset=utf-8')
        res.end('图片读取失败！')
      } else {
        // 图片不需要设置编码集
        res.setHeader('Content-Type', 'image/jpeg')
        res.end(data)
      }
    })
  }
})
server.listen('3000', () => {
  console.log('服务器启动!');
})
```

## 代码风格

两个参考网站：

- https://standardjs.com/
- [Airbnb JavaScript Style](https://github.com/airbnb/javascript)

## 模板引擎art-template

### 安装art-template

`npm install art-template --save`

### 在浏览器中使用art-template

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <script src="node_modules/art-template/lib/template-web.js"></script>
  <script type="text/template" id='tpl'>
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
    </head>
    <body>
      <p>大家好，我叫：{{ name }}</p>
      <p>我今年 {{ age }} 岁了</p>
      <p>我喜欢：{{each hobbies}} {{$value}} {{/each}}</p>
    </body>
    </html>
  </script>
  <script>
    var ret = template('tpl', {
      name: 'Tom',
      age: 22,
      hobbies: ['sing', 'dance', 'draw']
    })
    console.log(ret);
  </script>
</body>
</html>
```

**模板引擎不会改变模板的大致内容，只会改变响应的变量，使用mustache语法进行传参**

### 在Node中使用art-template

> 模板引擎最早就是诞生于服务器领域，后来才发展到了前端

```javascript
// 1.导入相关模块
let template = require('art-template')
let fs = require('fs')

// 2.利用fs模块读取html文件的内容
fs.readFile('./template.html', (err, data) => {
  if (err) {
    return console.log('文件读取失败');
  }
  // 3.如果读取成功，将内容渲染到模板上
  let res = template.render(data.toString(), {
    name: 'Tom',
    age: 22,
    hobbies: ['sing', 'dance', 'draw']
  })
  // 4.打印模板
  console.log(res);
})
```

## 服务端渲染和客户端渲染

### 服务端渲染

服务端在返回 html 之前，在特定的区域，符号里用数据填充，再给客户端，客户端只负责解析 HTML 。

### 客户端渲染

html 仅仅作为静态文件，客户端端在请求时，服务端不做任何处理，直接以原文件的形式返回给客户端客户端，然后根据 html 上的 JavaScript，生成 DOM 插入 html。

### 异同

- 渲染本质一样，都是字符串拼接，将数据渲染进一些固定格式的html代码中形成最终的html展示在用户页面上。
- 拼接字符串必然引起性能的消耗。
- 服务端渲染性能消耗在服务端，当用户量比较多时，缓存部分数据以避免过多数据重复渲染。
- 客户端渲染，如当下火热的 spa 框架，Angular、React、Vue，在首次渲染时，大多是将原 html 中的数据标记（如 {{ text }} ）替换。客户端渲染较难的一点是数据更新以后，页面响应式更新时如何节省资源，直接 DOM 的读写，是很消耗性能的。 Vue 2.0 + 有 Vnode，进行 diff 后，渲染到页面上。

### 利弊

{% asset_img Snipaste_2021-02-22_09-50-28.png %}

---

作者：三毛丶
链接：https://www.jianshu.com/p/656a1666a1c5
来源：简书

---

### 总结

- 客户端渲染不利于 SEO 搜索引擎优化
- 服务端渲染的页面可以被爬虫抓取到，客户端异步渲染很难被爬虫抓取到
- 大部分网站既不是纯异步也不是纯服务端渲染出来的，而是两者结合来做的
- 例如大部分电商平台的商品列表就采用的是服务端渲染，目的是为了 SEO 搜索引擎优化，让百度等搜索引擎爬取到相关内容，有助于商品的推广
- 而商品评论列表就是为了用户的体验，而且也不需要 SEO 优化，所以采用的是客户端渲染，所以这些信息很难被爬虫抓取到
- 当进行一次服务端渲染，页面往往会刷新一次；而进行一次客户端渲染，页面不会刷新，所以用户体验更好

