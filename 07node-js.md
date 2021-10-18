---
title: 07 node.js学习之路（三）
date: 2021-02-22 16:50:01
tags: [Nodejs, CommonJS, npm]
categories: Nodejs
---

## node.js中的模块系统

> JS 本身并不支持模块化，在 Node 环境中，进行了特殊的模块化支持（CommonJS），除此之外还有第三方库支持模块化，例如 `require.js`(AMD) 、`sea.js` (CMD)。另外 `<script>` 标签也可以进行引用加载，但必须考虑加载顺序的问题。
>
> 为了解决这一问题，官方在2015年发布了 EcmaScript 2016 官方标准，其中就包含了对模块化的支持。
>
> 虽然标准发布了，但很多 JavaScript 运行环境还不支持，所以会利用编译器工具打包使其可以在低版本浏览器中运行。
>
> 例如，less -> 编译器 -> css         EcmaScript 6 -> 编译器 -> EcmaScript 5
>
> 使用新技术就是为了提高效率，增加可维护性

node.js 编写程序主要在使用：

- EcmaScript
  - 和浏览器中的 JavaScript 不一样，在 node.js 中没有 BOM、DOM

- 核心模块
  - 文件操作的 fs
  - http 服务的 http
  - url 路径操作的 url
  - 路径处理模块的 path
  - 操作系统信息的 os
- 第三方模块
  - art-template
- 用户写的模块
  - 自己创建的文件

### CommonJS 模块规范

1. 使用 require 方法来加载模块
2. 使用 exports 接口对象来导出模块中的成员

### require 加载模块

```javascript
let m = require('./test.js')
```

### exports 导出模块

- 导出多个成员

  ```javascript
  exports.a = 'Hello World'
  exports.b = function (a, b) {
      return a + b
  } 
  ```

- 导出单个成员

  ```javascript
  module.exports = 'Hello'
  
  // 注意，这种导出方法，后面的导出会覆盖前面的导出
  module.exports = function (a, b) {
      return a + b
  }
  ```

### exports 和 module.exports 的区别

可以这样想，在每个文件内部都有 module 这个变量，module里面有一个属性 exports，包含要导出的变量

```javascript
var module = {
    exports: { }
}

...

return module.exports
```

文件最后，都会返回 module.exports 

为了简化书写，规定可以使用 exports 代替 module.exports

```javascript
console.log(exports === module.exports)  // true
```

这里的 module.exports 相当于一个变量，然后再将 module.exports 这个变量赋值给 exports，这样exports 和 module.exports 就指向了同一块内存地址。当给 exports 对象添加或修改属性时，都会影响到 module.exports，反之修改 module.exports 时，也会影响到 exports。

注意，当给 exports 或者 module.exports 重新赋值时，这两个对象将不再指向同一块地址，它们的链接将断开。

```javascript
exports.a = 'aaa'	// 等效于 module.exports.a = 'aaa'
module.exports.b = 'bbb' // 等效于 exports.b = 'bbb'

exports = {a: 'aaa'}  // module.exports 和 exports 将断开链接，exports 不再会影响到 module.exports
module.exports = {b: 'bbb'}	// 由于最后返回的都是 module.exports，所以无论前面做什么操作，这步操作都会影							// 响到最后结果，这也就是为什么使用 exports 导出多个成员，使用                                          // module.exports 导出单个成员
```

### require 优先从缓存加载

{% asset_img Snipaste_2021-02-22_21-01-04.png %}

### require 加载规则

模块查找机制（顺序）：

  1. 优先从缓存加载

  2. 加载核心模块

    - 核心模块的本质也是加载文件
    - 核心模块文件已经被编译到二进制文件中，我们只需要按照核心模块的名称加载即可
    - 如 `var fs = require('fs')`

  3. 加载路径形式的文件模块

    - `./` 		当前目录
    - `../`       上一级目录
    - `/xxx`      当前文件所属磁盘根目录（几乎不用）
    - `d:/xxx`   绝对路径（几乎不用）

  4. 加载第三方模块

    以加载第三方模块 art-template 为例，以下是加载这个模块的顺序：
    
      1. 先找到当前文件所处路径下的 node_modules 目录
    
      2. node_modules/art-template
    
      3. node_modules/art-template/package.json
    
      4. package.json 文件里的 main 属性
    
      5. main 属性记录了 art-template 的入口文件 index.js
    
      6. 然后在 index.js 中加载使用这个第三方模块，实际上最终加载的还是文件
    
        ---
    
      7. 如果 package-json 文件不存在或者 main 指定的入口模块没有，则会自动查找该目录下的 index.js，也就是说 index.js 会作为一个默认备选项
    
      8. 如果上述条件都不存在，则会进入到上一级目录(注意不会在兄弟目录中查找)查找 node_modules，重复之前的操作......
    
      9. 如果直到当前磁盘的根目录还找不到，则报错：can not find module xxx

