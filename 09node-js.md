---
title: 09 node.js 学习之路（五）
date: 2021-02-23 13:38:42
tags: [Nodejs, CRUD]
categories: Nodejs
---

## CRUD

### BootStrap 下载模板

https://v3.bootcss.com/examples/dashboard/

### 从 JSON 中读取数据

由于通过 `fs.readFile` 读取的 data 都是二进制，所以有必要将其转换成字符串，这里提供了两种方法：

-  ```javascript
  fs.readFile('./db.json', (err, data) => {
      data.toString()	// 直接转换成字符串
  })
  ```

- ```javascript
  fs.readFile('./db.json', 'utf8', (err, data) => {
      data	// 第二参数为 utf8 ，直接将数据转换成 utf-8 编码格式，data 也就成了字符串
  })
  ```

### 设计路由

### 路由模块的提取

为了不让 app.js 中的代码冗余，应当将关于路由的代码提取当另一个文件中，让 app.js 直接引用这个模块即可。

app.js 的主要作用是：

	- 创建服务器
	- 做一些服务相关的配置（模板引擎、body-parser解析表单 post 请求体、提供静态资源服务等）
	- 挂载路由
	- 监听端口启动服务

那么，如何让 app.js 和 router.js 关联呢？这里有两种方法：

- 在 app.js 中 加载 router.js 这个模块，传入 app 这个对象，在 router.js 中采用 `module.exports = function (app) {}` 的方式导出函数，函数内部作为对 app 的路由处理

- 这里 Express 有更加好的解决方法：

  - 在 app.js 中：

    ```javascript
    var router = require('./router')
    // 将router挂载到app中
    app.use(router)
    ```

  - 在 router.js 中

    ```javascript
    var fs = require('fs')
    
    var express = require('express')
    // 创建一个路由容器
    var router = express.Router()
    
    router.get('/', (req, res) => {
      /* 
        fs.readFile 的第二个参数是可选的，传入 utf8 就是声明读取文件直接按照 utf-8 编码，
        除此之外，也可以通过 data.toString() 的方式将二进制转换成字符串
      */
      fs.readFile('./db.json', 'utf8', (err, data) => {
        if (err) {
          return res.status(500).send('Server error.')
        }
        res.render('index.html', {
          students: JSON.parse(data).students
        })
      })
    })
    
    module.exports = router
    ```

### 封装异步API

引入问题：

```javascript
function fn() {
  setTimeout(function () {
  	var data = 'Hello'
  },1000)
}
// 问题：如何获取 setTimeout 里面的 data ？
console.log(fn())

// 想法1
function fn() {
  setTimeout(function () {
  	var data = 'Hello'
    return data
  },1000)
}
// 结论：不可行
// 原因：因为 setTimeout 是异步操作，所以会放到最后执行,由于 fn 没有返回值，所以 fn() 的结果为 undefined
// 一秒钟之后，再执行 setTimeout 里面的代码
console.log(fn())	// Undefined

// 想法2
function fn() {
  var data = ''
  setTimeout(function () {
  	var data = 'Hello'
    return data
  },1000)
  return data
}
// 结论：不可行
// 原因：和想法1一样，setTimeout 中的代码会在一秒钟后执行，此时 fn 早已返回，所以结果为一个空字符
console.log(fn())	// ''
```

**如果需要获取一个函数中异步操作的结果，则必须通过回调函数来获取**

```javascript
function fn(callback) {
  setTimeout(function () {
  	var data = 'Hello'
    callback(data)	// 将data传给callback，再callback中引用data
  }, 1000)
}
console.log(fn(function(data) {
  console.log(data)
}))	
```

#### 读取数据

所以，我们可以将读取数据库这步操作，封装成一个函数，放在 student.js 文件中，如果要用到这部操作，直接加载引用即可。

student.js:

```javascript
var fs = require('fs')
var dbPath = './db.json'

exports.find = function (callback) {
  fs.readFile(dbPath, 'utf8', function (err, data) {
    if (err) {
      return callback(err)
    }
    callback(null, JSON.parse(data).students)
  })
}
```

router.js:

```javascript
var student = require('./student')
student.find(function (err, students) {
    if (err) {
        return res.status(500).send('Server error.')
    }
    res.render('index.html', {
        students: students
    })
})
```

#### 保存数据

- 设置action

  ```html
  提交表单时，发送 post 请求，请求路径 /students/new
  <form action="/students/new" method="POST">
  	...
  </form>
  ```

- 添加路由

  ```javascript
  // 收到post请求后，处理请求
  router.post('/students/new', (req, res) => {
    // 调用save函数
    student.save(req.body, function(err) {
      if (err) {
        return res.status(500).send('Server error.')
      }
      res.redirect('/')
    })
  })
  ```

- 封装API保存数据

  ```javascript
  exports.save = function(stu, callback) {
    fs.readFile(dbPath, 'utf8', function (err, data) {
      if (err) {
        return callback(err)
      }
      let students = JSON.parse(data).students	// 解析JSON，转换成对象
  
      stu.id = students[students.length - 1].id + 1	// 添加数据
      students.push(stu)
  
      let res = JSON.stringify({students: students})	// 将对象转换成字符串
      fs.writeFile(dbPath, res, function(err) {		// 写入字符串数据
        if (err) {  // 如果写入出错，回调错误，return返回结束
          return callback(err)
        }
        callback(null)  // 写入成功，回调null
      })
    })
  }
  ```

#### 修改数据

原理和保存数据相似，也是创建一个 edit 页面，当点击 “编辑” 时，跳转到 edit 页面，edit 页面里有和 new 页面一样的表格，只不过表格里的 input 有对应的内容（可以通过设置 input 的 value 属性添加内容，使用 render 渲染页面并传递参数）。

当点击 提交 时，发送 post 请求，执行 updateById 函数，并跳转回主页面。

```javascript
// 通过id更新数据
exports.updateById = function(req, callback) {
  fs.readFile(dbPath, 'utf8', function (err, data) {
    if (err) {
      return callback(err)
    }
    let students = JSON.parse(data).students

    let findItem = students.find((item) => {
      return item.id === parseInt(req.id)
    })

    for (i in findItem) {  // 改变find获得的对象，会影响到原数组
      if (i !== 'id'){  // 不要给id重新赋值，否则id会变为字符串
        findItem[i] = req[i]
      }
    }

    let res = JSON.stringify({students: students})
    fs.writeFile(dbPath, res, function(err) {
      if (err) {
        return callback(err)
      }
      callback(null)
    })
  })
}

```



#### 删除数据

```javascript
// 根据id删除数据
exports.deleteById = function(id, callback) {
  fs.readFile(dbPath, 'utf8', function (err, data) {
    if (err) {
      return callback(err)
    }

    let students = JSON.parse(data).students
    let index = students.findIndex((item) => {
      return item.id === id
    })

    // splice会影响到原数组
    students.splice(index, 1)

    let res = JSON.stringify({students: students})
    fs.writeFile(dbPath, res, function(err) {
      if (err) {
        return callback(err)
      }
      callback(null)
    })
  })
}
```

### 总结

- 处理模板
- 配置开放静态资源
- 配置模板引擎
- 简单路由：渲染初始页面
- 路由设计
- 提取路由模块
- 封装异步API
  - find
  - findById
  - save
  - updateById
  - deleteById
- 实现具体功能
  - 通过路由收到请求
  - 接收请求的数据（get、post）
    - get： req.query
    - post：req.body
  - 调用数据操作 API 处理数据
  - 根据操作结果给客户端发送响应



  

