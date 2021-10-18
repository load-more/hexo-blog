---
title: 69 Nodejs
date: 2021-05-28 20:15:39
tags: [Nodejs]
categories: Nodejs
---

《Node.js 从零开发 web server博客项目》from 爱慕课，
重新学了一遍 Nodejs，收获很多。

<!-- more -->

# Nodejs 介绍

## 区别

- ECMAScript
  - 定义了语法，JS 和 Node 都必须遵守
  - 不能操作 DOM ，不能监听 click 事件
  - 不能处理 http 请求， 不能操作文件
- JavaScript
  - 使用了 ES 语法规范，外加 Web API，缺一不可
  - DOM操作，BOM 操作，事件绑定，Ajax 等
  - 两者结合
- Nodejs
  - 使用 ES 语法规范，外加 Nodejs API，缺一不可
  - 文件操作，处理 http 请求等
  - 两者结合，完成 server 端的任何操作

## CommonJS规范

引用：

```js
// 引用文件，只会执行文件里的代码
require('xxx/xxx')
// 引用文件并将导出的对象赋值给变量
const a = require('xxx/xxx')
```

导出：

```javascript
module.exports = {
    a,
    b,
}
exports.a = xxx
exports.b = yyy
```

## debug

## server 端

- 服务稳定性
  - server 端可能会遭受各种恶意攻击和误操作
  - 单个客户端可以意外挂掉，但服务端不能
  - 使用 PM2 做进程守候（增强服务稳定性）
- 考虑 CPU 和内存（优化、扩展）
  - 客户端独占一个浏览器，内存和 CPU 都不是问题
  - server 端要承载很多请求，CPU 和内存都是稀缺资源
  - 使用 stream 写日志（优化），使用 redis 存 session（扩展）
- 日志记录
  - 前端也会参与写日志，但只是日志的发起方，不关心后续
  - server 端要记录日志、存储日志、分析日志，前端不关心
- 安全
  - server 端要随时准备接收各种恶意攻击，前端则少很多
  - 如：越权操作，数据库攻击等
  - 登录验证，预防 xss 攻击和 sql 注入
- 集群和服务拆分
  - 产品发展速度快，流量可能迅速增加
  - 如何通过扩展机器和服务拆分来承载流量？

# 项目介绍

## 目标

- 开发一个博客系统，具有博客的基本功能
- 只开发 server 端，不关心前端

## 需求

- 首页，作者主页，博客详情页
- 登录页
- 管理中心，新建页，编辑页

## 技术方案

- 数据如何存储？
- 如何与前端对接？即接口设计

### 数据存储

- 博客

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210520223439.png)

- 用户

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210520223455.png)

### 接口设计

![](https://gitee.com/gainmore/imglib/raw/master/img/20210520223555.png)

# 开发接口（不使用框架）

- nodejs 处理 http 请求
- 搭建开发环境
- 开发接口（暂不连接数据库，暂不考虑登录）

## 概述

### http 请求概述

- DNS 解析，建立 TCP 连接，发送 http 请求
- server 接收到 http 请求，处理并返回（本章重点）
- 客户端接收到返回数据，处理数据（如渲染界面，执行 js）

### nodejs 处理 http 请求

- get 请求和 querystring
- post 请求和 postdata
- 路由（接口地址）

### nodejs 处理 get 请求

- get 请求，即客户端要向 server 获取数据，如查询博客列表
- 通过 querystring 来传递数据，如 `a.html?a=100&b=200`
- 浏览器直接访问，就发送 get 请求
- ![](https://gitee.com/gainmore/imglib/raw/master/img/20210520230136.png)

### nodejs 处理 post 请求

![image-20210522072508847](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210522072508847.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522072608.png)

### nodejs 处理路由

- http://github.com/
- http://github.com/username
- http://github.com/username/xxx

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522074117.png)

## 搭建开发环境

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522080615.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522081300.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522081314.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523090015.png)

**注意，当使用 `npm run test`、`npm run dev`、`npm run prd` 时，会找到 scripts 中的命令执行**

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523090333.png)

## 开发接口

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522081541.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522081746.png)

### 设计model

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522092616.png)

### 设计controller

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522094502.png)

### 流程

1. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522094746.png)
2. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522094951.png)
3. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522095227.png)
4. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522095334.png)
5. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522095439.png)
6. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522095732.png)

### 编写各个接口

# 数据库

## 操作数据库

- 建库
- 建表
- 表操作

### 建库

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522145015.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522145046.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522145120.png)

### 建表

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522145242.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522145319.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522145756.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210522150035.png)

### 操作表

1. 插入数据

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522151748.png)

2. 查询数据

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522152511.png)

3. 修改数据

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522153532.png)

4. 删除数据

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522153634.png)

   注意，实际开发中一般不用这种删除数据，而会使用到 **软删除**。

   也就是给表增加一列 `state`，默认所有数据的 `state` 为 0，表示数据存在数据库中，而要删除数据时，只需要把 `state` 设置为 1 即可，当查询数据时，只查询 `state` 为 0 的数据，这样就实现了 `软删除`。

   这样做的好处是，删除的数据随时可以恢复。

## nodejs 操作数据库

1. 安装插件：

   ```shell
   npm install mysql
   ```

2. 使用：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522160748.png)

在项目中使用：

1. 新建 `conf` 文件夹，在该文件夹下创建 `db.js` 文件，用于存放数据库的配置信息

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522181159.png)

   这里的环境参数在 `package.json` 中有配置

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210522181245.png)