### 文件操作路径和模块标识路径问题

- 文件操作路径

  ```javascript
  // 在文件操作路径中
  // 	 	./data/a.txt	相对于当前目录
  //		data/a.txt		相对于当前目录
  //		/data/a.txt		绝对路径，当前文件所处磁盘根目录
  //		C:/xx/xx		绝对路径
  
  ```

- 模块操作路径

  ```javascript
  // 这里如果忽略 . , 则也是磁盘根目录
  require('/data/foo.js')
  
  // 相对路径
  require('./data/foo.js')
  
  // 模块加载的路径不能省略 ./
  // require('data/foo.js')  这种写法是错误的
  ```

**注意，所有文件操作的 API 都是异步的，也就是关于文件操作的代码会放到最后执行**



## npm (node package manager)

### 引言

我们开发项目时，通常会安装许多第三方模块，这些第三方模块都放在 node_modules 文件夹下，如果我们不小心将这个文件夹删除了，那么要找回这些模块是十分困难的。

所以，我们会在项目的根目录下创建一个 package.json 文件，用于存放我们依赖的模块信息，方便我们查询。

### 创建 package.json

```javascript
npm init
```

按照提示输入内容：

```javascript
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help init` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (testnpminit) demo
version: (1.0.0) 0.0.1
description: 测试npm init
entry point: (index.js) main.js
test command:
git repository:
keywords:
author: loadmore
license: (ISC)
About to write to E:\Web Front End\Node.js\testNpmInit\package.json:

{
  "name": "demo",
  "version": "0.0.1",
  "description": "测试npm init",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "loadmore",
  "license": "ISC"
}


Is this OK? (yes) yes
```

### 安装第三方模块

```javascript
npm install art-template --save
```

`--save` 用于保存依赖项信息

执行上述命令安装好模块之后，package.json 文件中会增加 dependencies 这个选项，里面保存了安装模块的名称和版本信息。

如果 node_modules 不小心被删除了，我们可以直接通过 `npm install` 命令，自动把 package.json 中的 dependencies 中的所有依赖项全部下载回来。

- 建议每个项目的根目录下都有一个 package.json 文件
- 建议 npm install 的时候加上 --save 这个选项，用于保存依赖项信息

### npm

- npm 内涵
  - npm 网站

    [npmjs](www.npmjs.com)

  - npm 命令行工具

    下载了node，就有npm

    npm也有版本信息，通过 `npm --version` 查看

- npm 常用命令

  ```javascript
  npm init
  
  npm init -y			// 初始化跳过向导，快速生成
  
  npm install
  
  npm install <packageName>
      
  npm install <packageName> --save	// 安装模块并保留依赖项信息
  
  
  ```

  - npm init

    - npm init --yes	跳过向导初始化，快速生成 package.json
  - npm init -y
  
- npm install
  
    - 将 package.json 中 dependencies 中的所有依赖项全部安装
  - npm i
  
- npm install <packageName> ...
  
    - 安装模块，可接多个模块名，以空格连接
  - npm i <packageName> ...
  
- npm install <packageName> --save
  
    - 安装模块并保存依赖项信息
  - npm i <packageName> -S
  
- npm uninstall <packageName>
  
    - 删除模块，但依然保留依赖信息
  - npm un <packageName>
  
- npm uninstall <packageName> --save
  
    - 删除模块和依赖项信息
  - npm un <packageName> -S
  
- npm --help
  
  - 查看帮助
  
- npm <command> --help
  
  - 查看指定命令的用法
  

### 解决 npm 下载速度慢

- 使用 cnpm

  ```javascript
  npm install cnpm --global	// 全局安装 cnpm
  	
  cnpm install jquery		// 使用 cnpm 代替 npm
  ```

- 继续使用 npm

  ```javascript
  npm config set registry http://registry.npm.taobao.org	// 默认使用淘宝镜像
  
  npm config list 	// 查看配置信息是否配置成功
  
  npm install jquery  // 继续使用 npm 安装，但是通过淘宝镜像下载的
  ```


### package-lock.json

- npm 5 以前没有 `package-lock.json` 这个文件，这是在 npm 5 之后加入的

- 当安装好包之后，npm 会生成或者更新 `package-lock.json`
  - npm 5 以后，安装包不需要加 `--save`，会自动保存依赖信息
  - `package-lock.json` 会保存 `node_modules` 中所有包的信息（版本、下载地址等）
    - 这样重新 `npm install` 时速度可以提升
  - `lock` 意为 “锁”
    - 用来锁定版本
    - 重新 `npm install` 时，不会下载最新版本，而是下载锁定的版本号