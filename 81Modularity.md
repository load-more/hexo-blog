---
title: 81 JavaScript 模块化
date: 2021-09-21 18:19:36
tags: [JavaScript, Modularity]
categories: JavaScript
---

花了一点时间总结了一些关于 JavaScript 实现模块化的方式。

<!-- more -->

## 模块加载方案

---

### 模块化的作用

- **命名空间**

在 JavaScript 中，每个 JS 文件的接口都暴露在全局作用域中，每个人都可以访问它们，并且容易造成命名空间污染。模块化可以为变量创建私有空间来避免命名空间污染。

- **可重用性**

有没有曾经在某个时候将之前编写的代码复制到新的项目中呢？如果将此代码模块化，则可以反复使用，且在需要修改时只需要修改此模块，而不需要在项目中的每个此代码处做修改。

- **可维护性**

模块应该是独立的，一个设计良好的模块应尽可能减少对部分代码库的依赖，从而使其能够独立地删减和修改。当模块与其他代码片段分离时，更新单个模块要容易得多，还可以对每次修改的内容做版本管理。

---

### AMD与RequireJS

**AMD 规范是 RequireJS 在推广过程中对模块定义的规范化产出。**

首先，先了解 `require.js` 的使用方法。

项目目录：

```js
AMD/
    index.html
	main.js // 入口文件，依赖了 add.js 和 square.js
	add.js // 导出功能函数
	square.js // 依赖了 multiply.js
	multiply.js // 导出功能函数
```

代码：

```html
<!-- index.html -->
<body>
  <h1>AMD</h1>
  <script data-main="./main" src="https://cdn.jsdelivr.net/npm/requirejs@2.3.6/require.js"></script>
</body>
```

```js
// main.js
require(['./add', './square'], function(addModule, squareModule) {
  const addRes = addModule.add(11, 22)
  console.log(`11 + 22 = ${addRes}`)
  const squareRes = squareModule.square(15)
  console.log(`15 * 15 = ${squareRes}`)
})
```

```js
// add.js
define(function() {
  console.log('AddModule is called!!!')
  return {
    add(a, b) {
      return a + b
    }
  }
})
```

```js
// square.js
define(['./multiply'], function(multiplyModule) {
  console.log('SquareModule is called!!!')
  return {
    square(a) {
      return multiplyModule.multiply(a)
    }
  }
})
```

```js
// multiply.js
define(function() {
  console.log('MultiplyModule is called!!!')
  return {
    multiply(a) {
      return a * a
    }
  }
})
```

结果：

```js
AddModule is called!!!
MultiplyModule is called!!!
SquareModule is called!!!
11 + 22 = 33
15 * 15 = 225
```

总结：

- AMD 规范定义了一个全局函数 `define`，如果文件需要依赖模块，`define` 函数就需要传入两个参数：一个是模块路径的数组，一个是回调函数，该回调函数的参数对应了数组中的每个模块，可以在回调函数中以调用参数的形式调用每个模块，同时可以通过 `return` 一个对象的方式导出这个模块的数据。如果不需要依赖模块，直接传入回调函数即可；
- 对于入口文件中的模块引入，直接使用 `require` 函数即可，可以用 `define` 代替；

---

### CMD与SeaJS

**CMD 规范是 SeaJS 在推广过程中对模块定义的规范化产出。**

首先，先了解 `sea.js` 的使用方法。

项目目录：

```js
CMD/
    index.html
	main.js // 入口文件，依赖了 add.js 和 square.js
	add.js // 导出功能函数
	square.js // 依赖了 multiply.js
	multiply.js // 导出功能函数
```

代码：

```html
<!-- index.html --><body>  <h1>CMD</h1>  <script src="https://cdn.jsdelivr.net/npm/seajs@3.0.3/dist/sea.js"></script>  <script>    seajs.use('./main.js') // 指定入口文件  </script></body>
```

```js
// main.jsdefine(function(require, exports, module) {  const addModule = require('./add')  const addRes = addModule.add(12, 23)  console.log(`12 + 23 = ${addRes}`)  const squareModule = require('./square')  const squareRes = squareModule.square(24)  console.log(`24 * 24 = ${squareRes}`)})
```