2. 新建 `db` 文件夹，在该文件夹下创建 `mysql.js` 文件，文件内写入：

   ```javascript
   const mysql = require('mysql')
   const { MYSQL_CONF } = require('../conf/db')
   
   // 创建连接对象
   const connection = mysql.createConnection(MYSQL_CONF)
   
   // 开启连接
   connection.connect()
   
   function exec(sql) {
     // 返回一个promise对象，让外部能够调用回调的数据
     return new Promise((resolve, reject) => {
       connection.query(sql, (err, result) => {
         if (err) {
           reject(err)
         } else {
           resolve(result)
         }
       })
     })
   }
   
   module.exports = {
     exec
   }
   ```

3. 修改 `controller` 中的代码

   比如：

   ```javascript
   // 获取列表
   const getList = (author, keyword) => {
     let sql = 'select * from blogs where 1=1 '
     if (author) {
       sql = `${sql}and author='${author}' `
     }
     if (keyword) {
       sql = `${sql}and content like '%${keyword}%'`
     }
     return exec(sql)
   }
   
   // 获取详情
   const getDetail = (id) => {
     let sql = 'select * from blogs where 1=1 '
     sql = !id ? `${sql}and 1=2` : `${sql}and id=${id}` // 没有id，返回空
     return exec(sql).then(res => {
       return res[0]
     })
   }
   ```

   注意，返回的都是一个 promise 对象

4. 然后，在路由对象中修改相应代码

   ```javascript
     // 获取博客列表
     if (req.method === 'GET' && req.path === '/api/blog/list') {
       const author = req.query.author || ''
       const keyword = req.query.keyword || ''
       return getList(author, keyword).then(listData => {
         return new SuccessModel(listData)
       })
     }
     // 获取详情
     if (req.method === 'GET' && req.path === '/api/blog/detail') {
       const id = req.query.id || ''
       return getDetail(id).then(detailData => { // detailData对应上面的res[0]
         return new SuccessModel(detailData)
       })
     }
   ```

   注意，返回的也是一个 promise 对象，所以在 `app.js` 中也要修改相应代码

5. 修改 `app.js` 

   ```javascript
       // 处理 blog 路由
       const result = handleBlogRouter(req, res)
       if (result) {
         result.then(blogRes => { // blogRes对应上面的SuccessModel对象
           res.end(JSON.stringify(blogRes))
         })
         return
       }
   ```

   **.then() 中如果 return 的是 promise 对象，那么该对象就作为 .then() 的返回，供下一次链式调用；如果 return 的是一个值，那么该值就会被传入 `Promise.resolve()` 中，本身返回的也是一个 promise 对象，值作为 resolve 的值；如果没有返回值，则返回值就为 `undefined`，和上步一样。**

# 登录

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523075943.png)

## cookie

- 什么是 cookie
- JavaScript 操作 cookie，浏览器中查看 cookie
- server 端操作 cookie，实现登录验证

### 什么是 cookie

- 存储在浏览器中一段字符串 （最大 5kb）
- 跨域不共享
- 格式如 `k1=v1;k2=v2;k3=v3;`因此可以存储结构化数据
- 每次发送 http 请求，会将请求域的 cookie 一起发送给 server
- server 可以修改 cookie 并返回给浏览器
- 浏览器中也可以通过 JavaScript 修改 cookie （有限制）

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523081623.png)

### JavaScript 操作 cookie

- 客户端查看 cookie，三种方式
- JavaScript 查看、修改 cookie （有限制）

#### 浏览器查看cookie

1. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523082710.png)
2. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523082927.png)
3. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523083043.png)

#### JavaScript修改cookie

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523083251.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523092410.png)

### server端nodejs操作cookie

解析 cookie：

```javascript
  // 解析cookie
  req.cookie = {}
  const cookie = req.headers['cookie']
  cookie.split(';').forEach(item => {
    if (!item) return
    const key = item.split('=')[0].trim()
    const val = item.split('=')[1].trim()
    req.cookie[key] = val
  })
```

server 返回 cookie：

```javascript
const handleUserRouter = (req, res) => {  // 登录  if (req.method === 'GET' && req.path === '/api/user/login') {    // const { username, password } = req.body    const { username, password } = req.query    return login(username, password).then(loginRes => {      if (loginRes.realname) {        // 登录成功，返回cookie        res.setHeader('Set-Cookie', `username=${username}; path=/; httpOnly`)         return new SuccessModel()      }      return new ErrorModel('用户名或密码错误！')    })  }
```

`Set-Cookie` 的几个参数：

1. `path`

   - 指定 cookie 能被获取的路径范围
   - 比如 `path=/`表示域名下所有路径都能获取到该 cookie，`path=/api` 表示域名下所有 `/api` 的子路径可以获取到该 cookie，如 `/api/blogs`、`/api/users`等都能获取到该 cookie，而 `/ff/api` 或 `/apd` 却不能

2. `httpOnly`

   - 设置浏览器不能通过 JavaScript 修改 cookie，比如通过 `document.cookie="xxx=yyy"` 的方式很容易修改 cookie 的值，这样就可能导致其它用户的数据泄露
   - 给 cookie 设置 httpOnly 之后，用户无法通过 `document.cookie` 的方式在控制台查看 cookie，也无法利用这种方式对 cookie 进行修改

3. `expires`

   - 设置 cookie 的失效时间

   - ```javascript
     // 设置cookie的失效时间const getCookieExpires = (hour) => {  const date = new Date()  date.setTime(date.getTime() + (hour * 60 * 60 * 1000))  return date.toGMTString() // 格林威治时间}
     ```

## session

- 上面的问题：会暴露 username，很危险
- 解决办法： cookie 中存储 userid，server 端对应 username
- 解决方案： session，即 server 端存储用户信息

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523111509.png)

#### session原理

