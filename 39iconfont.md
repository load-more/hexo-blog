---
title: 39 “幸福外卖”项目【3】
date: 2021-03-18 20:46:17
tags: [Vuejs, iconfont]
categories: 项目
---

解决引入iconfont不显示问题

<!-- more -->

今天，用阿里图标库引入图标字体遇到了一个小问题：图标显示不出来？

我反复确认类名有没有写错，但都没有错，但为什么就是显示不出来呢？

其实原因也很简单，因为我的图标字体不是一次性导入项目中的，所以引用的链接里的图标还没有更新。

点击红字，更新代码，将 index.html 中的引用代码更改即可。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210318205138.png)