```js
// add.jsdefine(function(require, exports, module) {  console.log('AddModule is called!!!')  module.exports = {    add(a, b) {      return a + b    }  }})
```

```js
// square.jsdefine(function(require, exports, module) {  console.log('SquareModule is called!!!')  const multiplyModule = require('./multiply')  module.exports = {    square(a) {      return multiplyModule.multiply(a)    }  }})
```

```js
// multiply.jsdefine(function(require, exports, module) {  console.log('MultiplyModule is called!!!')  module.exports = {    multiply(a) {      return a * a    }  }})
```

结果：

```js
AddModule is called!!!12 + 23 = 35SquareModule is called!!!MultiplyModule is called!!!24 * 24 = 576
```

总结：

- CMD 规范也是定义了一个全局函数 `define`，这个 `define` 函数需要传入一个回调函数，该回调函数需要有三个参数 `require`、`exports`、`module`。在回调函数中通过 `require` 引入模块，通过 `exports` 和 `module` 导出模块，用法和 `CommonJS` 相同。

---

### CommonJS

首先，了解 `CommonJS` 的使用方法。

项目目录：

```js
CommonJS/	main.js // 入口文件，依赖了 add.js 和 square.js	add.js // 导出功能函数	square.js // 依赖了 multiply.js	multiply.js // 导出功能函数
```

代码：

```js
// main.jsconst addModule = require('./add')const addRes = addModule.add(12, 23)console.log(`12 + 23 = ${addRes}`)const squareModule = require('./square')const squareRes = squareModule.square(24)console.log(`24 * 24 = ${squareRes}`)
```

```js
// add.jsconsole.log('AddModule is called!!!')module.exports = {  add(a, b) {    return a + b  }}
```

```js
// square.jsconsole.log('SquareModule is called!!!')const multiplyModule = require('./multiply')module.exports = {  square(a) {    return multiplyModule.multiply(a)  }}
```

```js
// multiply.jsconsole.log('MultiplyModule is called!!!')module.exports = {  multiply(a) {    return a * a  }}
```

结果：

```js
AddModule is called!!!
12 + 23 = 35
SquareModule is called!!!
MultiplyModule is called!!!
24 * 24 = 576
```

总结：

- 用法和 `CMD` 规范类似。

---

### ES Module

首先，了解 `ES Module` 的使用方法。

项目目录：

```js
ES Module/
    index.html
	entry.js // 入口文件，依赖了 add.js 和 square.js
	add.js // 导出功能函数
	square.js // 依赖了 multiply.js
	multiply.js // 导出功能函数
```

代码：

```html
<!-- index.html --><body>  <h1>ES Module</h1>  <script src="./entry.js" type="module"></script></body>
```

注意：

- 使用 ES6 Module 时，需要将 script 标签的 type 设置为 module；
- 同时，需要使用 `http-server` 在项目目录下开启一个 http 服务，否则会出现跨域错误。

```js
// entry.jsimport { add } from './add.js'import { square } from './square.js'const addRes = add(11, 22)console.log(`11 + 22 = ${addRes}`)const squareRes = square(15)console.log(`15 * 15 = ${squareRes}`)
```

```js
// add.jsconsole.log('AddModule is called!!!')export function add(a, b) {  return a + b}
```

```js
// square.jsconsole.log('SquareModule is called!!!')import { multiply } from './multiply.js'export function square(a) {  return multiply(a)}
```

```js
// multiply.jsconsole.log('MultiplyModule is called!!!')export function multiply(a) {  return a * a}
```

结果：

```js
AddModule is called!!!MultiplyModule is called!!!SquareModule is called!!!11 + 22 = 3315 * 15 = 225
```

---

### 四种方式区别

#### AMD与CMD

- AMD 推崇 **依赖前置**，而 CMD 推崇 **依赖就近**。

  ```js
  // AMD 中的 main.js// 依赖必须提前写在数组中require(['./add', './square'], function(addModule, squareModule) {  const addRes = addModule.add(11, 22)  console.log(`11 + 22 = ${addRes}`)  const squareRes = squareModule.square(15)  console.log(`15 * 15 = ${squareRes}`)})// CMD 中的 main.jsdefine(function(require, exports, module) {  const addModule = require('./add')  const addRes = addModule.add(12, 23)  console.log(`12 + 23 = ${addRes}`)  // 依赖可以就近书写  const squareModule = require('./square')  const squareRes = squareModule.square(24)  console.log(`24 * 24 = ${squareRes}`)})
  ```