1. 当用户访问路由，客户端会自动发送 cookie 给 server
2. server 会检查 cookie 中是否有 userid，如果没有则新建一个 userid，并将 userid 添加到 cookie 中返回给客户端
3. 下一次访问路由时，会重复上面两步操作
4. 如果访问的是需要登录的路由，server 还会增加一些操作：检查 SESSION_DATA （userid 和 session 数据的映射表）是否有 userid 对应的 session，如果有说明用户已经登录，则正常返回数据，如果没有说明用户没有登录，返回异常
5. 当用户登录成功时，server 会从数据库中获取到一部分信息，然后写入 userid 对应的 session 中，这样 session 就可以作为用户是否完成登录的凭证

#### 实现过程

1. 由于在每个路由跳转完成时，都要进行 userid 的验证，所以应该处理 `app.js` 文件

   `app.js`

   ```javascript
   // 设置cookie的失效时间const getCookieExpires = (hour) => {  const date = new Date()  date.setTime(date.getTime() + (hour * 60 * 60 * 1000))  return date.toGMTString() // 格林威治时间}// session数据const SESSION_DATA = {}...  // 解析session  let needSetCookie = false // 是否需要设置cookie  let userId = req.cookie.userid // 获取cookie中的userid  if (userId) {    if (!SESSION_DATA[userId]) {      SESSION_DATA[userId] = {}    }  } else {    needSetCookie = true // 没有userid，需要设置cookie    userId = `${Date.now()}_${Math.random()}`    SESSION_DATA[userId] = {}  }  req.session = SESSION_DATA[userId]...  // 处理 blog 路由  const blogResult = handleBlogRouter(req, res)    if (blogResult) {      blogResult.then(blogRes => {        if (needSetCookie) { // 没有userid，需要设置cookie          res.setHeader('Set-Cookie', `userid=${userId}; path=/; httpOnly; expires=${getCookieExpires(24)};`)         }        res.end(JSON.stringify(blogRes))      })      return    }      // 处理 user 路由    const userResult = handleUserRouter(req, res)    if (userResult) {      userResult.then(userRes => {        if (needSetCookie) { // 没有userid，需要设置cookie          res.setHeader('Set-Cookie', `userid=${userId}; path=/; httpOnly; expires=${getCookieExpires(24)};`)         }        res.end(JSON.stringify(userRes))      })      return    }      // 处理其它路由    res.writeHead(404, {'Content-type': 'text/plain'})    res.write('404 Not Found\n')    res.end()
   ```

2. 然后就是对一些特定的路由进行处理，比如登录的路由和需要登录验证的路由

   `router/user.js`

   ```javascript
   const handleUserRouter = (req, res) => {  // 登录  if (req.method === 'GET' && req.path === '/api/user/login') {    // const { username, password } = req.body    const { username, password } = req.query    return login(username, password).then(loginRes => {      if (loginRes.realname) {        // 登录成功，设置session        req.session.username = username        req.session.realname = loginRes.realname        return new SuccessModel()      }      return new ErrorModel('用户名或密码错误！')    })  }  // 登录测试  if (req.method === 'GET' && req.path === '/api/user/login-test') {    const { username, realname } = req.session    if (username) {      return Promise.resolve(new SuccessModel({ username, realname }))    }    return Promise.resolve(new ErrorModel('未登录！'))  }}
   ```

#### session问题

   - 目前 session 直接是 js 变量，放在 nodejs 进程内存中
   - 问题一：进程内存有限，访问量过大，内存暴增怎么办？
   - 问题二：正式线上运行是多进程，进程之间内存无法共享

   进程内存模型

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523152227.png)

   问题一：进程内存有限，访问量过大，内存暴增怎么办？

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523152308.png)

问题二：正式线上运行是多进程，进程之间内存无法共享

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523152553.png)

#### 解决方案Redis

- web server 最常用的缓存数据库，数据存放在内存中
- 相比于 mysql，访问速度快（内存和硬盘不是一个数量级的）
- 但是成本更高，可存储的数据量更小（内存的硬伤）

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523152940.png)

- 将 web server 和 redis 拆分为两个单独的服务
- 双方都是独立的，都是可扩展的（例如都扩展成集群）
- 包括 mysql，也是一个单独的服务，也可扩展

**为何 session 适合用 redis？**

- session 访问频繁，对性能要求极高
- session 可以不考虑断电丢失数据的问题（内存的硬伤）
- session 的数据量不会太大（相比于 mysql 中存储的数据）

**为何网站数据不适合用 redis？**

- 操作频率不是太高（相比于 session 操作）
- 断电不能丢失，必须保留
- 数据量太大，内存成本太高

#### 安装Redis

教程：https://www.runoob.com/redis/redis-install.html

基本使用：

1. 打开命令行，输入：`redis-server`
2. 另外打开一个命令行，输入：`redis-cli`
3. 设置值：`set [key] [value]`
4. 获取值：`get [key]`
5. 查看键：`keys [pattern]`
6. 删除键：`del [key]`

#### Nodejs连接Redis

- 测试

1. 安装插件：`npm install redis`

2. 测试代码：

   ```javascript
   const redis = require('redis')// 创建客户端const redisClient = redis.createClient(6379, '127.0.0.1')redisClient.on('error', err => {  console.error(err)})// 测试redisClient.set('test', 'hello world', redis.print) // 打印状态信息redisClient.get('test', (err, val) => {  if (err) {    console.error(err)    return  }  console.log('val', val);  // 退出  redisClient.quit()})
   ```

操作 Redis 的流程基本上和操作 mysql 一样

## 和前端联调

- 登录功能依赖 cookie，必须用浏览器来联调
- cookie 跨域不共享，前端和 server 端必须同域
- 需要用到 nignx 做代理，让前后端同域

### nginx

- 高性能 web 服务器，开源免费
- 一般用于做静态服务、负载均衡
- 还有反向代理

### nginx 反向代理

