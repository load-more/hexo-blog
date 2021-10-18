---
title: 30 fontawesome
date: 2021-03-08 16:18:18
tags: [Vuejs, fontawesome]
categories: Vuejs
headimg: https://gitee.com/gainmore/imglib/raw/master/img/20210308161942.png
---

最近开始做一个关于购物商城的项目，引入图标字体时遇到了一些麻烦。

<!-- more -->

一开始想使用 fontawesome ，按照网上的教程，安装了很多的依赖包，不过都没什么效果。

后来才发现，简单的使用只要安装一个包就够了。

1. 安装：

   ```shell
   npm install --save font-awesome
   ```

2. 引入依赖样式文件，在 main.js 中引入：

   ```javascript
   import 'font-awesome/css/font-awesome.css'
   ```

   {% wavy 注意：这里不要在 App.vue 中引用。 %}

   一开始我是在 App.vue 里的 script 下导入的，结果发现没效果。

   tip：

   ```vue
   <script scoped>
   	@import 'xxx/xxx'; 注意，导入多行时，一定要加分号
   </script>
   ```

   

3. 查询字体样式，使用：

   ```html
   <i class='fa fa-user'></i>
   ```

   