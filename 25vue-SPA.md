---
title: 25 Vue 单页Web应用（SPA）
date: 2021-03-05 19:54:13
tags: [Vuejs, Webpack, Loader]
categories: Vuejs
---

## webpack

webpack 是一个前端资源加载和打包工具。

它可以将各种资源（JS、CSS样式、图片等）作为模块来使用，然后将这些模块按照一定规则进行打包处理，从而生成相应的静态资源。

### 1.1基本使用

- 创建一个文件夹 testWebpack，在这个文件夹下创建两个文件：entry.js 和 index.html。entry.js 为项目的入口文件。

- 在 entry.js 中写入 `document.write('hello webpack')`

- 此时使用 webpack 命令进行打包处理

  ```shell
  webpack ./entry.js -o bundle --mode=development
  ```

- 之后，会在当前目录下生成一个 bundle 文件夹，里面有一个 main.js 文件，这就是打包后的文件

- 在 index.html 中利用 <script> 引用这个文件，然后用浏览器打开 index.html

- 可以发现，浏览器执行了 `document.write('hello webpack')` 这个命令

- 另外，如果再创建一个 module.js 文件，在文件内利用 `module.exports = ` 的方式导出文件

- 在 main.js 这个文件中利用 `require("./module.js")` 加载这个导出，然后再用命令打包

- 可以发现，依然可以执行文件

通过上述过程可以看出，webpack 从入口文件开始对所有依赖文件（通过import或require引入的其他文件）进行打包，webpack 会解析依赖的文件，然后将内容输出到 bundle 中的 main.js 中

## loader

loader 是基于 webpack 的加载器。

webpack 本身只能处理 JavaScript 模块，如果要处理其他类型的模块（文件），就需要使用 loader（加载器）进行转换。

### 2.1加载CSS

如果要在应用添加CSS文件，就需要使用 css-loader 和 style-loader 加载器。

css-loader 用于加载 CSS 文件，而 style-loader 会将原来的 CSS 代码插入页面中的一个 <style> 标签中。

安装：

```shell
npm install css-loader style-loader --save-dev
```

- 在 testWebpack 中创建一个 style.css 文件，里面写入一些样式，如：

  ```css
  body {
      font-size: 32px;
      color: red;
  }
  ```

- 然后在 entry.js 中加载 style.css 文件

  ```javascript
  require("!style-loader!css-loader!./style.css")
  ```

- 再次打包文件，执行文件可以发现，字体发生了变化

### 2.2webpack配置文件

在使用 webpack 进行打包操作时，除了在命令行传入参数外，还可以通过指定的配置文件来执行。

将一些编译选项放在一个配置文件中，以便于集中管理。

在根目录下创建一个配置文件 webpack.config.js，该文件的配置选项需要通过 module.exports 导出。

```javascript
module.exports = {
    // configure options
}
```

常用配置选项：

- mode

  - webpack 4 以上版本提供该选项
  - 用于配置开发项目使用的模式，根据指定的模式选择使用相应的内置优化
  - 可能的值：production（默认）、development、none
    - production
      - 生产模式，使用该模式打包时，webpack 会自动启用 JS Tree Sharking 和文件压缩
    - development
      - 开发模式，使用该模式打包时，webpack 会启用 NamedChunksPlugin 和 NamedModulesPlugin 插件
    - none
      - 使用该模式，webpack 不会使用任何内置优化

- entry

  - 配置打包的入口文件
  - 指定的路径为相对该配置文件所在文件夹的路径

- output

  - 配置输出信息
  - output.path 指定打包后的文件路径
  - output.filename 指定打包后的文件名

- module

  - 配置加载的模块
  - module.rules 指定规则数组，这些规则可以对模块应用加载器
  - 规则是一个对象，该对象有以下 几个常用属性
    - test
      - 该属性值为一个正则表达式
      - webpack通过它去匹配相应的文件，通常用来匹配文件的后缀名
    - exclude
      - 指定不被加载器处理的文件
    - include
      - 该属性值通常是一个路径数组，这些路径会被加载器处理
    - use
      - 该属性用于指定应用 test 属性匹配到的文件对应的加载器，是一个数组

- plugins

  - 配置使用的插件

  - 使用插件可以实现一些 loader 不能完成的任务

  - webpack 自带了一些内置的插件

  - 要使用某个插件，需要通过 npm 对其进行安装，然后在 webpack.config.js 的plugins 选项中添加该插件的一个实例

  - 例如，配置插件 HtmlWebpackPlugin（该插件可以帮助生成最终的HTML文件，在这个文件中自动引用了打包后的 JS 文件，而不需要在HTML中手动添加生成的文件）

    安装插件：

    ```shell
    npm install html-webpack-plugin --save-dev
    ```

    在配置文件中进行引入并使用：

    ```javascript
    let htmlWebpackPlugin = require('html-webpack-plugin')	// 引入插件
    module.exports = {
        plugins: [
            new htmlWebpackPlugin()	// 使用插件
        ]
    }
    ```