![](https://gitee.com/gainmore/imglib/raw/master/img/20210523224813.png)

- 所谓反向代理指的是代理对于外部不可见，可以把它想象成一个黑盒，浏览器不知道它的内部是怎么操作的，只管访问就行；与之相对的是正向代理，用户可以手动操作代理

### nginx安装

下载：http://nginx.org/en/download.html

### nginx配置

配置文件路径：C:\nginx\conf\nginx.conf

### 常用命令

- 测试配置文件格式是否正确：`nginx -t`
- 启动 nginx
- 重启 nginx：`nginx -s reload`
- 停止 nginx：`nginx -s stop`

### 使用

1. 找到配置文件，修改配置参数，主要有以下几个部分：

   - ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523230927.png)
   - ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523231015.png)

2. 验证配置文件是否正确：

   在 nginx 目录下，执行命令，若出现以下提示，表示配置文件合法

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210523232031.png)

3. 命令行直接输入 `nginx` 启动（可以设置环境变量）



# 日志

- 系统没有日志，就等于人没有眼睛 --- 抓瞎
- 第一种，访问日志 `acess log` （server 端最重要的日志）
- 第二种，自定义日志（包括自定义事件、错误记录等）

## 目录

- nodejs 文件操作，nodejs stream
- 日志功能开发和使用
- 日志文件拆分，日志内容分析

## nodejs文件操作

- 日志要存储到文件中
- 为何不存储到 mysql 中？
  - 放到 mysql 中需要设计表结构，比较麻烦
  - 如果有多个服务器需要读取日志，那么每个服务器都需要有 mysql 环境，而文件类型都能打开，不需要配置环境
- 为何不存储到 redis 中？
  - 日志通常很大，如果放在 redis 中，成本高
  - 日志的读写通常是异步操作，对读写性能要求不是很高

### 示例

```javascript
const fs = require('fs')const path = require('path')const file = path.resolve(__dirname, 'test.txt')// 读取文件fs.readFile(file, (err, data) => {  if (err) {    console.log(err);    return  }  console.log(data.toString()); // data是二进制数据,需要转换成字符串})// 写入文件const opt = {  flag: 'a' // 追加写入,覆盖写入使用'w'}const content = '红红火火恍恍惚惚'fs.writeFile(file, content, opt, err => {  if (err) {    console.log(err);    return  }  console.log('追加写入成功!');})// 判断文件是否存在fs.exists(file, exist => {  console.log(exist);})
```

### 存在问题

- 当读取的文件很大时，比如是一部电影，有3-4个G，这样内存容易吃不消
- 当写入的内容很大时，比如有5-6个G，这样内存也容易吃不消
- 另一个问题是如果频繁进行文件的读写操作，由于文件读写本来就慢，就容易产生性能问题

## IO操作的性能瓶颈

- IO 包括 “网路IO” 和 “文件IO”
- 相比于 CPU 计算和内存读写，IO 的突出特点就是：慢！
- 如何在有限的硬件资源下提高 IO 的操作效率？

## stream

### 原理

![](https://gitee.com/gainmore/imglib/raw/master/img/20210524195054.png)

IO 操作犹如一个水缸向另一个水缸倒水。假如你的力气非常大，直接把一个水缸里的水倒到另一个水缸中，相当于你的硬件资源性能好，可以将资源全部加载出来实现读取操作。

但通常这种方法首先于用户的硬件配置，比较合理的解决办法是：用一个管子将两个水缸连起来，让水慢慢流过去。

就比如，我们在网上看一部电影，电影通常不是一次性加载完成的，而是一点点的加载出来的，用户看到哪里，视频就加载到哪里，等用户全部看完，电影也就全部加载完了。这样，就能够节省用户的硬件资源。

### 实际应用

1. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210524200813.png)

   上面代码很形象地契合了 stream 的原理，`process.stdin` 相当于来源（source），`process.stdout` 相当于目的地（dest），而中间的 `pipe` 就相当于连接两者的管道。

2. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210524200205.png)

   如果客户端向服务端传输的数据非常大，服务端的内存容易吃不消。

   通过上图的方式，客户端每向服务端发送一点数据，服务端就接收一点数据，直到数据全部接收完毕，触发 end。

3. ![](https://gitee.com/gainmore/imglib/raw/master/img/20210524201137.png)

   同样，上面的 `req` 相当于 `source`，`res` 相当于 `dest`，中间用 `pipe` 连接，可以看出 req 和 res 本质上都继承了 stream 的特性。

4. ```javascript
   // 实现利用stream将一个文件的数据拷贝到另一个文件const fs = require('fs')const path = require('path')const file1 = path.resolve(__dirname, 'data.txt')const file2 = path.resolve(__dirname, 'copy.txt')// 创建读写streamconst readStream = fs.createReadStream(file1)const writeStream = fs.createWriteStream(file2)// 从source向dest搭根管子readStream.pipe(writeStream)// 一点一点地读取文件,如果文件数据大,效果明显readStream.on('data', chunk => {  console.log('stream...  ', chunk.toString());})// source全部流向dest之后,触发endreadStream.on('end', () => {  console.log('拷贝完成!');})
   ```

5. ```javascript
   // 实现利用stream将一个文件的数据作为GET响应返回给客户端// （这个例子结合 `文件IO` 和 `网络IO`）const http = require('http')const fs = require('fs')const path = require('path')const server = http.createServer((req, res) => {  if (req.method === 'GET') {    const file = path.resolve(__dirname, 'data.txt')    const readStream = fs.createReadStream(file) // 创建读取stream    readStream.pipe(res) // 数据从文件慢慢流向res  }})server.listen(8080)
   ```



## stream写日志

1. 新建文件夹及文件：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210524205657.png)

