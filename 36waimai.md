---
title: 36 “幸福外卖”项目【1】
date: 2021-03-16 19:39:27
tags: [项目, Vuejs]
categories: 项目
---

这几天，跟着网上的教程，尝试跟着写个项目。前几天自己尝试独自用 vue.js 以及 node 开发一个电影网站，但是苦于没有经验，很多东西都要在网上不断找资料，非常浪费时间。所以，我想跟着视频练练手，熟悉一下开发流程。

<!-- more -->

## 项目目录设计

写一个项目之前，最重要的就是对自己项目结构有一个基本的划分，如果把所有文件都堆在一起，那么以后的维护和修改是十分困难的，这一点我深有体会。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210316195510.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210316200255.png)

## 初始化配置

项目创建好之后，就要修改一些默认配置。

### reset.css

在 index.html 中引入 reset.css，重置所有默认的 css 样式

![](https://gitee.com/gainmore/imglib/raw/master/img/20210316202306.png)

### 移动端 viewport 适配

替换 index.html 中默认的 meta 标签

```html
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0,user-scalable=no">
```



![](https://gitee.com/gainmore/imglib/raw/master/img/20210316202629.png)

### 解决点击响应延时 0.3s 问题

在 index.html 中加入以下代码：

```html
    <script src="https://as.alipayobjects.com/g/component/fastclick/1.0.6/fastclick.js"></script>
    <script>
      if ('addEventListener' in document) {
      document.addEventListener('DOMContentLoaded', function() {
      FastClick.attach(document.body);
      }, false);
      } 
      if(!window.Promise) {
        document.writeln(
          '<script src = "https://as.alipayobjects.com/g/component/es6-promise/3.2.2/es6-promise.min.js"' + ' > ' +
          ' < ' + ' / ' + 'script > ')
      }
    </script>
```

## footer

### 引入 iconfont 

参考教程：https://www.iconfont.cn/help/detail?spm=a313x.7781069.1998910419.d8cf4382a&helptype=code

1. 将图标添加入购物车中

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210316222514.png)

2. 将购物车里的图标添加入项目中

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210316222629.png)

   3. 复制项目中的代码

      ![](https://gitee.com/gainmore/imglib/raw/master/img/20210316222716.png)

   4. 在 index.html 中引入代码

      ![](https://gitee.com/gainmore/imglib/raw/master/img/20210316222825.png)

   5. 挑选相应图标并获取类名，应用于页面：

      ![](https://gitee.com/gainmore/imglib/raw/master/img/20210316223409.png)