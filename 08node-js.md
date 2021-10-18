---
title: 08 node.js学习之路（四）
date: 2021-02-22 22:55:20
tags: [Nodejs, Express]
categories: Nodejs
---

## Express

> 原生的 http 在某些方面的表现不足以应对我们的开发需求，所以我们就需要使用框架来加快我们的开发效率，框架的目的就是提高效率，让我们的代码更加高度统一。

### 安装 Express

```javascript
npm install express --save
```

### 使用 Express

```javascript
// 0.安装
// 1.引包
let express = require('express')

// 2.创建服务器应用程序，相当于原来的 http.createServer()
let app = express()

// 3.当服务器收到 get 请求 / 的时候，执行回调处理函数
app.get('/', (req, res) => {
  // res.write('Hello')
  // res.write('World')
  // res.end()
    
  // res.end('Hello World')
    
  // 使用 send 方法代替上面两种写法，输出中文时不用设置响应头
  res.send('Hello World!')
})
app.get('/about', (req, res) => {
  res.send('这是“关于”页面')  // 已经自动处理好编码问题，能够正常显示中文
})

// 公开指定目录
// 通过这种方式，用户可以直接通过 /public/xx 的方式访问 public 目录中的所有资源
app.use('/public/', express.static('./public/'))

// 4.创建端口号，相当于 server.listen()
app.listen(3000, () => {
  console.log('app is running at port 3000.');
})

```

### 修改完代码自动重启

我们可以使用一个第三方命令行工具 `nodemon` 来帮我们解决频繁修改代码重启服务器的问题。

安装：

```shell
npm install nodemon --global
```

安装完毕后，使用：

```shell
# 直接使用 nodemon 代替 node
nodemon app.js
```

通过 nodemon 启动的服务，会监视文件的变化，当文件发生改变时，会自动重启服务器。

### 路由

- 路由器

  {% asset_img Snipaste_2021-02-23_11-16-59.png %}

  当只有一根网线时，只有一个设备能连接网线上网；而我们使用路由器，可以将一根网线的信号分发到多个接口中，使用这些接口可以实现多台设备上网。

- 路由（router）其实就是一张表，这张表里有具体的映射关系。比如，你到了某个小区，要到某个房间去，这时你不认识路，就问小区的保安，保安就会告诉你房间的位置，这里的保安就相当于 “路由”。

- 基本路由

  > 请求方法 + 请求路径 + 回调函数

  - GET

    ```javascript
    app.get('/', callback)	 // 相对于一张映射表，每个路径对应一个回调函数
       .get('/category', callback)
       .get('profile', callback)
         ...
    ```

  - POST

    ```javascript
    app.post('/', callback)
       .post('/profile', callback)
         ...
    ```

### static-server 静态资源服务

当我们想通过 url 访问到一些静态资源时，可以使用到 Express 的静态资源服务。

例如，我们想通过 /public/img/a.png 访问到项目中的静态资源，可以使用：

```javascript
app.use('/public/', express.static('./public/'))
// 当请求的 url 中是以 /public/ 开头时，会在项目中的 public 文件夹里寻找对应路径的静态资源

// 上面代码可以简写，注意不能省略请求 url 的 /
app.use('/public', express.static('public'))

// 当省略第一个参数时，请求的 url 不用加 public
app.use(express.static('public'))

// 可以起别名，使用 a 代替请求 url 中的 public
app.use('/a', express.static('public'))
```

### 在 Express 中配置使用 art-template 模板引擎

- 安装

  ```shell
  npm install art-template --save
  npm install express-art-template --save
  ```

- 主要配置

  ```javascript
  app.engine('art', require('express-art-template'))
  ```

- 使用方法

  ```javascript
  let express = require('express')
  let app = express()
  
  /* 
    配置使用 art-template
      第一个参数表示：当渲染以 .art 结尾的文件时，使用 art-template 模板引擎；
      express-art-template 是专门用来在 Express 中把 art-template 整合到 Express 中的，
      虽然不需要加载 art-template，但也必须安装它，因为在 express-art-template 中依赖了 art-template。
      这里为了使文件有代码提示，可以将 art 改成 html
  */
  app.engine('html', require('express-art-template'))
  
  /* 
    Express 为 Response 响应对象提供了一个方法：render
    render方法默认是不可以使用的，但如果配置了模板引擎就可以使用
    res.render('html模板名', {模板数据})
    注意，render 函数的第一个参数不要写路径，只写文件名就可以，因为规定会在views文件夹下查找html文件，
    如果要修改默认 views 视图渲染存储目录，可以：app.set('views', 目录路径)	// 注意第一个参数必须是 views
  */
  app.get('/', (req, res) => {
    res.render('home.html', {
      title: 'Welcome To Express !'
    })
  })
  
  app.listen(3000, () => {
    console.log('app is running at port 3000');
  })
  
  
  ```

  如果要修改默认 views 视图渲染存储目录，可以：

  ```javascript
  app.set('views', 目录路径)	// 注意第一个参数必须是 views
  ```

- 一些 API

  ```javascript
  let express = require('express')
  let app = express()
  app.get('/', (req, res) => {
      let a= req.query	// 获取请求参数，注意只能在 GET 请求中使用
      res.redirect('/')	// 重定向
  })
  ```

### 获取表单 POST 请求体数据

在 Express 中没有内置的获取表单 POST 请求体的 API ，这里我们需要使用一个第三方包：`body-parser`

- 安装

  ```shell
  npm install body-parser --save
  ```

- 配置

  ```javascript
  var express = require('express')
  var bodyParser = require('body-parser')
  
  var app = express()
  
  // 配置 body-parser 中间件（插件），专门用来解析表单 POST 请求体
  // 只要加入这个配置，则在 req 请求对象中会多出一个属性：body
  // 直接通过 req.body 就可以获取表单 POST 请求体数据了
  app.use(bodyParser.urlencoded({ extended: false }))
  app.use(bodyParser.json())
  ```

- 注意

  - `req.query` 只针对 GET 请求，解析表单 POST 请求体数据要用第三方包
  - 在地址栏输入 url 采用的是 GET 请求，使用表单 POST 或者发送 ajax 请求采用的是 POST

### 服务器测试

使用谷歌插件：**Postman**

功能：

- 调试简单的 CSS、HTML、脚本等网页基本信息
- 发送几乎所有类型的 HTTP 请求

### 应用生成器（express-generator）

安装：

```shell
npm install express-generator -g
```

创建项目：

```shell
express testGenerator
```

结果：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210309165509.png)

按照如下三步使用：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210309165648.png)

目录结构：

![image-20210309170915792](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210309170915792.png)

使用 Nodemon 监控代码修改：

安装：

```shell
npm install nodemon -g
```

使用：

```shell
nodemon bin/www
```

这样就启动了项目，一旦项目中的代码发生了修改，就会自动重新运行项目。