2. `log.js`

   ```javascript
   const fs = require('fs')const path = require('path')// 根据文件名创建对应的writeStreamfunction createWriteStream(fileName) {  const filePath = path.resolve(__dirname, '../../', 'logs', fileName)  const writeStream = fs.createWriteStream(filePath, {    flags: 'a' // 追加写入  })  return writeStream}// 写访问日志const accessWriteStream = createWriteStream('access.log')function access(log) {  accessWriteStream.write(log + '\n')}module.exports = {  access}
   ```

3. `app.js`

   ```javascript
   const serverHandle = (req, res) => {  // 写访问日志  access(`${req.method} -- ${req.url} -- ${req.headers['user-agent']} -- ${Date.now()}`)  ...
   ```

4. 测试。。。

## 日志拆分

- 日志内容会慢慢积累，放在一个文件中不方便处理
- 按时间划分日志文件，如 2019-02-10.access.log
- 实现方式，Linux 的 crontab 命令，即定时任务

### crontab

- 设置定时任务，格式：`*****` command
- 将 access.log 拷贝并重命名为 2021-05-24.access.log
- 清空 access.log 文件，继续积累日志

首先，在 utils 下新建一个文件 `copy.sh`，这是用于拷贝日志文件的 shell 命令（由于这是 Linux 系统的命令，在 Windows 系统上无法使用），写入以下命令：

```shell
#!/bin/shcd /xxx/yyy/ddd # 进入到存放日志文件的目录cp access.log $(date +%Y-%m-%d).access.log # 复制access.log并将它按日期格式重命名echo "" > access.log # 将access.log的内容清空
```

之后，需要编辑 crontab，在 Linux 命令窗口下输入：`crontab -e`，就会进入到编辑窗口，然后再输入以下配置

![](https://gitee.com/gainmore/imglib/raw/master/img/20210524215744.png)

上面的 `* 0 * * *`代表每天的第0个小时执行依次后面的脚本命令。

五个星号分别代表的是 `第几分钟、第几小时、一个月的第几天、第几个月、一个星期中的第几天`



## 日志分析

- 比如针对 access.log，分析 chrome 的占比
- 日志是按行存储的，一行就是一条日志
- 使用 nodejs 的 readline（基于 stream，效率高）

比如，我们需要分析所有日志中 chrome 浏览器的占比，可以这样做：

1. 在 `utils` 文件夹下创建 `anlysis.js` 文件

2. 文件写入以下代码：

   ```javascript
   const fs = require('fs')const path = require('path')const readline = require('readline')const filePath = path.resolve(__dirname, '../../logs', 'access.log')const readStream = fs.createReadStream(filePath)let chromeNum = 0let num = 0const rl = readline.createInterface(readStream)// 逐行读取rl.on('line', (lineData) => {  if (!lineData) {    return  }  num++  const arr = lineData.split('--')  if (arr[2] && arr[2].indexOf('Chrome') !== -1) {    chromeNum++  }})rl.on('close', () => {  console.log('Chrome占比: ', chromeNum / num);})
   ```

   由上面代码可知，readline 实际上也是基于 stream 的

   

# 安全

- sql 注入：窃取数据库内容
- XSS 攻击：窃取前端的 cookie 内容
- 密码加密：保障用户信息安全（重要！）

## 补充

- server 端攻击方式非常多，预防手段也非常多
- 课程中只讲常见的、能通过 web server（nodejs）层面预防的
- 有些攻击需要硬件和服务来支持（需要 OP（服务端的运维人员）支持），如 DDOS

## sql注入

- 最原始、最简单的攻击，有了 web2.0 就有了 sql 注入攻击
- 攻击方式：输入一个 sql 片段，最终拼接成一段攻击代码
- 预防方式：使用 mysql 的 escape 函数处理输入内容即可

### 攻击方式

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525191915.png)

如上图，当用户输入的用户名和密码合法时，sql 语句内容能正常执行；

但如果用户输入了一些特殊字符，比如`-- `，这在 sql 中表示的是注释，所以这个符号之后的所有内容都不会执行。按照上面的方式，用户只需要输入一个用户名，密码随意输入，就能实现登录。

这是非常危险的，假如攻击者插入了删除数据的代码，那么整个数据库都可能被攻击。

### 预防方式

预防方法很简单，就是使用 mysql 中的 `escape` 函数

1. 在 `db/mysql.js`中，导出 escape：

   ```javascript
   module.exports = {  exec,  escape: mysql.escape}
   ```

