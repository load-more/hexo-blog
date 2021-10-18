---
title: 73 学习 Webpack
date: 2021-07-09 14:50:28
tags: [Webpack, 笔记]
categories: Webpack
---

最近，花了两周的时间学习 Webpack 的知识，由于知识繁杂，无从下手，遂从慕课网上找了一份视频学习，获益匪浅。

课程：《手把手带你掌握新版webpack4.0》（老师讲的十分细致）

<!-- more -->

## 课程简介

知识点：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210701193828.png)

## Webpack初探

### 引言

当我们使用**面向对象**编程时，如何导入其他文件的依赖？

![](https://gitee.com/gainmore/imglib/raw/master/img/20210701202106.png)

我们可能会想到用上面的 `ES Module` 的方法进行模块的导入导出，但是文件放到浏览器上却无法执行！

**因为浏览器本身根本无法识别这种模块关系！**

为了解决这一问题，webpack 应运而生。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210701202630.png)

根据上图，大致步骤和之前的一样，只不过在中间部分加入了`wepback`的打包处理；

`webpack` 将模块的依赖关系转换成浏览器能够理解的语言，使得文件能够正常执行。

### Webpack的定义

那么，`webpack`究竟是什么？

其实，`webpack` 就是一个**模块打包器**（**Module Bundler**），能够将模块之间的依赖关系转换成易被识别的语言，它能处理 `ES Module`、`CommonJS`、`AMD`、`CMD`等多种模块引用方法。

最初，`webpack` 只是用于打包 `JS`，但发展到后面，已经可以打包各式各样的文件，比如 `CSS`，`PNG`、`JPG`等等。

### Webpack安装

#### 安装Nodejs

首先，需要安装 `nodejs` 和 `npm`，在官网下载安装即可。

注意：`nodejs` 版本尽量要新，因为会影响到 `webpack` 的打包速度，一般来说，`nodejs`版本越新，`webpack`打包速度越快。

#### 安装Webpack

**全局安装：**

`npm install webpack@4 webpack-cli -g`

**注意：一定要安装 `webpack 4`，否则之后的很多插件会不兼容**

一般不推荐项目使用全局安装的 `webpack`，因为电脑上可能会有多个项目，每个项目中所用的 `webpack` 版本可能不会，这就会导致有的项目无法运行。因此，推荐使用局部安装。

**局部安装：**

1. `npm init -y` 生成 `package.json`
2. `npm install webpack webpack-cli -D`  将 `webpack` 安装到项目的开发环境中
3. 如果要安装其他版本的 `webpack`，可以使用 `npm info webpack` 去查看 `webpack` 的所有版本，然后安装指定版本即可，如 `npm install webpack@4.25.1 -D`
4. 由于此时 `webpack` 处于局部环境中，所以使用 `webpack -v` 无法查询到局部安装的 `webpack` 版本号；此时，可以通过 `npx webpack -v` 去查询项目中使用的版本号，`npx` 是 `npm` 提供的一个工具，它可以用于查询在当前目录下处于 `node_modules` 中的第三方包的信息。

**注意：如果在命令行中直接输入 `webpack` 打包，使用的是全局安装的 `webpack`，要想使用局部安装的 `webpack` 打包，就需要使用 `npx webpack`**

#### Webpack的配置文件

- 指定配置文件

  在命令行输入 `npx webpack`，会默认使用当前目录下的 `webpack.config.js` 中的配置打包；

  要指定其他名称的配置文件，比如 `webpack.conf.js`, 可以使用命令：`npx webpack --config webpack.conf.js `

- 使用 `npm run build`

  我们可以使用 `npx webpack` 执行 `webpack`，当然更常用的是指定 `npm run build`；

  我们需要修改 `package.json` 文件，将 `scripts` 修改成指定的命令即可，比如：

  ```js
  "scripts": {
      "build": "webpack" // 会先到项目的node_modules中查找wepack
  }
  ```

  实际上，`npm run build` 就是执行了 `webpack`

- `mode`

  `webpack.config.js` 中需要设置 `mode` 值，可选值有 `production || development || none`

  - `production (默认)`

    生产环境，打包文件代码会被压缩成一行

  - `development`

    开发环境，打包文件代码不会被压缩

#### 小结

以上，介绍了三种启动 `webpack` 的方法：

1. 全局安装 `webpack`，启动：`webpack`
2. 局部安装 `webpack`，启动：`npx webpack`
3. 项目启动 `webpack`：`npm run build`

上面三种方式都是通过命令行启动，而我们安装的 `webpack-cli` 的作用就是使我们可以通过命令行启动 `webpack`

## Webpack核心概念

### loader

我们通过JS模块的导出导入，可以使用 `webpack` 将其打包成功，但如果是其他格式的文件呢？比如 css 文件或是图片？

这里我们尝试往 `JS` 文件导入一张 `JPG` 格式的图片，然后用 `npm run build` 打包。

```javascript
import bar1 from './bar1'
import bar2 from './bar2'
const cat = require('./cat.jpg')

bar1()
bar2()
console.log(cat);
```

但是却出错了：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210701232046.png)

这是因为 `webpack` 在设计之初就是为了打包 `JS` 文件的，所以它本来就支持打包 `JS` 文件，但是其他格式的文件就需要使用 `loader` 来进行转换了。

通常，对于某一类文件，都有其对应的 `loader` 进行转换，比如像 `jpg` 、`png` 等这类图片格式，需要用 `file-loader` 进行转换，对于 `vue` 文件，需要使用到 `vue-loader`。

所以，需要通过 `npm install xxx-loader -D` 安装 `loader`。

然后，就是修改配置文件：

```javascript
// webpack.config.js
module: { // 解析其他模块（非JS模块）
    rules: [ // 指定规则
        {
            test: /\.jpg$/, // 正则匹配文件
            use: [
                {
                    loader: 'file-loader' // 使用loader
                }
            ]
        }
    ]
}
```

打包流程：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210701233222.png)

**总结：**

由于 `webpack` 不能识别导入的非 `.js` 文件，所以需要 `loader` 将文件进行转换，使其能够识别。

#### 打包图片

##### file-loader

安装：`npm install file-loader -D`

```javascript
// webpack.config.js
module.exports = {
  mode: 'development',
  entry: {
    index: './src/index.js'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.(jpg|png|gif)$/, // 匹配图片
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[ext]', // placeholder 占位符
              outputPath: 'img' // 输出路径，基于上面的output
            }
          }
        ]
      }
    ]
  }
}
```

##### url-loader

安装：`npm install url-loader -D`

用法几乎和 `file-loader` 一样

```javascript
module.exports = {
  mode: 'development',
  entry: {
    index: './src/index.js'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.(jpg|png|gif)$/, // 匹配图片
        use: [
          {
            loader: 'url-loader',
            options: {
              name: '[name].[ext]', // placeholder 占位符
            }
          }
        ]
      }
    ]
  }
}
```

不同的是，`file-loader` 会生成图片的副本到指定的 `outputPath` 下，但 `url-loader` 却不会，而是会在打包文件中将图片转换成 `base64` 储存起来。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210702102546.png)

另外，`url-loader` 中 `options` 还有一个 `limit` 属性。当图片的大小大于 `limit` 时，将会采用 `file-loader` 的方式，将图片的副本放在 `outputPath` 下；当图片大小小于 `limit` 时，则将图片转换成 `base64`。

```js
module.exports = {
  mode: 'development',
  entry: {
    index: './src/index.js'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.(jpg|png|gif)$/, // 匹配图片
        use: [
          {
            loader: 'url-loader',
            options: {
              name: '[name].[ext]', // placeholder 占位符
              outputPath: 'img', // 输出路径，基于上面的output
              limit: 10240, // 10kb
            }
          }
        ]
      }
    ]
  }
}
```

#### 打包样式

我们在项目中引入一个样式文件：

```js
// index.css
img {
  width: 100px;
  height: 100px;
}

// index.js
import cat from './cat.jpg'
import './index.css'

// 加入图片到#root
var root = document.getElementById('root')
var img = new Image()
img.src = cat
root.append(img)

// 添加样式
root.classList.add('img')
```

当然，肯定不能打包成功，因为现在还没有添加 `loader`，`webpack` 无法识别 `.css` 文件。

一般打包样式文件，会涉及到两个必不可少的 `loader`：`css-loader` 和 `style-loader`

##### css-loader

`css-loader` 用于让 `webpack` 识别到 `.css` 文件，并处理 `.css` 文件导出导入之间的模块依赖关系。

比如 `import './index.css'` ，有了 `css-loader`，可以让 `webpack` 解析到 `.css` 文件，并导入进来。

##### style-loader

单单有 `css-loader` 还不够，因为只是将 `css` 解析了出来，还没有插入到 `html` 中。

所以需要使用 `style-loader`，它的作用是，将通过 `css-loader` 解析出的 `css` 语句插入到 `style` 标签中，然后将整个 `<style>...</style>` 插入到 `<head></head>` 中。

这样，样式就可以生效了。

##### sass-loader + node-sass

当使用到预处理器，比如 `sass` 、`less`时，就需要添加对应的 `loader`。

处理 `sass` ，需要使用到 `sass-loader`，为此需要安装 `sass-loader 和 node-sass`：

`npm install sass-loader node-sass -D`

