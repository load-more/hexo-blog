---
title: 06 node.js学习之路（二）
date: 2021-02-22 14:06:58
tags: [Nodejs]
categories: Nodejs
---


## 处理网站的静态资源

当浏览器收到 HTML 响应内容后，就会开始从上到下依次解析 HTML ，当在解析的过程中，如果发现：

link，script，img，iframe，video，audio 等带有 src 或者 href（link）属性标签的时候，浏览器会自动对这些资源发起新的请求，一个静态资源对应一个请求。

具体请求可在浏览器的 network 中查看。

{% asset_img Snipaste_2021-02-22_14-33-14.png %}

为了统一管理所有的静态资源，我们可以将这些资源都放在一个 public 文件夹内

{% asset_img Snipaste_2021-02-22_14-41-36.png %}

在index.html中：

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <link rel="stylesheet" href="/public/css/main.css">
</head>
<body>
  <p>哈哈哈哈哈哈哈</p>
  <img src="/public/img/a.png" alt="">
</body>
</html>
```

在app.js中：

```javascript
let http = require('http')
let fs = require('fs')

http
  .createServer((req, res) => {
    let url = req.url
    if (url === '/') {
      fs.readFile('./views/index.html', (err, data) => {
        if (err) {
          return res.end('404 Not Found...')
        }
        res.end(data)
      })
    } else if (url.indexOf('/public/') === 0) {
      // 访问静态资源时，注意要在路径前面加上 '.' ，表示当前路径
      fs.readFile('.' + url, (err, data) => {
        if (err) {
          return res.end('404 Not Found.')
        }
        res.end(data)
      })
    }
  })
  .listen(3000, () => {
    console.log('服务器启动！');
  })
```

## 处理表单GET提交

使用到的核心模块 -- url

```javascript
let url = require('url')
```

用法：

```javascript
let url = require('url')	// 1.导入模块

let res1 = url.parse('http://localhost:3000/comment?name=fdsf&message=fdsfsdfsdaf') // 2.解析url
/*
Url {
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'localhost:3000',
  port: '3000',
  hostname: 'localhost',
  hash: null,
  search: '?name=fdsf&message=fdsfsdfsdaf',
  query: 'name=fdsf&message=fdsfsdfsdaf',
  pathname: '/comment',
  path: '/comment?name=fdsf&message=fdsfsdfsdaf',
  href: 'http://localhost:3000/comment?name=fdsf&message=fdsfsdfsdaf'
}
*/

// 加入true参数，将提交的数据转换成一个对象放在query参数里
let res2 = url.parse('http://localhost:3000/comment?name=fdsf&message=fdsfsdfsdaf', true)
/*
Url {
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'localhost:3000',
  port: '3000',
  hostname: 'localhost',
  hash: null,
  search: '?name=fdsf&message=fdsfsdfsdaf',
  query: [Object: null prototype] { name: 'fdsf', message: 'fdsfsdfsdaf' },
  pathname: '/comment',
  path: '/comment?name=fdsf&message=fdsfsdfsdaf',
  href: 'http://localhost:3000/comment?name=fdsf&message=fdsfsdfsdaf'
}
*/
```

## 客户端重定向

如何通过服务器让客户端重定向？

1. 状态码设置为 302 临时重定向（301为永久重定向）

2. 在响应头中通过 Location 告诉客户端重定向的位置

```javascript
res.statusCode = 302
res.setHeader('Location', '/')
res.end()
```