2. 在我们创建 sql 语句处，对插入的变量进行 escape 转换即可，比如：`controller/user.js`：

   ```javascript
     const usrname = escape(username)  const psw = escape(password)  const sql = `select * from users where username=${usrname} and \`password\`=${psw}`
   ```

   **注意，插入变量时两边不需要加引号**

3. 这里可以打印输出一下 sql：

   ```javascript
   select * from users where username='zhangxiaosan\'-- ' and `password`='1234561fsdafsadf'
   ```

   可以看到 escape 实际上是将 -- 进行了转义，使之不会生成恶意代码



## XSS攻击

- 前端最熟悉的攻击方式，但 server 端更应该掌握
- 攻击方式：在页面展示内容中掺杂 js 代码，以获取网页信息
- 预防措施：转换生成 js 的特殊字符

### 攻击方式

在提交给 server 端的信息中插入一些恶意代码，如 `<script>alert(document.cookie)</script>`：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525194936.png)

这样攻击者就能窃取到 cookie：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525195148.png)

### 预防方式

为了避免这种攻击，server 端需要做的就是将这些提交进来的恶意代码进行转义，将一些特殊字符转义成不具攻击性的字符，如以下这些字符：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525193905.png)

这里我们可以使用插件 `xss` 来完成转义的工作。

1. 安装插件：`npm install xss`
2. 在需要进行转义的地方使用 `xss(content)` 即可转义

另外，还可以将 cookie 设置为 `httpOnly` 来禁止 js 脚本获取 cookie ，这种方法也可以预防 XSS 攻击，但是在项目中难免会操作 cookie，所以这种方法没有比上面转义那种方法更适用

## 密码加密

- 万一数据库被攻破，最不应该泄漏的就是用户的信息
- 攻击方式：获取用户名和密码，再去尝试登录其它系统，因为很多系统的用户名和密码都是通用的
- 预防措施：将密码进行加密，即便拿到密码也不知道明文

### 加密过程

1. 首先需要定义一个加密函数，可以在 `src/utils` 中创建一个 `crypto.js`，写入以下代码：

   ```javascript
   const crypto = require('crypto')// 密钥const SECRET_KEY = '!#This_is_SECRET_KEY#!'// md5加密function md5(content) {  const md5 = crypto.createHash('md5')  return md5.update(content).digest('hex')}// 加密函数function genPassword(password) {  const str = `password=${password}&key=${SECRET_KEY}`  return md5(str)}module.exports = {  genPassword}
   ```

2. 然后，在登录的时候，将密码进行加密即可

   ```javascript
   const login = (username, password) => {  // 密码加密  password = genPassword(password)  // 预防sql注入攻击  username = escape(username)  password = escape(password)  ...
   ```

# 小结

- 开发了哪些功能模块，完整的流程
- 用到了哪些核心的知识点
- 回顾 `server 端和前端的区别`

## 功能模块

- 处理 http 接口
- 连接数据库
- 实现登录
- 日志
- 安全
- 上线（接下来会涉及）

## 流程图

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525204955.png)

## 核心知识点

- http，nodejs 处理 http，处理路由，mysql
- cookie，session，redis，nginx 反向代理
- sql 注入，xss 攻击，密码加密
- 日志，stream，crontab，readline
- （线上环境的知识点，最后统一讲解）

## server端和前端区别

五个区别：

- 服务稳定性（最后讲）
- 内存 CPU（优化 --> stream，扩展 --> redis）
- 日志记录
- 安全（包括登录验证）
- 集群和服务拆分（设计已支持）

## 下一步

- 不使用框架开发，从零开始，关注底层 API
- 很琐碎、很复杂，没有标准可依，很容易将代码写乱
- 适合学习，但不适合应用，接下来开始 express 和 koa2



# express重构项目

- express 是 nodejs 最常用的 web server 框架
- 什么是框架？
  - 使开发关注业务，不关注底层
  - 使代码整洁有序，有条理
- 不要以为 express 过时了！

## 目录

- express 下载、安装和使用，express 中间件机制
- 开发接口，连接数据库，实现登录，日志记录
- 分析 express 中间件原理

## 介绍express

- 安装（使用脚手架 express-generator）
- 初始化代码介绍，处理路由
- 使用中间件

### 安装

- `npm install express-generator -g`
- `express express-test`
- `npm install`
- `npm start`

### 项目目录介绍

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525213935.png)

可以看出，express 中的目录基本和我们上面写的原生代码一致，只是多出来两个 `public` 和 `views` 文件夹，由于我们主要用来写后端，所以不会用到这两个目录。

### 初始配置

打开 `package.json`，脚本中只有 `start` 一条命令，为了开发方便，需要配置一个开发环境的命令

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525214247.png)

首先，安装 cross-env：`npm install cross-env`

然后在 `package.json` 中写入：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525220624.png)

使用 `npm run dev` 启动项目

### 介绍app.js

- 各个插件的作用
- 思考各个插件的实现原理（结合之前学过的知识）
- 处理 get 请求和 post 请求

**对比我们之前用原生代码写的 app.js 和 express 生成的 app.js，可以发现有很多共同之处，原理大致一样。**

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525222031.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210525222352.png)

*这里可以对比之前写的app.js，可以发现很多共通之处，比如logger对应的是access方法，用于写日志。*

### express处理路由

- 新建路由文件，在 `app.js` 中引用路由文件

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210525223835.png)

- 在路由文件中完成 get 请求或者 post 请求

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210525224723.png)

### 中间件机制

- 有很多 app.use
- next 参数是什么？

代码示例：

```javascript
const express = require('express')const app = express()app.use((req, res, next) => {  console.log('请求开始...', req.method, req.url);  next()})app.use((req, res, next) => {  // 假设处理cookie  console.log('处理cookie...');  next()})app.use((req, res, next) => {  // 假设处理post data(异步)  setTimeout(() => {    console.log('处理post data...');    next()  })})// 所有/api开头的路由触发该中间件app.use('/api', (req, res, next) => {   console.log('处理 /api 路由');  next()})// get请求所有/api开头的路由触发该中间件app.get('/api', (req, res, next) => {  console.log('get /api ...');  next()})// post请求所有/api开头的路由触发该中间件app.post('/api', (req, res, next) => {  console.log('post /api ...');  next()})// get请求所有/api/detail开头的路由触发该中间件app.get('/api/detail', (req, res, next) => {  console.log('get /api/detail ...');  // 没有next,直接返回res,不会继续执行之后的中间件  res.json({    errno: 0,    data: 'detail...'  })})// 登录验证函数function loginCheck(req, res, next) {  if (1) { // 验证成功,next执行下一个中间件    console.log('登录验证成功...');    next()  } else { // 验证失败,直接返回结果,不会执行接下来的中间件    res.json({      errno: -1,      msg: '登录验证失败!'    })  }}// post请求所有/api/new开头的路由触发该中间件// 可以传入多个中间件,如果第一个中间件没有返回next(),则后面的中间件不会执行app.get('/api/new', loginCheck, (req, res, next) => {  console.log('post /api/new ...');  // 没有next,直接返回res,不会继续执行之后的中间件  res.json({    errno: 0,    data: 'new...'  })})// 最后没有命中的路由返回404页面app.use((req, res, next) => {  console.log('处理404');  // 没有next,直接返回res,不会继续执行之后的中间件  res.json({    errno: -1,    msg: '404 Not Found'  })})app.listen(3030, () => {  console.log('server start on port 3030...');})
```

- 所谓的中间件就是 `app.use` 中 `(req, res, next) => {...}` 这样的函数，其中中间件中的 `next()` 函数表示的是执行下一个中间件。通俗点来说，`next()` 就是将所有分散的中间件串联起来的绳索，假如一个中间件没有 `next()`，那么它之后的所有中间件也就不会生效，即它和之后所有中间件的联系断开了。

  比如，试着将某处中间件的 `next()` 注释掉，可以发现之后的中间件都不会生效：

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210526195300.png)

- 使用中间件有三种方式：`app.use()`、`app.get()`、`app.post()`。其中，`app.use()`这种方式比其它两种方式的生效范围更广：

  - 如果 `app.use()` 不带路由，比如 `app.use((req, res, next) => {...})`，那么只要有 next 连接，这个中间件都会触发；
  - 如果 `app.use()` 带路由，比如 `app.use('/api/detail', (req, res, next) => {...})`，那么只有在对应的路由下，并且有 next 连接时，才会触发这个中间件；
  - 而 `app.get()` 和 `app.post()` 只有在满足对应的请求方式，而且在对应的路由下，并且有 next 连接时，才会触发该中间件。比如 `app.post('/api/login', (req, res, next) => {...})`，只有当使用 post 请求 /api/login 这个路由时，才会触发这个中间件。

- 利用 `app.use()`、`app.get()` 或是 `app.post()` 使用中间件时，参数中可以包含多个中间件，比如示例代码中加入的那个登录验证函数 `loginCheck()`，这种方法相当于将多个 `app.xxx()` 合并起来了，只不过这几个合并的中间件需要满足同一个路由或是同一种请求方式。注意，参数的中间件也是有顺序的，一般是从左到右的顺序依次执行。

## express开发接口

- 初始化项目，之前的部分代码可以复用
- 开发路由，并实现登录
- 记录日志

### 初始化项目

- 安装插件 `mysql`、`xss`
- mysql、controller、resModel 相关代码可以复用
- 初始化路由

## 登录

- 使用 `express-session` 和 `connect-redis` ，简单方便
- `req.session` 保存登录信息，登录校验做成 `express` 中间件

### 使用express-session

1. 安装：`npm install express-session`

2. `app.js`

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210526210859.png)

3. 测试（`routes/user.js`）：

   ```javascript
   router.get('/session-test', (req, res, next) => {
     // viewNum查看访问次数
     if (!req.session.viewNum) {
       req.session.viewNum = 1
     } else {
       req.session.viewNum++
     }
     res.json(
       new SuccessModel({viewNum: req.session.viewNum})
     )
   })
   ```

### 重写登录接口

`routes/user.js`

```javascript
router.post('/login', function(req, res, next) {
  const { username, password } = req.body
  login(username, password).then(loginRes => {
    if (loginRes.realname) {
      // 登录成功，设置session
      req.session.username = username
      req.session.realname = loginRes.realname
      res.json(new SuccessModel())
      return
    }
    res.json(new ErrorModel('用户名或密码错误！'))
  })
})
```

### 连接redis

1. 安装插件：

   - `npm install redis`
   - `npm install connect-redis`

2. 配置redis：

   `db/redis.js`

   ```javascript
   const redis = require('redis')
   const { REDIS_CONF } = require('../conf/db')
   
   // 创建客户端
   const redisClient = redis.createClient(REDIS_CONF.port, REDIS_CONF.host)
   
   // 监听异常
   redisClient.on('error', err => {
     console.error(err)
   })
   
   module.exports = redisClient
   ```

3. `app.js`

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210526214743.png)

4. 测试

### 登录中间件

- 新建文件夹 `middleware`，在里面创建一个文件 `loginCheck.js`

- 写入：

  ```javascript
  const { ErrorModel } = require('../model/resModel')
  
  module.exports = (req, res, next) => {
    // 如果session中有username,说明已登录,执行下一个中间件
    if (req.session.username) {
      next()
      return
    }
    // 如果没有则返回未登录
    res.json(new ErrorModel('未登录!'))
  ```

- 在需要登陆验证的地方插入即可，如 `routes/blog.js`：

  ```javascript
  // 获取博客列表
  router.get('/list', loginCheck, function(req, res, next) {
    const author = req.query.author || ''
    const keyword = req.query.keyword || ''
    getList(author, keyword).then(listData => {
      res.json(new SuccessModel(listData))
    })
  })
  ```

## 日志

- access log 记录，直接使用脚手架推荐的 `morgan`
- 自定义日志使用 console.log 和 console.error 即可
- 日志文件的拆分、日志内容分析，之前讲过，不再赘述

### 流程

1. 修改 `app.js`

   ```javascript
   // 当前环境
   const env = process.env.NODE_ENV
   if (env === 'development') { // 开发环境
     app.use(logger('dev')); // 格式为dev,默认是直接输出到控制台
   } else if (env === 'production') { // 发布环境
     const filePath = path.resolve(__dirname, 'logs/access.log')
     const writeStream = fs.createWriteStream(filePath, {
       flags: 'a'
     })
     app.use(logger('combined', { // 格式为combined,以stream的方式输出到日志文件中
       stream: writeStream
     }))
   }
   ```

2. 通过 `npm run dev` 或者 `npm run prd` 的方式运行项目，测试结果

   `npm run dev`

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210527190700.png)

   `npm run prd`

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210527190743.png)

   ## 中间件原理

- `app.use` 用来注册中间件，先收集起来
- 遇到 http 请求，根据 path 和 method 判断触发哪些
- 实现 next 机制，即上一个通过 next 触发下一个

### 源码



# koa2重构项目

- express 中间件是异步回调，koa2 原生支持 async/await
- 新开发框架和系统，都开始基于 koa2，例如 egg.js
- express 虽未过时，但是 koa2 肯定是未来趋势

## async/await使用

1. await 后面接 promise 对象，获取 resolve 的值
2. await 必须包裹在 async 函数里面
3. async 函数执行返回的也是一个 promise 对象
4. try-catch 用于截获 promise 中 reject 的值

## 介绍koa2

### 安装

- `npm install koa-generator -g`
- `koa2 koa2-test`
- `npm install`
- `npm run dev`

### 项目目录介绍

koa2 的项目目录和 express 项目目录完全一致，不过多介绍

### 初始配置

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527201851.png)

首先需要配置环境参数，安装 `cross-env`

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527202131.png)

### 介绍app.js

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527202728.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527202824.png)

### 处理路由

### 中间件机制

- app.use 中的参数本质上都是一个 async 函数

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210527205044.png)

- next 本质是一个 promise 对象，所以放在 await 的后面，作用和 express 中的 next 基本相同

## koa2开发接口



## 登录

- 和 express 类似
- 安装插件：`koa-generic-session` 和 `koa-redis`

### 配置

`app.js`

```javascript
const session = require('koa-generic-session')
const redisStore = require('koa-redis')

...
// session & redis
app.keys = ['!##This_is_SECRET!##'] // 密钥
app.use(session({
  // 配置cookie
  cookie: {
    path: '/',
    httpOnly: true,
    maxAge: 24 * 60 * 60 * 1000
  },
  // 配置redis
  store: redisStore({
    all: '127.0.0.1:6379' // 写死本地的redis
  })
}))
...
```

测试：

```javascript
router.get('/session-test', (ctx, next) => {
  if (!ctx.session.viewNum) {
    ctx.session.viewNum = 1
  } else {
    ctx.session.viewNum++
  }
  ctx.body = {
    errno: 0,
    view: ctx.session.viewNum
  }
})
```

*注意，后台需要通过 `redis-server` 打开redis服务*

## 重写接口

- 可以复用之前写的代码
- 注意要把所有的 promise 都改写成 async/await 的形式

## 日志

借用 express 中的 `morgan` 在 koa2 中写日志。

首先，需要安装插件：`npm install koa-morgan`；

然后，修改 `app.js`：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527223015.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527223205.png)

配置基本和 express 一样

## 中间件原理

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527224452.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527224527.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210527224553.png)

### 分析

- app.use 用来注册中间件，先收集起来
- 实现 next 机制，即上一个通过 next 触发下一个
- （不涉及 method 和 path 的判断，koa2 中没有get、post 这些请求，只有 app.use 这一个方式，然后就是 next 连接）



# 上线与配置

## 线上环境

- 服务器稳定性
- 充分利用服务器硬件资源，以便提高性能
- 线上日志记录

## pm2

- 进程守护，系统崩溃自动重启
- 启动多进程，充分利用 CPU 和内存
- 自带日志记录功能

## 目录

- PM2 介绍
- PM2 进程守护
- PM2 配置和日志记录
- PM2 多进程
- 服务器运维

## PM2介绍

### 下载安装

- `npm install pm2 -g`
- `pm2 -v`

### 基本使用

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528191006.png)

可以发现，使用 PM2 不会占用命令行，而且可以显示进程的一些信息。

可以通过 `pm2 list` 再次显示信息列表。

### 常用命令

- `pm2 start ...`  启动某个文件
- `pm2 list`   显示进程的列表信息
- `pm2 restart <AppName | id>`  代码修改后，重启进程使修改生效
- `pm2 stop <AppName | id>`  停止某个进程
- `pm2 delete <AppName | id>`  删除某个进程
- `pm2 info <AppName | id>`  查看某个进程的信息
- `pm2 log <AppName | id>`  查看某个进程的日志信息
- `pm2 monit <AppName | id>`  检测某个进程

## 进程守护

- node app.js 和 nodemon app.js ，进程崩溃则不能访问
- pm2 遇到进程崩溃，会自动重启

## 配置文件

- 新建 pm2 配置文件（包括进程数量，日志文件目录等）
- 修改 pm2 启动命令，重启
- 访问 server，检查日志文件的内容（日志记录是否生效）

### 新建配置文件

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528195109.png)

### 修改PM2启动命令

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528195140.png)

### 测试效果

。。。



## 多进程

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528195305.png)

- 系统中可以同时拥有多个进程，但每个进程都有一个最大的内存空间

### 为何使用多进程

- 回顾之前讲 session 时说过，操作系统会限制一个进程的内存
- 内存：无法充分利用机器的全部内存
- CPU：无法充分利用多核 CPU 的优势

### 多进程和redis

回顾之前 redis 的内容：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528195729.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528195738.png)

我们将 session 存到 redis 中，让每个进程访问一个 redis 来获取 session 信息，这样就能减少 session 对内存的占用，降低进程崩溃的发生几率。

- 多进程之间，内存无法共享
- 多进程可以访问一个 redis，实现数据共享
- 上图表明多个 node.js 进程可以通过访问一个 redis，来共享一个 session

### pm2创建多进程

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528200511.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210528200639.png)



## 关于运维

- 服务器运维，一般都有专业的 OP 人员和部门来处理
- 大公司都有自己的运维团队
- 中小型公司推荐使用一些云服务，如阿里云的 node 平台



#  总结

![image-20210528201434021](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210528201434021.png)