配置：

```js
// webpack.config.js
...
{
    test: /\.scss$/,
        use: [
            'style-loader',
            'css-loader',
            'sass-loader'
        ]
}
...
```

**注意： `loader` 的加载顺序是从下到上（或者从右到左），所以顺序不能调换。**

依照上面的顺序，先使用 `sass-loader` 将 `scss` 文件的内容转换成 `css` 语句；然后通过 `css-loader` 将 `css` 语句转换成 `webpack` 能够识别的语句，并处理模块依赖；最后使用 `style-loader` 将 `css` 语句插入到 `head` 标签中的 `style` 标签中，使得样式生效。

##### postcss-loader

对于一些 `css` 属性，我们希望它带上厂商前缀，比如：`-webkit-transform`，这时需要使用到 `postcss-loader` 这个 `loader`，同时需要 `autoprefixer` 这个插件。

`npm install postcss-loader autoprefixer -D`

配置：

`webpack.config.js:`

```js
      {        test: /\.scss$/,        use: [          'style-loader',          'css-loader',          'sass-loader',          'postcss-loader',        ]      }
```

`postcss.config.js`

```js
module.exports = {  plugins: [    require('autoprefixer')  ]}
```

##### importLoaders

这是 `css-loader` 中 `options` 的一个选项，用于：`启用/禁用或者设置在 css-loader 前应用的 loader 数量`，具体用法可以查看官网：https://webpack.docschina.org/loaders/css-loader/

比如：

```js
{    test: /\.scss$/,        use: [            'style-loader',            {                loader: 'css-loader',                options: {                    importLoaders: 2                }            },            'sass-loader',            'postcss-loader',        ]}
```

由于 `loaders` 是从下到上依次执行的，所以当执行到 `css-loader` 时，可能有多个依赖的 `css` 文件，这时处理这些文件就可能不会用到之前的 `postcss-loader` 和 `sass-loader` 了。所以，当设置了 `importLoaders: 2` 时，此时就会依次执行前面的两个 `loader`。

##### modules: true

这也是 `css-loader` 中的一个配置项，用于启动 `css` 模块化。比如两个 `css` 文件之间进行相互引用，可能会导致样式发生冲突，而设置 `modules: true` ，会让两个文件的样式作用范围变得独立，即使在一个文件中引用了另一个文件的样式，也不会使得这个文件影响到另一个文件的样式。

##### 打包字体

字体文件可能涉及到 `.eot | .ttf | .svg` 等格式，处理这些格式的文件，依然要用到 `file-loader` ，和打包图片类似，直接在配置文件中配置即可。

#### 处理ES6

##### Babel

当我们在项目中引用了 `ES6` 的语法：

```js
const arr = [  new Promise((resolve, reject) => {}),  new Promise((resolve, reject) => {})]arr.map(item => {  console.log(item);})
```

对项目进行打包，但却不会报错。查看打包的文件：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703101139.png)

可以发现，`ES6` 的语法原封不动的出现在了打包文件中，很明显 `ES6` 语法没有被转换。

但是在 `Chrome` 浏览器却能正常执行这些 `ES6` 语句，这是因为 `Chrome` 浏览器已经实现了对 `ES6` 的兼容。在一些低版本的浏览器中，执行这些 `ES6` 语句不可避免的还是会报错。

所以，为了解决这一问题，就提出了一种方法：`将 ES6 转换成 ES5` ，这样不就可以兼容所有浏览器了吗！

而 `Babel` 就是可以实现这一功能的工具。

官网：http://babeljs.io

##### Babel的使用

官网教程：https://babeljs.io/setup#installation

1. 安装：`npm install --save-dev babel-loader @babel/core @babel/preset-env` 

   - 这里的 `babel-loader` 只是从 `ES6` 到 `ES5` 的一个桥梁，它本身不实现转换的功能（转换的功能交给 `@babel/core` 来实现）
   - 这里的 `@babel/core` 是 `babel` 的核心库，它负责将 `ES6语法 -> AST（抽象语法树） -> 能够识别的语法`
   - 这里的 `@babel/preset-env` 包含所有从 `ES6` 到 `ES5` 的转换规则，负责将所有的 `ES6` 语法翻译成 `ES5` 语法。

2. 配置：

   ```js
        // webpack.config.js	...	 {        test: /\.m?js$/,        exclude: /node_modules/,        use: {          loader: 'babel-loader',          options: {            presets: ['@babel/preset-env']          }        }      }
   ```

通过上面的配置，我们重新打包，查看打包文件：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703103855.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703103639.png)

##### Polyfill

可以发现 `const` 已经被转换为 `var` 了，但是 `Promise` 和 `map` 却没有被转换。

这是因为上面的配置只翻译了一部分的 `ES6` 语法，而一些高级函数和方法却没有被翻译，比如 `Promise、map`，为了将这些函数和方法都转换，需要使用到 `Polyfill`。

**安装：**

`npm install @babel/polyfill --save  // 因为要在项目中使用，所以用到--save`

**使用：**

在文件中直接导入即可

```js
import "@babel/polyfill";const arr = [  new Promise((resolve, reject) => {}),  new Promise((resolve, reject) => {})]arr.map(item => {  console.log(item);})
```

然后，重新打包项目即可：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703104650.png)

可以发现打包文件从原来的 `87.7 KiB` 直接增加到了 `992 KiB`。这是因为 `Polyfill` 将**所有 `ES6` 语法**都实现了一遍，然后将代码全部加到了打包文件中，比如 `Promise` 和 `map` ，`Polyfill` 用底层代码实现了这两个方法的功能，然后把底层代码加入到了 `main.js` 中。

而这种方式将所有语法的实现都加到一个文件中，会显得十分冗余，因为有些语法压根没有使用到。

这时候就需要使用到 `按需加载` 了：

```js
      {        test: /\.m?js$/,        exclude: /node_modules/,        use: {          loader: 'babel-loader',          options: {            presets: [              ['@babel/preset-env', {                useBuiltIns: 'usage' // 按需加载              }]            ]          }        }      }
```

其中： `useBuiltIns: 'usage'` 的作用就是：只把使用到的语法加入到打包文件中，即按需加载。

这时候，重新打包项目：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703105510.png)

可以发现，文件小了很多。

另外，`presets` 中还有一个属性 `targets`，它表示的是项目所运行的目标环境，比如：

```js
          options: {            presets: [              ['@babel/preset-env', {                useBuiltIns: 'usage',                targets: {                  chrome: "67"                }              }]            ]          }
```

上面的 `targets` 表示我们的项目将会运行在 `Chrome 67` 上，然后 `Babel` 就会判断该版本的浏览器是否兼容了项目中的部分 `ES6` 语法。因为 `Chrome 67` 支持 `Promise` 和 `map` ，所以 `Babel` 就不会将这两个方法的底层实现代码注入到 `main.js` 中，于是就能够减少打包文件的体积。 

重新打包项目：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703111759.png)

可以发现，体积的确变小了。

##### transform-runtime

以上通过 `Polyfill` 的方式转换 `ES6` ，对于普通的业务代码没什么问题，但对于开发一些框架或是一些第三方库就有不小的问题了。因为在转换过程中，`Polyfill` 会创建一些全局变量，这些全局变量会污染全局环境，这对于框架或类库的开发显然是不可取的。

所以，在开发框架、类库时，需要换一种转换方式了。

安装：

1. `npm install @babel/plugin-transform-runtime -D`
2. `npm install @babel/runtime --save`
3. `npm install @babel/runtime-corejs2 --save`

配置：

```js
      {        test: /\.m?js$/,        exclude: /node_modules/,        use: {          loader: 'babel-loader',          options: {            // 1.开发业务代码时的配置            // presets: [            //   ['@babel/preset-env', {            //     useBuiltIns: 'usage',            //     targets: {            //       chrome: "67"            //     }            //   }]            // ]            // 2.开发框架、类库时的配置            plugins: [              ['@babel/plugin-transform-runtime', {                'corejs': 2,                'helpers': true,                'regenerator': true,                'useESModules': false              }]            ]          }        }      }
```

由于使用 `@babel/plugin-transform-runtime` 会使用闭包的方式转换语法，所以不会创建全局变量，也就不会污染全局环境。

##### .babelrc

由于 `Babel` 配置项会很多，所以可以将 `options` 的内容放到一个文件中（ 名为`.babelrc`的 `JSON` 文件 ），这样当项目打包时，读取 `Babel` 配置时会自动从 `.babelrc` 中读取。

比如：

```json
// .babelrc{  "presets":   [    ["@babel/preset-env", {      "useBuiltIns": "usage",      "targets": {        "chrome": "67"      }    }]  ]}
```

##### 总结

1. 对于处理业务代码：

安装：

```shell
$ npm install babel-loader @babel/core @babel/preset-env -D$ npm install @babel/polyfill --save 
```

配置：

```js
      {        test: /\.m?js$/,        exclude: /node_modules/,        use: {          loader: 'babel-loader',          options: {            // 开发业务代码时的配置            presets: [              ['@babel/preset-env', {                useBuiltIns: 'usage', // 按需加载                targets: { // 目标环境                  chrome: "67"                }              }]            ]          }        }      }
```

2. 对于开发框架或是第三方类库：

安装：