- AMD 是将需要使用的模块先加载完再执行代码，而 CMD 是在 require 的时候才去加载模块文件，加载完再接着执行。

  ```js
  // AMD 中的结果（模块先全部加载完）AddModule is called!!!MultiplyModule is called!!!SquareModule is called!!!11 + 22 = 3315 * 15 = 225// CMD 中的结果（模块在 require 时才加载）AddModule is called!!!12 + 23 = 35SquareModule is called!!!MultiplyModule is called!!!24 * 24 = 576
  ```

  

#### CommonJS与AMD

- CommonJS 是同步加载模块，只有加载完模块，才能执行后面的操作。由于 Node.js 主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以 CommonJS 规范比较适用。
- AMD 是异步加载模块，允许指定回调函数。如果是浏览器环境，要从服务器端加载模块，这时就必须采用异步模式，因此浏览器端一般采用 AMD 规范。

#### CommonJS与ES Module

- CommonJS 模块输出的是一个值的浅拷贝，ES6 模块输出的是值的引用。

  做个测试：

  ```js
  // counter.js
  let counter = 1 // 基本数据类型
  let info = { // 引用数据类型
    name: 'Tom',
    age: 18,
    hobby: ['basketball']
  }
  
  const increase = function() {
    counter++
  }
  const grow = function() {
    info.age++
  }
  
  module.exports = {
    counter,
    increase,
    info,
    grow
  }
  
  // main.js
  const { counter, increase, info, grow } = require('./counter')
  
  console.log(counter) // 1
  increase()
  console.log(counter) // 1
  
  console.log(info) // { name: 'Tom', age: 18, hobby: [ 'basketball' ] }
  grow()
  console.log(info) // { name: 'Tom', age: 19, hobby: [ 'basketball' ] }
  
  // 可以发现，CommonJS 的模块输出其实是一个值的浅拷贝，当值为基本数据类型时，外部文件无法修改模块内部的数据，而值为引用数据类型时，导出的其实是这个数据的内存地址，可以修改。
  ```

  ```js
  // counter.js
  let counter = 1
  let info = {
    name: 'Tom',
    age: 18,
    hobby: ['basketball']
  }
  
  const increase = function() {
    counter++
  }
  const grow = function() {
    info.age++
  }
  
  export {
    counter,
    increase,
    info,
    grow
  }
  
  // main.js
  console.log(counter) // 1
  increase()
  console.log(counter) // 2
  
  console.log(info) // { name: 'Tom', age: 18, hobby: [ 'basketball' ] }
  grow()
  console.log(info) // { name: 'Tom', age: 19, hobby: [ 'basketball' ] }
  
  // ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令 import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用,去被加载的那个模块里面去取值。因此，ES6 模块是动态引用，不会缓存值。
  ```

- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

  ```js
  // CommonJS模块
  let { stat, exists, readFile } = require('fs')
   
  // 等同于
  let _fs = require('fs')
  let stat = _fs.stat
  let exists = _fs.exists
  let readfile = _fs.readfile
  ```

  上面代码的实质是整体加载 `fs` 模块（即加载 `fs` 的所有方法），生成一个对象（ `_fs` ），然后再从这个对象上面读取 3 个方法。这种加载称为 `运行时加载` 或者 `动态加载`，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

  ```js
  import { stat, exists, readFile } from 'fs';
  ```

  上面代码的实质是从 `fs` 模块加载 3 个方法，其他方法不加载。这种加载称为 `编译时加载` 或者 `静态加载`，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。

  注意，`编译时加载(静态加载)` 的模块必须在编译时导入，不能在运行时导入，比如下面这种情况是不允许的：

  ```js
  function test() {
      import { testA } from './test.js' // 报错
  }
  test()
  testA()
  ```

  

---

### 实现代码

- https://github.com/load-more/frontend-interview/tree/main/03.JavaScript/Modularity

---

### 参考资料

- https://juejin.cn/post/6844903712553435149
- https://juejin.cn/post/6844903576309858318
- https://juejin.cn/post/6844904056847073293

