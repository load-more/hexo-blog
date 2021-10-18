---
title: 15 node.js 学习之路（七）
date: 2021-02-25 13:57:37
tags: [Nodejs, Promise, path]
categories: Nodejs
---

## 异步编程

### 回调函数

### 回调地狱

当有 A、B、C 三个文件，我们采用 `fs.readFile()`的方式分别读取这三个文件，查看打印结果

```javascript
var fs = require('fs')

fs.readFile('./a.txt', (err, data) => {
  if (err) {
    // 结束运行并打印错误信息
    throw err
  }
  console.log(data.toString());
})

fs.readFile('./b.txt', (err, data) => {
  if (err) {
    // 结束运行并打印错误信息
    throw err
  }
  console.log(data.toString());
})

fs.readFile('./c.txt', (err, data) => {
  if (err) {
    // 结束运行并打印错误信息
    throw err
  }
  console.log(data.toString());
})
```

我们通过不断运行可以发现，运行结果不是固定的，文件读取内容的顺序会发生随机的变化。

这是因为 `fs.readFile()` 是一个异步操作，会放到最后来执行，而它们的执行顺序受多种因素影响。

如果要规定读取顺序，如 A -> B -> C，那么可以进行嵌套：

```javascript
var fs = require('fs')

fs.readFile('./a.txt', (err, data) => {
  if (err) {
    throw err
  }
  console.log(data.toString());
  fs.readFile('./b.txt', (err, data) => {
    if (err) {
      throw err
    }
    console.log(data.toString());
    fs.readFile('./c.txt', (err, data) => {
      if (err) {
        throw err
      }
      console.log(data.toString());
    })
  })
})
```

这种代码嵌套层级多，阅读困难，被称为 `回调地狱(callback hell)` 

{% asset_img Snipaste_2021-02-25_14-13-51.png %}

### Promise

> 为了解决上面编码方式带来的问题（回调地狱嵌套），所以在 EcmaScript 6 中新增了一个 API：`Promise`

{% asset_img image-20210225142304032.png %}

- Promise 可以看成一个容器，里面存放了 一个异步任务
- 异步任务只有三种状态：`Pending`(等待)、`Resolved`(成功)、`Rejected`(失败)

#### 基本使用

```javascript
var fs = require('fs')

var p1 = new Promise((resolve, reject) => {
  fs.readFile('./a.txt', (err, data) => {
    if (err) {
      reject('fail')      // 失败，回调2
    }
    resolve('succeed')    // 成功，回调1
  })
})

// then 中有两个回调函数
p1.then((data) => {       // 回调1 
  console.log(data);
}, (err) => {             // 回调2
  console.log(err);
})
```

**注意，Promise 本身不是异步的，里面的任务才是异步的**

```javascript
var fs = require('fs')

console.log(111);
var p1 = new Promise((resolve, reject) => {
  console.log(222);
  fs.readFile('./a.txt', (err, data) => {
    console.log(333);
    if (err) {
      reject('fail')      
    }
    resolve('succeed')    
  })
  console.log(444);
})

console.log(555);
p1.then((data) => {       
  console.log(data);
}, (err) => {             
  console.log(err);
})

打印结果：
111
222
444
555
333
succeed
```

#### 封装 API

```javascript
var fs = require('fs')

function pReadFile(path) {
  return new Promise((resolve, rejects) => {
    fs.readFile(path, (err, data) => {
      if (err) {
        reject('failed')
      }
      resolve(data.toString())
    })
  })
}

pReadFile('./a.txt')
  .then((data) => {
    console.log(data);
    return pReadFile('./b.txt')	// 返回一个Promise对象，下一个then的第一个回调函数作为resolve
  })
  .then((data) => {
    console.log(data);
    return pReadFile('./c.txt')
  })
  .then((data) => {
    console.log(data);
  })

```



## 路径问题

### path 模块

Node.js 中的核心模块 -- `path`

常用 API：https://nodejs.org/dist/latest-v15.x/docs/api/path.html

### 动态获取绝对路径

先介绍两个属性：

- `__dirname` 动态获取文件的目录名
- `__filename` 动态获取文件名

相对路径的影响

```shell
E:\Web Front End\Node.js>nodemon path-demo/index.js
```

我们可以通过以上命令，操纵不在当前目录下的文件

例如，当前处在 Node.js 文件夹下，可以通过以上命令运行 Node.js 文件夹下的 path-demo 文件夹里的 index.js 文件

但如果在 index.js 文件里使用了相对路径，如 `./a.txt`，那么 `./` 就相当于命令窗口中的路径，即 `E:\Web Front End\Node.js`，所以会查找 `E:\Web Front End\Node.js\a.txt`，而真实路径却是 `E:\Web Front End\Node.js\path-demo\a.txt`，这样就会报错。

**注意，所有引用的文件中的相对路径都会变为命令窗口的路径，包括一些导入的模块文件**

为了解决这一问题，我们可以动态获取绝对路径

如，`path.join(__dirname, 'd.txt')`，这是路径就变为绝对路径了