```shell
$ npm install babel-loader @babel/core -D$ npm install @babel/plugin-transform-runtime -D$ npm install @babel/runtime --save$ npm install @babel/runtime-corejs2 --save
```

配置：

```js
      {        test: /\.m?js$/,        exclude: /node_modules/,        use: {          loader: 'babel-loader',          options: {            // 开发框架、类库时的配置            plugins: [              ['@babel/plugin-transform-runtime', {                'corejs': 2,                'helpers': true,                'regenerator': true,                'useESModules': false              }]            ]          }        }      }
```

### plugins

#### 定义

`plugin` 可以在 `webpack` 运行到**某个时刻**的时候，做一些事情。

#### html-webpack-plugin

作用：在打包结束后生成一个 `html` 文件，并将打包生成的 `js` 文件注入到这个 `html` 文件中。

安装：`npm install html-webpack-plugin@4 -D`

**注意：安装版本要是 4**

配置：

```js
  plugins: [    new HtmlWebpackPlugin({      template: './src/index.html' // 指定模板文件，将打包后的js文件注入其中    })  ]
```

#### clean-webpack-plugin

作用：在打包开始前，删除指定目录。

安装：`npm install clean-webpack-plugin -D`

配置：

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')const path = require('path')module.exports = {  ...  output: {    filename: '[name].js',    path: path.resolve(__dirname, 'dist') // 必须要配置输出路径，否则无法通过插件删除目录  },  plugins: [    new HtmlWebpackPlugin({      template: './src/index.html'    }),    new CleanWebpackPlugin(),  ]}
```

### entry output

```js
  entry: {    index1: './src/index.js',    index2: './src/index.js',  },  output: {    publicPath: 'http://cdn.cn', // 注入html中的js的前缀    filename: '[name].js', // 占位符，对应entry的名称    path: path.resolve(__dirname, 'dist')  },
```

### performance

有时，项目打包后的文件体积过大，会提示“影响性能”之类的信息，通过向 `webpack.config.js` 添加 `performance: false` 即可隐藏该信息。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705210050.png)

### devtool

当我们进行代码调试的时候，难免会出现错误，但是当我们运行打包之后的文件，在控制台查找错误的来源，会发现错误提示是根据打包文件的进行定位的。

而在开发过程中，这种错误提示无疑毫无作用，我们想要的是具体到项目中哪个文件、哪一行的错误提示。

`SourceMap` 就是为了解决这一问题。

#### 定义

`SourceMap` 本质上是一种映射关系，是打包文件中的错误到项目文件（源代码）中的错误的一种映射。

比如，打包之后运行项目，控制台报错：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210702163017.png)

这个 `index.js:26` 表明是在打包文件 `index.js` 的第 26 行出现了错误。

而通过 `SourceMap` ，可以将 `index.js:26` 映射到比如 `./src/index.js:20` 的位置上，这样就方便了代码调试。

#### 配置

开启 `SourceMap`，只需要设置配置文件中的 `devtool` 属性即可，但 `devtool` 却有很多可选值，分别代表不同的功能，具体可以查看官网：https://webpack.docschina.org/configuration/devtool/

这里可以解释这些参数分别代表什么作用。

#### source-map

这是开启 `SourceMap` 的基本参数。

设置该参数之后，项目打包完成后会在输出目录下创建一个 `.map` 文件，文件里面存储了从**打包文件代码**到**源代码**的一些映射关系。

#### inline

作用和 `source-map` 相同，只不过不会生成 `.map` 文件，而是将这些映射关系转换成 `base64` 格式，以 `sourceMappingURL` 的形式存储到打包文件中。

#### cheap

有以下两个特点：

1. 错误提示信息不再精确到哪一行的哪一列，而是只提示哪一行出错，提高了打包速度；
2. 只关注业务代码的错误，不关注像 `loader`、`plugin`、第三方模块之类的其他代码的错误。

#### module

和 `cheap` 相比，不仅会关注业务代码的错误，还会关注像 `loader`、`plugin`、第三方模块之类的其他代码的错误。

#### eval

在打包文件中的映射关系不使用 `base64` ，而是使用另一种性能更高的方式存储。

#### 最佳实践

- 开发环境下：

  ```js
  mode: 'development',devtool: 'eval-cheap-module-source-map',
  ```

  这种方式代码出错可以映射到源代码，而且既有良好的打包速度，也有较全的代码提示。

- 生产环境下：

  ```js
  mode: 'production',devtool: 'cheap-module-source-map',
  ```

  这种方式，代码出错不会映射到源代码中，因为在生产环境下。

### devServer

之前，我们修改完代码，都会使用 `npm run build` 重新打包代码，这样做难免有些麻烦。

我们可以使用一些工具，实现 `修改代码后自动打包并重启项目`。

#### --watch

我们可以修改 `package.json` 文件：

```json
"scripts": {    "build": "webpack --watch"},
```

在命令后面加上 `--watch`，这样当我们使用 `npm run build` 打包项目时，就相当于执行了 `webpack --watch`，这样打包过程就处于`监控` 状态，一旦项目中有了代码的修改，就会被检测到，然后重新打包项目。

缺点：

- 虽然能自动打包，但是浏览器还是要手动刷新；
- 启动项目时，是通过文件方式启动的，如果项目需要发送 `Ajax` 请求，就无能为力了。

#### devServer(推荐)

使用 `WebpackDevServer` 可以完美的解决上面的两个问题。

首先，需要重新配置一个启动命令：

```js
// package.json"scripts": {    "build": "webpack --watch",    "start": "webpack-dev-server"}
```

为了使用 `webpack-dev-server`，需要安装这个包：`npm install webpack-dev-server -D`

注意，为了能够使这个包正常运行，需要检查 `webpack-cli` 的版本，`webpack-cli` 需要降到 `3.x.x`：

```shell
$ npm uninstall webpack-cli -D$ npm install webpack-cli@3 -D
```

然后，配置文件：

```js
// webpack.config.js  devServer: {    contentBase: './dist', // 设置服务器访问的基本目录    host: 'localhost', // 设置服务器的ip地址    port: 8090, // 设置服务器的端口，默认为8080    open: true, // 打包完成，自动打开浏览器    proxy: { // 代理，跨域      '/api': 'http://localhost:3000'    }  },
```

特点：

- 不会生成 `dist` 目录，因为 `devServer` 会将打包后的文件都放到内存里，这样就加快了打包速度，因为内存的运算速度很快。

#### server.js

除了上面两种方式外，还可以自己写一个用于自动打包的文件 `server.js`

首先，重新设置一个启动命令：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210702221730.png)

然后，编辑 `server.js`：

```js
const express = require('express')const webpack = require('webpack')const WebpackDevMiddleware = require('webpack-dev-middleware')const config = require('./webpack.config.js')// 在node中使用webpackconst compiler = webpack(config)// 使用express创建一个本地服务器const app = express()// 使用中间件app.use(WebpackDevMiddleware(compiler, {}))app.listen(3000, () => { // 监听端口  console.log('server is running at port 3000...');})
```

最后，使用 `npm run server` 运行即可。

缺点：

1. 麻烦，需要手动编写
2. 浏览器需要手动刷新

### HMR

#### 定义

`HMR(Hot Module Replacement)` 即 “热模块更新”。

当我们使用 `devServer` 或是其他方式实现项目代码变化就自动重新打包的功能时，往往会存在一些不足，比如：**当代码修改之后，浏览器页面会自动刷新，此时所有渲染出的效果全部都重新加载了。** 

这种现象非常不利于代码调试，所以针对这一问题，提出了 `HMR` 的解决方法。

#### 配置

开启热模块更新，需要配置三个参数，如下：

```js
// webpack.config.jsconst webpack = require('webpack')...  devServer: {    contentBase: './dist',    open: true,    hot: true, // 1.开启热模块更新    hotOnly: true // 2.即使HMR不生效，浏览器也不会自动刷新  },  plugins: [    ...    new webpack.HotModuleReplacementPlugin() // 3.必要的插件  ]
```

#### 监控JS文件

当我们仅仅用上面的配置，去实现 `JS` 模块的热更新时，会发现达不到我们想要的效果，比如：

```js
import './style.css'import counter1 from './couter1'import counter2 from './couter2'counter1()counter2()
```

这里有两个 `JS` 模块，当 `counter2` 模块发生变化时，页面没有发生变化，这是什么原因？

其实，我们监控 `JS` 模块的变化，还需要加上一串代码：

```js
import './style.css'import counter1 from './couter1'import counter2 from './couter2'counter1()counter2()if (module.hot) { // 如果开启了热模块更新  module.hot.accept('counter2.js', () => { // 监听counter2模块的变化    // 如果变化了执行以下代码    ...  })}
```

这里我们会提出一个问题：为什么上面的 `css` 文件样式改变会触发热模块更新，而 `js` 文件就需要手写一些代码实现热模块更新呢？

其实，在 `css-loader` 的底层代码中已经实现了热模块更新的效果，所以自然就不需要手动写监控代码了，从本质上，`css` 文件也需要写，不过 `css-loader` 帮忙实现了，我们就需要写了。

还有像 `vue-loader` 底层也实现了这种效果，所以也不需要我们手写监控代码，但其他一些类型的文件，可能就需要我们手写了。

## Webpack高级概念

### Tree Shaking

#### 引言

现在，我们定义了一个 `math.js` 文件，里面我们导出了两个运算函数：

```js
// math.jsfunction add(a, b) {  console.log(a + b);}function minus(a, b) {  console.log(a - b);}export {  add,  minus}
```

在 `index.js` 里面，我们导入了 `add` 函数，但却没有导入 `minus` 函数：

```js
// index.jsimport { add } from './math'add(1, 2)
```

这时，我们打包项目，查看 `main.js`：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703145201.png)

我们却看到了 `minus` 函数也出现在打包文件中，我们明明没有使用到这个函数，但却打包到了 `main.js` 中。

如果，我们想要只将我们 `import` 的函数打包，而不将虽然 `export` 但却没有 `import` 的函数打包，这种方式就称之为：`Tree Shaking`。

**需要注意的是：`Tree Shaking` 只支持 `ES Module` （静态引入），而不支持 `CommonJS`（动态引入）。**

`Tree Shaking` 根据 `mode` 的不同，可分为两种配置方式，下面分别讲解这两种配置方式。

#### 开发环境

当处于开发环境下，即 `mode: 'development'`时，需要设置两个部分的参数。

1. 修改 `webpack.config.js`

   ```js
     // webpack.config.js  ...  mode: 'development', // 开发模式  devtool: 'eval-cheap-module-source-map', // source-map  optimization: {    usedExports: true // 开启Tree Shaking  },
   ```

   `mode: 'development'` 默认是没有 `Tree Shaking` 的，所以要通过 `optimization` 手动开启

2. 修改 `package.json`

   ```json
   {    ...    "sideEffects": ["*.css", "@babel/polyfill"]}
   ```

   这里需要向 `package.json` 文件添加一行属性 `sideEffects`。

   因为在 `index.js` 文件，我们导入了一个 `css` 文件和 `@babel/polyfill`：

   ```js
   import './style.css'import '@babel/polyfill'
   ```

   这两个导入都不像 `import { add } from './math.js'` 一样，有具体的导入值，所以如果对它们做 `Tree Shaking` 的话，那么就相当于什么也没有导入，这显然会出现一些代码的缺失，造成问题。

   所以向 `sideEffects` 添加一些字符用于匹配导入模块，这样就不会对它们做 `Tree Shaking`。

   如果没有这类文件，即所有模块都做 `Tree Shaking` 的话，可以设置 `sideEffects: false`。

   因此，`sideEffects` 其实存放的就是那些不做 `Tree Shaking` 的模块文件。

#### 生产环境

当处于生产环境下，即 `mode: 'production'`时，只需要设置 `package.json`，即只要增加 `sideEffects` 即可，原理和上面一样。

因为生产环境下，默认支持 `Tree Shaking`，所以不用在 `webpack.config.js` 中设置 `optimizatiion`

#### 对比

开发环境和生产环境下的 `Tree Shaking` 除了配置方法不同以外，对打包文件代码的保留方式也不同。

1. 对于开发环境，即 `mode: development`，做了 `Tree Shaking` 的模块，对于那些没有导入的函数也依然会添加到打包结果中。比如，上面的例子，在开发环境下进行打包，`main.js` 中依然能找到 `minus` 这个没有导入的函数。

   因为在开发环境下，是要做 `SourceMap` 的，如果删除这些代码的话，会造行数的减少，所以错误提示的所在行数可能会不准确。所以为了防止这种情况的发生，开发环境下会保留这些代码，便于 `SourceMap`。

2. 对于生产环境，`SourceMap` 就显得没有那么重要了，所以即使删除了那些代码也无关紧要，所以生产环境下，那些未被使用的函数会被删除，以减小文件体积。

### 开发环境和生产环境区分打包

对于开发环境和生产环境，如果共用一个配置文件，那么将会反复的修改代码，比较麻烦，所以需要将两种环境下的配置文件分离出来。

可以拆分为三个配置文件：

1. `webpack.common.js` 两种环境共用的配置
2. `webpack.dev.js` 开发环境独有的配置
3. `webpack.prd.js` 生产环境独有的配置

然后，进行代码的划分。

这里需要用到一个插件：`webpack-merge` ，用于配置的合并。

安装：`npm install webpack-merge -D`

使用：`module.exports = WebpackMerge.merge(commonConfig, devConfig)`

代码截图：

1. `webpack.common.js`

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703162753.png)

2. `webpack.dev.js`

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703162829.png)

3. `webpack.prd.js`

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703162904.png)

4. `package.json`

![](https://gitee.com/gainmore/imglib/raw/master/img/20210703162953.png)

### Code Splitting

#### 引言

现在，假设我们在 `index.js` 中写了一些代码：

```js
// 引包，假设包的大小为1MBimport _ from 'lodash'// 业务代码，假设业务代码的大小为1MBconsole.log('hello')// 此处省略一万行代码console.log('world')
```

我们打包项目之后，`index.js` 中的所有代码都加到了 `main.js` 中，此时 `main.js` 的大小就应该是 `2MB`。

因为 `index.html` 中引用了这个 `main.js` ，所以当运行项目时，`index.html` 会加载一个大小为 `2MB` 的 `JS` 文件。当然，如果项目大的话，`main.js` 的体积还会更大。

这就会导致一些问题：

1. 倘若我们将所有代码都塞进一个文件里，这个文件的加载时间也会变得更长；
2. 当我们修改代码后，会重新打包项目，整个 `main.js` 的内容也会重新加载，加载时间也会变长。

所以，为了解决这个问题，就需要对代码进行 `Code Splitting` ，也就是 `代码分割`。

比如，将上面的代码拆分成两个文件：`lodash` 部分 和 业务代码 部分。这样当修改了业务代码，只会重新打包并加载业务代码部分，而 `lodash` 部分因为有缓存，所以保持不变。

> 需要注意的是：代码分割并不代表 `webpack`，两者其实没有什么联系，只是 `webpack` 内置了比较完善的代码功能，`Code Splitting` 只是 `webpack` 里的一大功能而已。

#### 配置

`Code Splitting` 的配置可分为两种情况，一种是 `同步引入` 的代码分割，另一种是 `异步引入` 的代码分割。

##### 同步引入

```js
import _ from 'lodash'import './style.css'console.log('hello')// 此处省略一万行代码console.log('world')
```

上面 `import` 的导入方式就是 `同步引入`。

`同步引入` 开启 `Code Splitting` 的配置只需要一步，在 `webpack.common.js` 中加入（当然这只是最简单的配置）：

```js
  optimization: {    splitChunks: {      chunks: 'all'    }  }
```

##### 异步引入

![](https://gitee.com/gainmore/imglib/raw/master/img/20210704101921.png)

而对于上面 `import` 的异步引入，无需做任何配置，会自动进行代码分割。

注意：要实现上面的动态引入，还需要安装一个插件：`npm install @babel/plugin-syntax-dynamic-import -D`

然后在 `.babelrc` 中加入： ` "plugins": ["@babel/syntax-dynamic-import"]`

#### SplitChunksPlugin

无论是 `同步引入` 还是 `异步引入`，都会使用到底层的一个插件：`SplitChunksPlugin`。

配置这个插件主要是在 `optimization.splitChunks` 中，一般会有如下配置：

```js
module.exports = {  //...  optimization: {    splitChunks: {      chunks: 'all',      minSize: 20000,	 // maxSize: 0, // 一般不设置      minChunks: 1,      maxAsyncRequests: 30, // 一般不修改      maxInitialRequests: 30, // 一般不修改      automaticNameDelimiter: '~',      cacheGroups: {        vendors: {          test: /[\\/]node_modules[\\/]/,          priority: -10,          reuseExistingChunk: true,        },        default: {          minChunks: 2,          priority: -20,          reuseExistingChunk: true,        },      },    },  },};
```

1. `chunks`

   有三个可选值：

   - `initial`：只对同步引入的代码进行分割
   - `async`：只对异步引入的代码进行分割
   - `all`：对所有引入的代码进行分割

2. `minSize`

   - 引入代码的最小体积（单位为字节）
   - 如果引入代码的体积大于 `minSize`，则会进行代码分割，反之则不会。

3. `maxSize`

   - 引入代码的最大体积（单位为字节）

   - 如果引入代码的体积小于 `maxSize`，但大于 `minSize`，则进行代码分割且将分割的代码放到一个文件里；如果大于 `maxSize`，则会将代码放到多个文件里，每个文件的最大体积为 `maxSize`。

     比如，如果一个包 `test.js`，这个包的大小为 `20 KB`，而 `maxSize` 为 `100 KB`，则会将代码放到五个文件里。

4. `minChunks`

   - 某个模块**最少被多少个入口文件**依赖

   - 首先，`chunk` 的定义就是 `entry` 中的入口文件：

     ```js
       entry: {    main: './src/index.js',    main2: './src/index2.js'  },
     ```

     上面就有两个 `chunk`

   - 当`minChunks` 为 2，且一个模块比如 `counter.js` 被 `main` 和 `main2` 这两个 `chunk` 引入，那么就满足条件，该模块就会进行代码分割，加入到一个独立的文件中；如果该模块只被一个 `chunk` 引入，那么就不满足条件，则不会进行代码分割。

5. `maxAsyncRequests`

   - 即最大异步请求的数量
   - 比如将 `maxAsyncRequests` 设置为 5，而一个项目中代码分割得非常厉害，假设分割成 10 个文件，那么此时就超过了 `maxAsyncRequests`，因此最终只会分割成 5 个文件，其余代码就不会进行分割。

6. `maxInitialRequests`

   - 即最大初始请求的数量
   - 比如一个入口文件，可能会请求多个 `JS` 文件或者库，而这个属性指的就是入口文件最多能分割文件的数量，超过这个数值，就不能进行代码分割。

7. `automaticNameDelimiter`

   - `cacheGroup` 名称和导出文件名称之间的连接符

8. `cacheGroups`

   - 意为 “缓存组”
   - 当模块符合上面的条件，就会先进入到缓存组中；而缓存组又分为多个组，比如上面的 `vendors` 和 `default`，模块会根据 `priority` 的大小进入到组中，根据条件进入到缓存组。当所有模块缓存完毕，就生成最终的文件。

   1. `priority`

      - 优先级，数值越大，优先级越高
      - 当一个模块同时满足多个组的条件，就会根据 `priority` 的大小，选择进入到哪个组

   2. `test`

      - 匹配文件
      - 比如上面的 `test: /[\\/]node_modules[\\/]/`，表示如果是 `node_modules` 里面的模块则进入到该组缓存

   3. `filename`

      - 设置代码分割生成文件的名称，默认为 `组名 + 连接符 + 输出文件名` 进行命名

   4. `reuseExistingChunk`

      - 是否复用存在的模块代码

      - 比如一个文件引入了两个模块：

        ```js
        import a from './a'import b from './b'
        ```

        假设模块 `a` 符合缓存组 `default` 的条件，而模块 `a` 中又引用了模块 `b`，所以当模块 `a` 加入到 `default` 的时候，相当于 `b` 也加入其中了。所以，之后如果模块 `b` 也符合缓存组 `default` 的条件，那么也会加入到 `default`，但是之前 `b` 已经伴随 `a` 加入了 `default` ，所以此时 `b` 重复添加了。

        而将 `reuseExistingChunk` 设置为 `true`，则会利用之前添加的 `b`，不会再将 `b` 添加了，避免了重复引入。 

流程：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210704162833.png)

### Lazy-Loading

我们看以下代码：

```js
function getComponent() {  return import('jquery').then(({ default: _ }) => {    return 'hello'  })}document.onclick = () => {  getComponent().then(rst => {    alert(rst)  })}
```

当点击页面时，才会触发 `import`。

当首次加载页面时，浏览器只请求了三个文件：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210704165343.png)

当点击页面后，浏览器请求了另外一个文件：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210704165404.png)

而另外的这个文件就是通过懒加载动态导入导入的文件。

很明显，通过懒加载可以减少请求文件的数量，提高加载速度。

### Chunks

回过头看，此时 `chunk` 的概念就很好理解了。

`chunk` 指的就是输出目录下代码分割的文件。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210704170005.png)

上面，项目的所有代码压缩到了两个文件中：`default.js` 和 `main.js` ，这两个文件就称之为两个 `chunk`，是代码分割后的产物。

### Bundle Analysis

官网：http://github.com/webpack/analyse

根据官网的提示，打包分析的第一步就是需要生成一个 `stats.json`。

在 `package.json` 中的运行命令中加入一行语句 `--profile --json > stats.json`，这样通过 `npm run dev` 的方式就会在项目的根目录下生成一个 `stats.json`，里面包含了打包的所有信息。

然后，需要选择工具进行打包分析，有如下几种：

- http://webpack.github.com/analyse（官方推荐，但需要科学上网）
- 更多工具：https://webpack.js.org/guides/code-splitting/#bundle-analysis

### Prefetching Preloading

#### 引言

上面进行代码分割时，我们了解了这样的配置：

```js
optimization: {    splitChunks: {        chunks: 'all'    }}
```

这个配置指的是对所有引入的文件进行代码分割（无论是同步引入还是异步引入）。

但官网的默认是：

```js
optimization: {    splitChunks: {        chunks: 'async'    }}
```

即官网推荐我们只对异步引入的文件进行代码分割。

这是为什么呢?

其实，对所有引入的文件进行代码分割，分割的文件第一次加载需要花费时间，但之后的加载因为有了缓存，所以加载时间更少。这的确能够减少加载时间，但是第一次文件加载依然需要花费时间，**而 `webpack` 希望在第一次加载文件时候就减少加载时间。**

#### coverage

假设我们在 `test_coverage.js` 中写入同步代码：

```js
document.onclick = function() {  var div = document.createElement('div')  div.innerHTML = '创建元素'  document.body.appendChild(div)}
```

然后，将这个文件作为入口文件进行打包，在浏览器运行文件。

打开控制台，按 `ctrl + shift + P`，输入 `coverage`，进入 `coverage` 面板，点击重新加载，可以看到每个文件的覆盖率（即利用率）。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705155410.png)

可以看到，`test_coverage.js` 的有 `34.4%` 的代码没有被利用，点击该文件：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705155517.png)

可以看到红色部分就是未利用部分，当点击页面时，触发回调函数，红色部分代码变绿。

---

为了解决 `test_coverage.js` 中无用代码占用率过高的问题，我们可以将里面的代码放到一个 `click.js` 文件里，然后通过动态引入的方式导入代码：

```js
document.onclick = function() {  import('./click.js').then(({ default: func }) => {    func()  })}
```

重新看一下利用率：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705161339.png)

可以看到，`test_coverage.js` 中有 `24.9%` 的代码未被利用，说明代码利用率相比之前提高了。

---

因此，`webpack` 希望我们通过 `chunks: 'async'`的方式只对异步引入的代码进行分割，因为导入同步代码，可能会使代码的利用率变低，影响加载速度。这也是为什么 `webpack` 里的 `chunks` 默认为 `async`。

#### Prefetching

> 上面异步引入模块的方式也存在一些问题，假如引入的模块体积非常大，如果当用户点击某个按钮的时候，才引入这个模块，那么用户可能就需要等待很长的一段时间去等待模块加载完成，这样就会导致用户的体验非常差。

所以，为了解决这个问题，就需要使用到 `prefetching（预先获取）` 和 `preloading（预先加载）`。

这里需要使用到 `魔法注释(magic comment)`，在 `import` 里面假如如下注释，开启 `prefetching`：

`import(/* webpackPrefetch: true */'./click.js')`

开启 `prefetching` 的作用是：会在加载完页面且网络空闲的时候加载并缓存该模块，当需要引入该模块时，浏览器能够利用缓存很快地将模块加载出来。

我们可以对比开启前后的效果：

开启之前：

1. 首次加载页面：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705164050.png)

2. 点击页面，触发回调，加载 `0.js`

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705164130.png)

开启之后：

1. 首次加载页面：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705164218.png)

​       可以看到，`main.js` 加载完成之后，随即加载 `0.js`。

2. 点击页面，触发回调，加载 `0.js` （缓存，加载速度更快）

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705164252.png)

#### Preloading

`preloading` 和 `prefetching` 的差异主要体现在加载时刻的不同：`preloading` 的模块会在其他文件加载的同时加载，而 `prefetching` 的模块是在其他文件加载完成之后，网络空闲的阶段加载。 

因此，相比于 `preloading` ，`prefetching` 更符合我们对性能优化的要求。

### chunkFilename

```js
// webpack.common.js ...  entry: {    main: './src/index.js'  },  output: {    path: path.resolve(__dirname, 'dist'),    filename: '[name].js',    chunkFilename: '[name].chunk.js'  },
```

其中，`output` 中的 `filename` 对应的是 `entry` 中的入口文件，而 `chunkFilename` 对应的是入口文件中引入的模块。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705194317.png)

### CSS文件代码分割

之前打包 `CSS` 文件都是将 `CSS` 插入到 `JS` 中，生成打包文件。

现在对 `CSS` 文件进行代码分割。

#### MiniCssExtractPlugin

安装：`npm install mini-css-extract-plugin -D`

**注意：由于这个插件不支持 `HMR`，所以不适合放在开发环境中，因为不便于代码调试。因此，尽量在生产环境中应用此插件。**

配置：

```js
// webpack.prd.js  ...  plugins: [    new MiniCssExtractPlugin({ // 1.添加插件      filename: '[name].css',      chunkFilename: '[name].chunk.css'    })   ],  module: {    rules: [      {        test: /\.css$/,        use: [          {            loader: MiniCssExtractPlugin.loader // 将style-loader替换成插件里的loader          },          {            loader: 'css-loader'          }        ]  ...
```

特别注意：

如果项目中开启了 `Tree Shaking`，注意要设置 `package.json` 的 `sideEffects` 选项，将 `CSS` 文件添加进去，比如：` "sideEffects": ["*.css"]`。

否则的话，使用 `import './style.css'` 的方式导入，会将整个 `CSS` 文件给忽略掉。

#### CSS代码压缩

安装插件：`npm install optimize-css-assets-webpack-plugin -D`

配置：

```js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')...  optimization: {    minimizer: [      new OptimizeCSSAssetsPlugin({})    ]  }
```

`CSS` 代码会自动压缩并合并。

### Caching

之前，我们生产环境打包的文件都如下面这种形式：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705204758.png)

可以看出，这些文件的名称都是固定的。

这就会带来一个问题：当我们把这些文件部署到服务器上，用户通过浏览器访问网站，然后请求了这些文件并将它们缓存到了浏览器上。当我们修改源代码，然后重新打包并部署到服务器上时，用户再次访问网站、请求文件，然而因为请求的这些文件名称和修改之前的文件名称完全一样，所以浏览器就会访问缓存里的文件。

这导致的结果就是，用户不能第一时间获取到最新的修改。

因此，为了解决这一问题，就需要在每次修改源代码后，重新打包生成不同名称的文件。

这里就可以使用到 `output` 属性中 `filename` 的占位符：`[contenthash]`。它的作用就是根据打包文件的内容生成一串哈希值加到文件名称里，如果内容不变，哈希值不变；如果内容修改，哈希值更改。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705205957.png)

---

另外，对于 `webpack4` 之前的老版本的 `webpack`，加了 `[contenthash]`，即使源代码没有被修改，哈希值也可能会变化。

解决办法：

```js
// webpack.prd.jsoptimization: {    runtimeChunk: {		name: 'runtime'    }}
```

重新打包项目：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210705210819.png)

可以发现，打包目录下主要有三类文件，第一类是主文件 `main.js` ，第二类是 `runtime.js`，第三类是引入的第三方包 `vendors.js`。

我们知道主文件里的代码和第三方包的代码有各种依赖关系，这些依赖关系就称之为 `manifest`，打包的时候，虽然源代码不变，但依赖关系可能会改变，这就导致 `[contenthash]` 会改变。而如果将 `manifest` 都保存在 `runtime.js` 中，即使 `manifest` 改变，也不会影响到其他两类文件的内容，哈希值就不会改变了。

当然，以上只是针对老版本的 `webpack`，对于 `webpack4` 及以上的版本，直接使用 `[contenthash]` 即可。

### Shimming

Shim，即垫片。

一般，垫片用于解决兼容性的问题。比如之前在转换 `ES6` 语法时，使用了 `Polyfill` 这个垫片。像一些对象或函数比如 `Promise`，`map`，它们不兼容低版本的浏览器，所以需要使用 `Polyfill` 这个垫片解决兼容问题。

而 `webpack` 本身内置了一个插件 `ProvidePlugin`，它可以将模块内没有声明的变量和某个模块对应起来。

比如，我们写了一个 `UI` 模块，为了保持代码的简洁，我们不希望导入第三方模块，而是使用一些变量代替导入模块：

```js
export function UI() {    $('body').css('background', _join(['green'], ''))}
```

正常情况下，代码肯定报错。

但我们可以使用 `webpack.ProvidePlugin` 处理这种情况：

```js
const webpack = require('webpack')...plugins: [    new webpack.ProvidePlugin({        $: 'jquery',        _join: ['lodash', 'join']    })]
```

上面的配置就相当于：

```js
import $ from 'jquery'import _ from 'lodash'var _join = _.join
```

---

在一个 `js` 文件里，`this` 默认指向的就是这个 `js` 文件。如果我们想要修改 `this` 的指向，可以用到 `imports-loader` 这个插件。

安装： `npm install imports-loader -D`

配置：

```js
test: /\.js$/,use: [    {        loader: 'imports-loader?this=>window' // 将this指向window对象    }]
```

### 环境变量

```json
// package.json"scripts": {    "build": "webpack --env.production --config webpack.common.js"}
```

```js
// webpack.common.js...module.exports = (env) => {    if (env && env.production) {        ...    } else {        ...    }}
```

使用 `--env.production` 会向目标配置文件中传入该参数，并且 `env.production` 默认为 `true`；当没有 `--env` 的命令，就不会向配置文件传参，`env` 也就不存在。

当然也可以写成这种形式： `--env.production='prd'`，这时判断条件为：

```js
module.exports = (env) => {    if (env && env.production === 'prd') {        ...    } else {        ...    }}
```

## Webpack实战配置案例

### Library的打包

如果我们写完一个组件库或者库函数，该如何对它们进行打包？

这里我们写一个简单的库：

```js
// math.jsexport function add(a, b) {  return a + b}export function minus(a, b) {  return a - b}export function multiply(a, b) {  return a * b}export function division(a, b) {  return a / b}// string.jsexport function join(a, b) {  return a + '-' + b}// index.jsimport * as math from './math'import * as string from './string'export default { // 导出库函数  math,  string}
```

我们将 `index.js` 作为入口文件进行打包，打包生成的 `main.js` 作为一个库被人调用。

而其他人调用这个库的方式可能有很多种，比如：

```js
import library from 'library' // ES Moduleconst library = require('library') //CommonJSrequire(['library'], function() { // AMD    ...})
```

为了兼容这几种调用方法，可以添加配置：

```js
output: {    libraryTarget: 'umd' // u指universal，适用于上面几种调用方式}
```

这样，打包生成的代码就可以通过上面几种方式导入到其他代码中。

这里还有一种引入方式：

```html
<script src="library.js"></script>
```

通过 `HTML` 文件依赖 `JS` 文件，可以实现 `JS` 的引入，如果要将库文件挂载到一个全局变量中，可以添加配置： `library: 'variableName'`，这样在浏览器控制台中输入这个变量，就可以打印出来这个库文件的信息。

一般配置：

```js
output: {    library: 'root',    libraryTarget: 'umd'}
```

`libraryTarget` 除了 `umd` 这个值外，还有 `this、window、global(Nodejs)` 这几个可选值，指的是将变量挂载哪里，比如如果 `libraryTarge: 'this'`，则可以通过 `this.root` 访问库函数。

---

当我们写的库函数中，需要引入其他库，比如：

```js
// string.jsimport _ from 'lodash'export function join(a, b) {  return _.join([a, b], '-')}
```

如果直接这样打包，打包后的文件体积一定会过大，因为包含了其他库 `lodash`。

为了解决这一问题，我们可以在打包的过程忽略其他库的打包，然后在需要使用打包库的时候，将其他库引入进去：

```js
// string.jsexport function join(a, b) {  return _.join([a, b], '-')}// 打包。。。生成文件 `library.js`// 使用库import _ from 'lodash' // 必须预先引入import library from 'library' // 使用库
```

在打包过程忽略某些库的打包所要做的配置是：

```js
// webpack.config.jsexternals: ['lodash'] // 在打包过程中，如果遇到lodash库，将其忽略
```

---

如果我们写的库函数还不错，可以发布到 `npm` 中，供其他人使用。

1. 在 `npm` 上注册一个账号

2. `npm adduser`

3. 配置：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210706105233.png)

4. `npm publish`

5. `npm install xxx`

### PWA

`PWA` 即 `Progressive Web Application`，翻译成 “渐进式网络应用”。

我们通过 `webpack` 打包生成的代码是要部署到服务器上的，然后才能被用户访问。

假设一种情况：当服务器宕机或者用户的网络出现故障，用户还能访问我们的网站吗？

这显然是不能的。而 `PWA` 的作用就是即使出现上面的情况，用户依然还能获取到网站的内容。

要实现这种功能，需要安装一个插件 `workbox-webpack-plugin`。

1. 安装：`npm install workbox-webpack-plugin -D`

2. 配置：

   ```js
   // webpack.prd.jsconst WorkboxPlugin = require('workbox-webpack-plugin')...plugins: {    new WorkboxPlugin.GenerateSW({ // SW指Service Worker        clientsClaim: true,        skipWaiting: true    })}
   ```

   

3. 重新打包项目，可以发现 `dist` 目录下增加了两个文件：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210706185447.png)

   这其实对一些页面内容的缓存

4. 然后我们需要在 `index.js` 中加入一些业务代码（用于判断是否支持 `serviceWorker` 以及添加监听事件）：

   ```js
   if ('serviceWorker' in navigator) {  window.addEventListener('load', () => {    navigator.serviceWorker.register('service-worker.js')      .then(registration => {        console.log('service-worker registed successfully!');      }).catch(error => {        console.log('service-worker registed failed!');      })  })}
   ```

5. 测试：这里我们可以安装 `http-server` 插件来测试效果。

   安装：`npm install http-server -D`

   配置：

   ```json
   // package.json"scripts": {    "server": "http-server dist"}
   ```

   `npm run server` 启动本地服务器，测试 `PWA`

### TypeScript打包配置

`TypeScript` 相比于 `JavaScript` 有以下特性：

- 具有类型约束，更加严谨
- 能够规范代码，增强代码的可维护性和扩展性

当然，`webpack` 不能直接打包 `.tsx` 文件（也就是 `TypeScript` 文件），需要使用 `ts-loader` 将 `ts` 转换成 `js`。

1. 安装：`npm install ts-loader typescript -D`

2. 配置：

   ```js
   // webpack.config.jsmodule: {    rules: [        {            test: /.tsx?$/,            use: 'ts-loader',            exclude: /node_modules/        }    ]}
   ```

3. 创建 `tsconfig.json` 文件：

   ```json
   {    "compilerOptions": {        "outDir": "./dist",        "module": "es6",        "target": "es5",        "allowJs": true    }}
   ```

通过以上操作，就可以打包项目中的 `TypeScript` 文件。

`TypeScript`文件：

```ts
class Greeter {    greeting: string;    constructor(message: string) {		this.greeting = message    }    greet() {        return _.join(['hello', this.greeting], '-')    }}const greeter = new Greeter('world')alert(greeter.greet())
```

有时，我们需要在 `ts` 中引入第三方库，比如 `lodash` 、`jquery`，我们使用这些库的方法时，有时可能会少传一些参数或者将参数类型弄错，但却没有代码提示。

为此，我们可以安装库的类型文件，去自动检测类型。

通常这些类型文件的格式为：`@types/<library>`，比如 `@types/lodash`，`@types/jquery`，

我们可以通过 `npm install @types/xxx -D` 的方式，安装这些类型文件，用于自动检测类型，方便纠错。

### WebpackDevServer

#### 请求转发

之前的内容学习了 `devServer` 的一些基本配置项：

```js
// webpack.config.js  devServer: {    contentBase: './dist', // 设置服务器访问的基本目录    host: 'localhost', // 设置服务器的ip地址    port: 8090, // 设置服务器的端口，默认为8080    open: true, // 打包完成，自动打开浏览器    proxy: { // 代理，跨域      '/api': 'http://localhost:3000'    }  },
```

这里，我们主要来学习其中的 `proxy` 这个配置项，`proxy` 可以用来做请求的转发。

在项目的开发阶段，我们使用了 `devServer` 开启一个本地的服务器，项目中如果要发送或接收 `XHR` 异步请求，就需要使用 `axios` 这个库。

但是在开发阶段的项目地址和接口地址是不同源的，这就会造成跨域的问题。为了解决跨域问题，就可以利用到**请求转发**了。

比如我们项目的地址是 `http://localhost:3000/index`，我们要在项目中请求接口 `http://test/api/users`，为了避免跨域就可以先访问一个本地地址 `/api/users`，然后通过代理将这个地址转换成对应的接口地址。

配置：

```js
// webpack.config.js...devServer: {    ...    proxy: {        '/api/users': {            target: 'http://test/api/users',            pathRewrite: { // 将某个资源重定向到另一个地址上                'header.json': 'demo.json'            },            // secure: false, // 当targetURL为https请求时，需要开启            bypass: { // 拦截某些请求                ...            },            changeOrigin: true // 当服务器设置了爬虫拦截，需要开启        }    }}
```

更多配置，去看官网。

#### 单页应用

使用 `Vue` 或者 `React` 写单页面应用时，往往会涉及到路由跳转。

在开发阶段，我们使用 `WebpackDevServer` 创建本地服务器，当我们进行路由跳转时，会跳转不到我们希望的页面上，这是因为 `WebpackDevServer` 在请求路由的时候，是在目录中寻找对应的 `HTML` 文件的。

比如，访问 `http://localhost:3000/index`，其实 `WebpackDevServer` 就是在 `dist` 目录下请求 `index.html` 然后将它展示出来。

为了解决这个问题，我们需要在 `devServer` 中写入如下配置：

```js
devServer: {    historyApiFallback: true,}
```

*一般，将这个参数设置为 `true` 就行了，如果需要加入更多功能，可参照官网给出的配置。*

需要注意的是，这种解决方法只针对开发环境，对于生产环境，就不能使用这种方法了。这需要交给后端人员处理，使用 `Nginx` 或者 `apache` 转发请求。

### ESlint

使用 `ESlint` 规范代码。

1. 安装：`npm install eslint eslint-loader -D`

2. 生成配置文件：`npx eslint --init`

3. 配置文件：

   ```js
   // .eslintrc.jsmodule.exports = {  extends: 'airbnb-base',  rules: {    semi: 0, // 自定义规范  },};
   ```

4. 此外，可以在 `VSCode` 中安装插件 `ESlint`，这个插件可以根据配置文件识别文件中的书写错误，更加方便

5. 打包配置：

   ```js
   // webpack.config.js      {        test: /\.js$/,        exclude: /node_modules/,        use: [          {            loader: 'eslint-loader',            options: {              force: 'pre', // 让eslint-loader强制先执行，即使loader顺序不对              fix: true, // 对于一些小错误，自动修复，比如自动缩进，自动加分号              cache: true // 设置缓存，提高打包速度            }          }        ]      }
   ```

6. `devServer`配置：

   ```js
   // webpack.config.jsdevServer: {    overlay: true // 将错误信息覆盖到浏览器页面上}
   ```

### 多页面应用打包

之前我们的项目都是单页面应用，即只有一个 `HTML` 文件，像一些流行框架 `Vue` 、`React` 都是单页面应用的框架。

那么如何打包多页面应用的项目呢？

其实也很简单，既然多页面应用对应多个 `HTML` 文件，我们只要在原来的基础上改一些配置即可。

```js
// webpack.config.jsentry: {    index: './src/index.js',    list: './src/list.js',    user: './src/user.js',    ...}plugins: [    new HtmlWebpackPlugin({        template: 'src/index.html', // 模板        filename: 'index.html', // html文件名        chunks: ['runtime', 'vendors', 'index'] // 依赖的文件    }),    new HtmlWebpackPlugin({        template: 'src/index.html',        filename: 'list.html',        chunks: ['runtime', 'vendors', 'list']    }),        new HtmlWebpackPlugin({        template: 'src/index.html',        filename: 'user.html',        chunks: ['runtime', 'vendors', 'user']    }),]
```



## Webpack性能优化

### 1.更新打包工具

- 跟上技术的迭代（Node、Npm、Yarn）
- 打包工具版本越新，打包效率越高

### 2.loader

- 在尽可能少的模块上应用 `loader`
- 比如，对于 `JS` 文件可以设置 `exclude: /node_modules/` 跳过一些第三方包的 `JS` 文件，或者设置 `include` 指定文件所在的路径，以此来降低 `loader` 的使用频率

### 3.plugin

- `plugin` 尽可能精简且可靠
- 尽可能少用 `plugin`
- `plugin` 尽可能选择官网推荐插件，保证插件的性能

### 4.resolve

- 合理配置 `resolve`

- ```js
  // webpack.config.js...resolve: {    // 1.设置默认扩展名，如果导入文件没有扩展名，则在extensions依次使用扩展名匹配文件    extensions: ['.js', '.jsx'],    // 2.设置默认文件，如果导入只有路径没有文件，则在mainFiles依次使用文件名匹配文件    mainFiles: ['index', 'demo'],    // 3.设置别名，用别名代替指定路径    alias: {        component: './src/component',        api: './src/api',    }}
  ```

- 合理配置 `resolve`，可以减少文件查找次数，提高打包速度

### 5.DllPlugin

在大型项目中，可能会引入许多第三方模块，这些第三方模块的引入会导致项目打包时间的增加。

所以，为了提高打包速度，我们可以将这些第三方模块提取出来，只打包一次并生成一个包含所有第三方库的文件，然后在项目中引入这个文件，以后的打包就不需要重新分析所有的第三方模块，这样就提高了打包速度。

1. 将第三模块只打包一次

   为此，我们新建一个配置文件 `webpack.dll.js`，写入：

   ```js
   const path = require('path')const webpack = require('webpack')module.exports = {  mode: 'production',  entry: {    vendors: ['lodash'], // 放置所有需要打包的第三方模块  },  output: {    filename: '[name].dll.js',    path: path.resolve(__dirname, 'dll'),    library: '[name]', // 当浏览器引入该文件时，能暴露出一个全局变量  },  plugins: [    new webpack.DllPlugin({ // 生成映射文件，根据映射文件能够找到已经打包的第三方模块      name: '[name]',      path: path.resolve(__dirname, 'dll/[name].manifest.json'),    }),  ],}
   ```

2. 在对项目打包时，不分析第三方模块

   修改 `webpack.config.js`：

   ```js
   // 1.将dll文件注入到html-plugin中，即在index.html中引入dll文件，使浏览器能通过全局变量vendors访问模块new AddAssetHtmlWebpackPlugin({     filepath: path.resolve(__dirname, 'dll/vendors.dll.js'),}),// 2.依赖映射文件，当打包项目时，不分析在dll中的第三方模块，以此提升打包速度new webpack.DllReferencePlugin({    manifest: path.resolve(__dirname, 'dll/vendors.manifest.json'),}),
   ```

实际开发中，第三方模块的数量会很多，需要分成多个 dll 文件，所以需要修改 `webpack.dll.js` 的入口：

```js
entry: {  vendors: ['lodash'], // 放置所有需要打包的第三方模块  react: ['react', 'react-dom'],  jquery: ['jquery'],  ...},
```

所以这种情况下，`dll` 目录下就会生成多个 `dll.js` 文件和多个 `manifest.json` 文件，因此在 `webpack.config.js` 中也需要增加多个 `AddAssetHtmlWebpackPlugin` 和 `DllReferencePlugin`，为了简化代码，可以：

```js
const plugins = [...]const files = fs.readdirSync(path.resolve(__dirname, './dll'))files.forEach(file => {    if (/.*\.dll.js/.test(file)) { // 匹配dll.js        plugins.push(new AddAssetHtmlWebpackPlugin({            filepath: path.resolve(__dirname, 'dll', file)        }))    } else if (/.*\.manifest.json/.test(file)) { // 匹配manifest.json        plugins.push(new webpack.DllReferencePlugin({            manifest: path.resolve(__dirname, 'dll', file)        }))    }})
```

### 6.控制包文件大小

- 通过 `Tree Shaking` 忽略不需要的代码引入
- 通过 `Code Splitting` 缩小单个文件的体积

### 7.多进程打包

- `thread-loader`
- `parallel-webpack`
- `happypack`

### 8.合理使用sourceMap

### 9.结合stats分析打包结果

### 10.开发环境内存编译

### 11.开发环境无用插件剔除



## Webpack底层原理

### 编写loader

首先，需要创建一个 `loader` 文件，比如 `replaceLoader.js`。

`loader` 文件本质上是一个函数：

```js
module.exports = function(source) { // 注意，不能是箭头函数，因为会用到函数的this    return source.replace('Hello', 'Hi')}
```

以上，就是一个最简单的 `loader`了。

然后，需要在指定的文件上配置 `loader`：

```js
// webpack.config.jsmodule: {    rules: [        {            test: /\.js$/,            use: [                path.resolve(__dirname, './loaders/replaceLoader.js') // 配置自定义loader            ]        }    ]}
```

---

此外，还可以向 `loader` 传递参数（根据官网提示进行配置）：

```js
// webpack.config.jsuse: [    {        loader: path.resolve(__dirname, './replaceLoader.js'),        options: {			name: 'Jack',             info: {				gender: 'male',                 age: 20             }        }    }]
```

在 `loader` 中获取参数：

```js
module.exports = function(source) {	const name = this.query.name    const info = this.query.info    ...}
```

此外，还有一些有用的 `API`，比如 `this.callback`。上面的那些 `loader` 只能返回 `source` （即源代码），如果想返回 `SourceMap` 或者其他一些数据，就可以利用到这个 `API`。

```js
this.callback(	err: Error | null, // 错误信息    content: string | Buffer // 源代码     sourceMap?: SourceMap, // SourceMap    meta?: any // 其他数据)
```

官网上还介绍了很多其他的 `API`，比如 `this.async()` 可以用来执行异步函数。

另外，在引入自定义 `loader` 时，可能会写一长串的路径，比如：`path.resolve(__dirname, './loaders/xxxLoader.js')`

如果想直接写 `xxxLoader`，而不写前面的路径，可以加入以下配置：

```js
resolveLoaders: {    modules: ['node_modules', './loaders'] // 引入loader时，会自动在这些文件夹中找对应的loader}
```

### 编写plugin

`webpack` 中的 `plugin` 本质上是一个类，其基本形式为：

```js
class TestWebpackPlugin {  constructor() {    ...  }  apply(compiler) {    ...  }}module.exports = TestWebpackPlugin
```

然后，需要在 `webpack.config.js` 中导入这个插件，由于插件本质上是一个类，所以要使用 `new` 对它初始化。

```js
plugins: [    new TestWebpackPlugin()]
```

初始化插件时，可以向里面传入一个参数，通常为对象，如：

```js
plugins: [    new TestWebpackPlugin({        name: 'Tom',        gender: 'male',        age: 22    })]
```

在 `construtor` 中接收这个对象：

```js
constructor(options) {    console.log(options);}
```

插件的作用是，让 `webpack` 在打包过程的某一时刻完成某件事情，所以就会应用到钩子函数。

`webpack` 的官网中列举了很多钩子函数，分别代表了不同的时刻，我们可以根据插件的功能选择合适的钩子函数进行操作。

```js
class TestWebpackPlugin {  constructor(options) {    console.log(options);  }  apply(compiler) {    compiler.hooks.emit.tapAsync('TestWebpackPlugin', (compilation, callback) => {      compilation.assets['test.txt'] = { // 向输出目录添加文件        source: function() { // 文件内容          return 'This is test...'        },        size: function() { // 文件体积          return 20        }      }      callback() // 回调函数    })  }}module.exports = TestWebpackPlugin
```

`Node调试`：

`"debug": "node --inspect --inspect-brk node_modules/webpack/bin/webpack.js" // 开启调试，第一行打断点`

`npm run debug` 启动项目之后，打开浏览器，点击控制台，点击绿色图标，进入调试页面，在源代码上写 `debugger` 就在该位置打上断点。

### 编写源码

#### moduleAnalyser

![](https://gitee.com/gainmore/imglib/raw/master/img/20210709113041.png)

根目录下创建 `bundle.js` ，对入口文件进行打包。

首先，需要读取入口文件的内容：

```js
// bundle.jsconst fs = require('fs')const path = require('path')function moduleAnalyser(filename) {  const content = fs.readFileSync(filename, 'utf-8') // 获取文件内容  console.log(content);}moduleAnalyser('./src/index.js')
```

然后，获取文件中的依赖模块，可以使用到 `@babel/parser`。

安装：`npm install @babel/parser --save`

```js
const fs = require('fs')const path = require('path')const parser = require('@babel/parser')function moduleAnalyser(filename) {  const content = fs.readFileSync(filename, 'utf-8') // 获取文件内容  const ast = parser.parse(content, { // 将文件内容解析成AST    sourceType: 'module'  })  console.log(ast.program.body);}moduleAnalyser('./src/index.js')
```

然后，需要遍历所有节点，获取到引入节点，可以使用到 `@babel/traverse`。

安装：`npm install @babel/traverse --save`

```js
const fs = require('fs')const path = require('path')const parser = require('@babel/parser')const traverse = require('@babel/traverse').default // ES Modulefunction moduleAnalyser(filename) {  const content = fs.readFileSync(filename, 'utf-8') // 获取文件内容  const ast = parser.parse(content, { // 将文件内容解析成AST    sourceType: 'module'  })  traverse(ast, {    ImportDeclaration({ node }) {      console.log(node.source.value); // 获取依赖模块路径    }  })}moduleAnalyser('./src/index.js')
```

将所有依赖存到一个对象里面。

```js
const fs = require('fs')const path = require('path')const parser = require('@babel/parser')const traverse = require('@babel/traverse').default // ES Modulefunction moduleAnalyser(filename) {  const content = fs.readFileSync(filename, 'utf-8') // 获取文件内容  const ast = parser.parse(content, { // 将文件内容解析成AST    sourceType: 'module'  })  const dependencies = {} // { 相对路径: 绝对路径 }  traverse(ast, {    ImportDeclaration({ node }) {      const dirname = path.dirname(filename)      dependencies[node.source.value] = path.join(dirname, node.source.value)      console.log(dependencies);    }  })}moduleAnalyser('./src/index.js')
```

此外，我们还要把代码翻译成浏览器能识别的代码，需要用到 `@babel/core`。

安装： `npm install @babel/core --save`

```js
const fs = require('fs')const path = require('path')const parser = require('@babel/parser')const traverse = require('@babel/traverse').default // ES Moduleconst babel = require('@babel/core')function moduleAnalyser(filename) {  const content = fs.readFileSync(filename, 'utf-8') // 获取文件内容  const ast = parser.parse(content, { // 将文件内容解析成AST    sourceType: 'module'  })  const dependencies = {} // { 相对路径: 绝对路径 }  traverse(ast, {    ImportDeclaration({ node }) {      const dirname = path.dirname(filename)      dependencies[node.source.value] = path.resolve(dirname, node.source.value)    }  })  const { code } = babel.transformFromAst(ast, null, { // 翻译源码    presets: ['@babel/preset-env']  })   return { // 返回对象    filename,    dependencies,    code  }}module.exports = bundleFilemoduleAnalyser('./src/index.js')
```

#### makeDependenciesGraph

上面的 `moduleAnalyser` 只针对一个模块进行分析。

现在我们需要收集所有模块中的依赖：

```js
function makeDependenciesGraph(entry) {  const entryModule = moduleAnalyser(entry)  const graphArray = [ entryModule ]  // 生成依赖数组  for (let i = 0; i < graphArray.length; i++) {    const item = graphArray[i]    const { dependencies } = item    for (let j in dependencies) {      graphArray.push(        moduleAnalyser(dependencies[j])      )    }  }  // 将依赖数组转换成对象  const graph = {}  graphArray.forEach(item => {    graph[item.filename] = {      dependencies: item.dependencies,      code: item.code    }  })}
```

#### generateCode

收集完依赖模块之后，就需要生成代码文件了。

```js
function generateCode(entry) {  const graph = JSON.stringify(makeDependenciesGraph(entry))  return `    (function(graph) {      function require(module) {        function localRequire(relativePath) {          return require(graph[module].dependencies[relativePath])        }        var exports = {}        (function(require, exports, code) {          eval(code)        })(localRequire, exports, graph[module].code)        return exports      }      require('${entry}')    })(${graph})  `}
```



## Vue-CLI3

作为 `Vue` 的一款脚手架工具，`Vue-CLI3` 追求 “零配置”，尽可能让不懂 `Webpack` 的开发者上手，所以在生成的项目中看不到有关 `Webpack` 的配置文件。

当我们想要修改一些配置时，可以在项目的根目录中添加配置文件 `vue.config.js`。

但在该文件中的配置和 `webpack.config.js` 不一样，`Vue-CLI` 提供了一套自己的 `API`，我们直接根据文档进行调用即可。它的本质是对 `Webpack` 中一些配置的封装。

`Vue-CLI官网`：https://cli.vuejs.org/zh/guide/

