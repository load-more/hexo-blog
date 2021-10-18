---
title: 78 前端性能优化
date: 2021-09-02 14:35:33
tags: [Webpack, Vuejs]
categories: 性能优化
---

学习前端性能优化常用方式。

<!-- more -->

# 前端性能优化

## Web性能定义

MDN 上的 Web 性能定义：Web 性能是网站或应用程序的客观度量和可感知的用户体验。

- 减少整体加载时间：减少文件体积、减少 HTTP 请求，使用预加载；
- 使网站尽快可用：仅加载首屏内容，其它内容根据需要进行懒加载；
- 平滑和交互性：使用 CSS 代替 JS 动画、减少 UI 重绘；
- 感知表现：页面可能做不到更快，但可以让用户感觉更快。耗时操作要给用户反馈，比如加载动画、进度条、骨架屏等提示信息；
- 性能测定：性能指标、性能测试、性能监控等持续优化。

## 性能指标

### RAIL 性能模型

**Response**

响应。应该尽可能快速地响应用户，应该在 100ms 以内响应用户的输入。

**Animation**

动画。在展示动画的时候，每一帧应该以 16ms 进行渲染，这样可以保持动画效果的一致性，并且避免卡顿。

**Idle**

空闲。当使用 JavaScript 主线程的时候，应该把任务划分到执行时间小于 50ms 的片段中去，这样可以释放线程以进行用户交互。

**Load**

加载。应该在小于 1s 的时间内加载完成你的网站，并且可以进行用户交互。



### 基于用户体验的性能指标

> https://web.dev/

#### FCP

First Contentful Paint 首次内容绘制。

浏览器首次绘制来自 DOM 的内容的时间，内容必须时文本、图片（包含背景图）、非白色的 canvas 或 SVG，也包括带有正在加载中的 Web 字体的文本。

#### LCP

Largest Contentful Paint 最大内容绘制。

可视区域中最大内容元素呈现到屏幕上的时间，用以**估算页面的主要内容对用户可见的时间**。

LCP 考虑的元素：

- `<img>` 元素
- `<image>` 元素内的 `<svg>` 元素
- `<video>` 元素（封面图）
- 通过 `url()` 函数加载背景图片的元素
- 包含文本节点或者其它内联级文本元素子级的块级元素

为了提供良好的用户体验，网站应力争用 2.5s 或者更短的 “最大内容绘画”。

#### FID

First Input Delay 首次输入延迟。从用户第一次与页面交互（例如点击链接、点击按钮等）到浏览器实际能够响应交互的时间。

输入延迟是因为浏览器的主线程正忙于做其他事情，所以不能响应用户。发生这种情况的一个常见原因是因为浏览器正忙于解析和执行应用程序加载的大量计算的 JavaScript。

第一次输入延迟通常发生在第一次内容绘制（FCP） 和可持续交互时间（TTI）之间，因为页面已经呈现了一些内容，但还不能可靠地交互。

#### TTI

Time To Interactive 表示网页第一次**完全达到可交互状态**的时间点。浏览器已经可以持续性的响应用户的输入，完全达到可交互状态的时间点是在最后一个长任务（Long Task）完成的时间，并且在随后的 5s 内网络和主线程是空闲的。

从定义上看，中文名称叫可持续交互时间或可流畅交互时间更合适。

> 长任务是需要 50ms 以上才能完成的任务。

#### TBT

Total Block Time 总阻塞时间。度量了 FCP 和 TTI 之间的总时间，在该时间范围内，主线程被阻塞足够长的时间以防止输入响应。

只要存在长任务，该主线程就会被视为 “阻塞”，该任务在主线程上运行超过 50ms。主线程被阻塞是因为浏览器无法中断正在执行的任务。因此，如果用户确实在较长的任务中间与页面进行交互，则浏览器必须等待任务完成才能进行响应。

如果任务足够长（例如，超过 50ms 的任何时间），则用户很可能会注意到延迟并感觉页面缓慢或者过时。