结合上面介绍的，对之前的打包过程进行修改：

- 首先，在根目录中创建配置文件 webpack.config.js，进行配置：

  ```javascript
  let htmlWebpackPlugin = require('html-webpack-plugin')
  
  module.exports = {
    mode: 'development',
  
    entry: './entry.js',
    
    output: {
      path: __dirname + '/dist',
      filename: 'bundle.js'
    },
  
    module: {
      rules: [
        {
          test: /\.css$/,   
          use: ['css-loader', 'style-loader']
        }
      ]
    },
  
    plugins: [
      new htmlWebpackPlugin()
    ]
  
  }
  ```

- 在终端中，不传入参数，直接调用 webpack 命令

  ```shell
  webpack
  ```

- 打包完成后，在项目根目录下会自动生成 dist 文件夹，该文件夹下自动生成 bundle.js 和 index.html 文件

- 其中 index.html 自动引用了 bundle.js

### 2.3加载图片文件

在应用中加载图片文件需要使用 file-loader 加载器。

安装：

```shell
npm install file-loader --save-dev
```

修改 style.css：

```css
body {
  font-size: 36px;
  color: red;
  background: url(image/test.jpg) ;
}
```

修改 webpack.config.js：

```javascript
let htmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'development',

  entry: './entry.js',
  
  output: {
    path: __dirname + '/dist',
    filename: 'bundle.js'
  },

  module: {
    rules: [
      {
        test: /\.css$/,   
        use: ['css-loader', 'style-loader']
      },
      {
        test: /\.(jpg|png|gif)&/,
        use: ['file-loader']
      }
    ]
  },

  plugins: [
    new htmlWebpackPlugin()
  ]

}
```

重新打包

## 单文件组件

早期编写一个组件时，通常会将一个组件的HTML、Javascript和CSS放在三个不同的文件里，再用编译工具整合到一起，这样非常不利于后期维护。

有了 webpack 和 loader 以后，可以将一个组件的 HTML、Javascript 和 CSS 用各自的标签写在一个文件里，文件扩展名为 .vue。

这样的文件即为单文件组件。

webpack 和 loader 会将单文件组件中的三部分代码分别编译成可执行的代码。

>处理 .vue 文件需要用到 vue-loader 加载器和 vue-template-compiler 工具

使用单文件组件，示例：

- 创建项目文件夹 myapp

- `npm init -y`

- `npm install vue`

- `npm install webpack --save-dev`

- `npm install vue-loader vue-template-compiler css-loader style-loader html-webpack-plugin --save-dev`

- 创建一个 src 文件夹，里面创建 Demo.vue 文件，写入代码：

  ```vue
  <template>
    <div>
      <p>{{msg}}</p>
    </div>
  </template>
  
  <script>
  export default {
    name: "name",
    data () {
      return {
        msg: 'Hello Vue.js!'
      }
    }
  }
  </script>
    
  <style scoped>
    p {
      font-size: 33px;
      color: red;
    }
  </style>
  
  ```

  >默认情况下，单文件组件中的 CSS 样式为全局样式。
  >
  >如果需要使 CSS 样式仅在当前组件中生效，需要设置 <style> 标签的 scope 属性

- 在 src 文件夹下创建 main.js 文件，作为入口文件，写入代码：

  ```javascript
  import Vue from 'vue'
  import Demo from './Demo.vue'

  new Vue({
    render: h => h(Demo)
  }).$mount('#app')
  ```
  
- 配置 webpack.config.js

  ```javascript
  let HtmlWebpackPlugin = require('html-webpack-plugin')
  let VueLoaderPlugin = require('vue-loader/lib/plugin')
  
  module.exports = {
    mode: 'development',
  
    entry: './src/main.js',
  
    output: {
      path: __dirname + 'dist',
      filename: 'bundle.js'
    },
  
    module: {
      rules: [
        {
          test: /\.css$/,
          use: ['style.loader', 'css.loader']
        },
        {
          test: /\.vue$/,
          use: ['vue-loader']
        }
      ]
    },
  
    plugins: [
      new HtmlWebpackPlugin(),
      new VueLoaderPlugin()
    ]
  }
  ```

- webpack 打包

## 项目目录结构

使用 Vue.js 开发较大应用时，需要考虑项目的目录结构、配置文件和项目所需的各种依赖等方面。

如果手动完成这些配置工作，效率会非常低。

为此，官方提供了一款脚手架生成工具 @vue/cli，通过该工具可以快速构建项目。

### 3.1安装

```shell
npm install -g @vue/cli
vue --version
```

> @vue/cli 需要 Node.js 8.9及以上版本

### 3.2创建项目

使用 @vue/cli 可以快速生成一个基于 webpack 构建的项目。

```shell
vue create my-project
```

按照提示，完成初始化配置

。。。