#### CLS

Cumulative Layout Shift 累计布局偏移。CLS 会测量在页面整个生命周期中发生的每个意外的布局偏移的所有单独布局偏移位分数的总和，它是一种保证页面的视觉稳定性从而提升用户体验的指标方案。

在没有任何警告的情况下，页面突然发生变化，比如从页面顶部突然增加了一个图片，并使之前的内容往下偏移，这可能导致用户不小心点击到了其它的元素。



### Web Vitals

- 加载性能（LCP）-- 显示最大内容元素所需时间
- 交互性（FID）-- 首次输入延迟时间
- 视觉稳定性（CLS）-- 累计布局偏移



## 检测工具

### LightHouse

#### 性能报告

##### 检测得分

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901225259.png)

##### 性能指标

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901225719.png)

##### 优化建议

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901230238.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901230533.png)

##### 诊断结果

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901230820.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901231022.png)

##### 已通过项

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901231254.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901231052.png)

### WebPageTest

### ChromeDevtools

#### Network

通过浏览器 Network 确定首页加载的资源和请求量

- requests：请求数量
- resources：前端资源总大小
- transferred：资源在网络中传输的大小
- DOMContentLoaded：浏览器加载完 HTML 的时间（此时其它静态资源，如 JS、CSS、图片等还未下载）
- Load：浏览器加载完所有资源文件的时间
- ![](https://gitee.com/gainmore/imglib/raw/master/img/20210902093306.png)

#### Coverage

通过浏览器 Coverage 来查看代码的覆盖率

- 只针对 JS 和 CSS
- 可以看出哪些代码虽然加载了但是没有执行
- 没有执行的代码可以考虑是否可以进行懒加载
- ![](https://gitee.com/gainmore/imglib/raw/master/img/20210902093445.png)

#### Performance



## 优化方式

### 1.减少资源请求

#### 1.1减少图片请求

1. 通过懒加载的方式处理非首屏的图片；
2. 对于小图标可用采用 iconfont 的方式解决；
3. 对于小图片可用采用 Sprite 精灵图解决或者将小图片转成 base64 格式让它们随着 CSS 文件一起加载。比如在 webpack 中配置 url-loader，给它设置一个 limit 值，当图片小于这个 limit 值就会将图片转成 base64 格式，但这种方法会导致 CSS 文件非常臃肿。

#### 1.2减少首页请求

1. 通过 nginx 服务器（可用来做 CDN，用来处理静态资源）来做资源文件的合并（combo）-- 将多个资源的 CDN 地址合并到一起，同时加载多个 JS 文件或 CSS 文件。

   > 扩展：通常按照功能区分可以将服务器分为：
   >
   > - 应用服务器：服务端语言运行的服务器（Java、Nodejs），比如放淘宝应用的位置；
   > - 数据库服务器：放数据库的服务器；
   > - 存储服务器：放大型文件的服务器，如各种网盘；
   > - CDN 服务器：放**静态资源**的服务器，如 JS、CSS、图片、字体等。

2. 通过打包工具（Webpack）来做资源文件的物理打包（相对没有第一种灵活）

除了从资源层面解决问题，还可以从项目代码考虑。

1. 对于引入的一些大型的第三方库，如 lodash、elementUI，务必使用按需加载（一般都是用 Babel 插件实现的）；
2. 对于 SPA ，可以使用前端的路由懒加载减少请求；
3. 使用防抖、节流函数减少请求次数；
4. 给一些按钮组件添加禁用的效果，不让用户频繁点击，减少 HTTP 请求。

### 2.减少资源大小

1. JS 和 CSS 文件可以通过 Webpack 进行**混淆**和**压缩**

   - 混淆：将 JS 代码进行加密，最大程度减少代码，比如将长变量名变成单个字母等；
   - 压缩：去除注释、空行以及 console.log 等调试代码。

2. 图片压缩

   - 通过自动化工具来压缩图片

     - 熊猫站（https://www.tinypng.com），智能压缩 PNG 和 JPG 图片的一个网站；
     - 可以对图片进行**等比例无损压缩**（通过相似颜色`量化`的技术来**减少颜色数量**，以及**将不必要的元数据删除**，如照拍摄图片的设备等数据）；
     - 手动上传到网站进行压缩有些麻烦，可以通过使用 npm 安装开源包进行批量处理。

   - 使用 WebP 格式

     > https://www.upyun.com/webp

     WebP 的优势：根据 Google 测试，同等条件下等比例无损压缩后的 WebP 比 PNG 少了 26% 的体积，并且图片越多，压缩后的体积优势越明显。

     ![](https://gitee.com/gainmore/imglib/raw/master/img/20210902100108.png)

     ![](https://gitee.com/gainmore/imglib/raw/master/img/20210902100120.png)

3. 服务器开启 gzip 进行全部资源压缩

   - gzip 是一种压缩文件格式，可以对任何文件进行压缩，类比于文件压缩；
   - 可以通过 nginx 服务器的配置项进行开启，或者使用 nodejs 中的中间件 compression 进行开启。

### 3.CDN 加速

CDN 就是离用户最近的一台服务器，把所有静态资源都同步到这台服务器上，使用户访问资源的速度变快。

另外由于 CDN 服务器的地址一般和主服务器的地址不同，所以破除浏览器对同一个域名发送请求的限制。

比如在 HTTP1.1 中，要求：对于同一个源（协议、域名、端口都相同）的 URL，浏览器允许同时打开最多 6个 TCP 连接（最多同时发送6个请求）。

使用 CDN 地址的话，由于 CDN 地址一般和主服务器不同，所以可以避免这种限制。

除此之外，可以可以使用 HTTP2.0 （https://http2.akamai.com/demo），引入了多路复用的机制，可以最大化发送请求数量。

### 4.Webpack 打包优化

1. 使请求更少。使用 Webpack 进行物理打包。

2. 使资源更小。使用 Webpack 进行代码的混淆和压缩，同时可以拆包然后结合路由懒加载按需加载资源文件，所有与 Webpack 优化相关的配置都是在 `optimization` 这个配置项里管理的。

   > 从 webpack4 开始，开源根据 mode 选项进行不同的优化。
   >
   > development：不混淆、不压缩、不优化
   >
   > production：混淆、压缩、自动优化

对文件进行拆包处理，那么肯定会造成文件变多，是否矛盾？

其实不矛盾，因为拆包后的文件，不可能同时加载，所以不会造成同一时间资源请求过多。

但是要注意**打包策略**。我们通常会把包分为两类：**第三方包** 和 **自己实现的包（又可以分为公共的和非公共的）**。

所以我们可以把第三方包打一个包，公共代码打一个包，非公共代码打一个包。

> 第三方包：改动频率小；
>
> 公共代码包：改动频率中；
>
> 非公共代码包：改动频率高。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210902101645.png)

因此，可以将 **打包策略** 和 **网络缓存** 结合进行优化。

- 对于不经常改动的资源，如第三方包，可以使用强缓存 `Cache-Control: max-age=3153600`（缓存一年）并结合协商缓存 `Etag` 使用（一旦文件名变动才会下载新的文件）；
- 对于需要频繁变动的资源，如非公共代码包，可以使用协商缓存。

### 5.用户感知优化

减少DOM操作

- 无论是浏览器的 DOM 和 BOM，还是 Nodejs，它们都是基于 Javascript 引擎之上开发出来的；
- DOM 和 BOM 的处理最终都是要被转换成 Javascript 引擎能够处理的数据；
- 这个转换的过程非常耗时；
- 所以在**浏览器中最消耗性能的操作就是操作 DOM**。

渲染十万条数据，如何避免卡顿?

普通方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <ul id="list"></ul>
  <script>
    console.time()
    const container = document.getElementById('list')
    const total = 1000000
    const fragment = document.createDocumentFragment()
    for (let i = 0; i < total; i++) {
      const li = document.createElement('li')
      li.innerText = `${Math.floor(Math.random() * total)}`
      fragment.appendChild(li)
    }
    container.appendChild(fragment)
    console.timeEnd()
  </script>
</body>
</html>
```

可以看出，这种方式一次性把 1000000 条数据都加到文档片段中，然后渲染出来，这么大的数据量十分容易造成卡顿。



优化方式：

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <ul id="list"></ul>
  <script>
    console.time()
    const container = document.getElementById('list')
    const total = 1000000
    let page = 20, cur = 0

    function add() {
      const fragment = document.createDocumentFragment()
      for (let i = 0; i < page; i++) {
        const li = document.createElement('li')
        li.innerText = `${Math.floor(Math.random() * total)}`
        fragment.appendChild(li)
      }
      container.appendChild(fragment)
      cur += page
      loop()
    }
    
    function loop() {
      if (cur < total) {
        // 类似于 setTimeout(add, 16) 每渲染一帧调用一次函数
        window.requestAnimationFrame(add)
      }
    }
    loop()
    console.timeEnd()
  </script>
</body>
</html>
```

这种方式采用分段渲染的方式，比如每一帧渲染 20 条数据，即每 16.67ms 渲染 20 条数据，这样逐帧渲染可以避免因为数据量过大而造成卡顿。



## 总结

### 1.减少HTTP请求

减少图片请求：

1. 通过懒加载的方式处理非首屏的图片；
2. 对于小图标可用采用 iconfont 的方式解决；
3. 对于小图片可用采用 Sprite 精灵图解决或者将小图片转成 base64 格式让它们随着 CSS 文件一起加载。比如在 webpack 中配置 url-loader，给它设置一个 limit 值，当图片小于这个 limit 值就会将图片转成 base64 格式，但这种方法会导致 CSS 文件非常臃肿。

减少首页请求：

1. 使用路由懒加载，按需加载路由；
2. 通过 nginx 服务器（可用来做 CDN，用来处理静态资源）来做资源文件的合并（combo）-- 将多个资源的 CDN 地址合并到一起，同时加载多个 JS 文件或 CSS 文件；
3. 通过打包工具（Webpack）来做资源文件的物理打包（相对没有第一种灵活）；
4. 对于引入的一些大型的第三方库，如 lodash、elementUI，务必使用按需加载（一般都是用 Babel 插件实现的）；
5. 使用防抖、节流函数减少请求次数；
6. 给一些按钮组件添加禁用的效果，不让用户频繁点击，减少 HTTP 请求；
7. 服务器可以给一些频繁访问的资源设置缓存，通常可以使用 expires 或者 cache-control 字段给资源设置一个过期时间，这样在这段时间内，就可以减少HTTP请求；

### 2.减少静态资源的体积

1. 服务端开启 gzip 压缩，如果后端是 nodejs 写的，可以使用中间件 compression 开启 gzip 压缩；
2. JS 和 CSS 文件可以通过 Webpack 进行**混淆**和**压缩**；
3. 图片可以工具进行等比例无损压缩，或者使用 webp 格式减小体积；
4. 优化 CSS 代码，css 代码尽量不要冗余，减少 css 样式的嵌套深度；
5. 使用 HTTP2 。

### 3.用户感知优化

1. 进度条、加载动画、骨架屏；
2. 减少 DOM 操作，用 requestAnimationFrame 逐帧渲染动画，减少卡顿。

### 4.减少请求时间

1. 使用 CDN，利用离用户最近的服务器，缩短资源传输距离，减少请求时间。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210901201639.png)



## 参考资料

- https://www.bilibili.com/video/BV15b4y1R7pj
- https://www.bilibili.com/video/BV1vf4y1h7rg

