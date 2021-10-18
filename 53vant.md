---
title: 53 “幸福外卖”项目【14】
date: 2021-03-27 22:19:54
tags: [Vuejs, VantUI]
categories: 项目
---

使用一款移动端UI框架 **VantUI** 美化界面。

VantUI 中在线引入 iconfont 图标的方法。

<!-- more -->

当使用上一款成熟的UI框架之后，才意识到之前重复造轮子的过程是多么愚蠢。

前几天还在乐此不疲的写组件，现在用上了框架才知道面向CV编程的感觉是多么的爽~~

这是 Vant 的官方文档：https://vant-contrib.gitee.io/vant/#/zh-CN/

我能想到的UI组件里面差不多都有，而且使用起来也很简单，让我不禁感叹之前花了多少时间在写UI上面。。。

Swipe、LazyLoad、TabBar、Switch、Toast......这些常用组件我都重新用 Vant 写了一遍，代码简洁了不少，虽然电脑上显示不咋地，但在手机上看着效果还真不错。

```javascript
import {
  Button, Cell, CellGroup, Dialog, Toast, Switch,
  Tabbar, TabbarItem, Icon, Lazyload, Tabs, Tab,
  Overlay, Popup
} from 'vant'

Vue.use(Button)
Vue.use(Cell)
Vue.use(CellGroup)
Vue.use(Dialog)
Vue.use(Toast)
Vue.use(Switch)
Vue.use(Tabbar)
Vue.use(TabbarItem)
Vue.use(Icon)
Vue.use(Lazyload)
Vue.use(Tabs)
Vue.use(Tab)
Vue.use(Overlay)
Vue.use(Popup)
```

不过，这里有个图标引入的问题值得注意。虽然 Vant 内部也有许多 icon 图标，但毕竟不是应有尽有，很多还需要我们在 iconfont 找。

但 Vant 中有自己的 icon 标签，之前使用 `<i class='iconfont xxx'></i>` 引入图标的方式不再适用了。

举个例子，我们想更改 TabBar 中的图标，需要用到 icon 属性：

```html
  <van-tabbar v-model="active" active-color="red" inactive-color="green" route>
    <van-tabbar-item icon='iconfont iconwaimai' to='/home'>外卖</van-tabbar-item>
    <van-tabbar-item icon='iconfont iconsearch' to='/search'>搜索</van-tabbar-item>
    <van-tabbar-item icon='iconfont iconziyuan' to='/order'>订单</van-tabbar-item>
    <van-tabbar-item icon='iconfont iconbiaoqianA01_wode-70' to='/profile'>我的</van-tabbar-item>
  </van-tabbar>
```

如果直接使用 i 标签那种引用方式，图标将不会显示。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210327224328.png)

这时候，需要用到一些属性了。

如果我们直接对 icon 属性赋值，可以在浏览器中查看到图标的属性：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210327224631.png)

可以看到，i 中的 class 属性默认会有 van-icon 以及 由 van-icon 和 icon 属性值 通过 - 拼接的字符串；

如果给标签设置一个 icon-prefix 属性：

`<van-tabbar-item icon='iconfont iconwaimai' icon-prefix='hhh' to='/home'>外卖</van-tabbar-item>`

在浏览器中可以看出变化：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210327225001.png)

van-icon 已经被替换成了 hhh，所以 icon-prefix 属性值。

这里，我们就大概可以想到引入的办法了。

```html
  <van-tabbar v-model="active" active-color="red" inactive-color="green" route>
    <van-tabbar-item icon=' iconwaimai' icon-prefix='iconfont' to='/home'>外卖</van-tabbar-item>
    <van-tabbar-item icon=' iconsearch' icon-prefix='iconfont' to='/search'>搜索</van-tabbar-item>
    <van-tabbar-item icon=' iconziyuan' icon-prefix='iconfont' to='/order'>订单</van-tabbar-item>
    <van-tabbar-item icon=' iconbiaoqianA01_wode-70' icon-prefix='iconfont' to='/profile'>我的</van-tabbar-item>
  </van-tabbar>
```

icon-prefix 设置成 'iconfont'，icon 设置成 icon 的名称（注意前面一定要加上一个空格）

在浏览器中观察类名的变化：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210327225526.png)

这个形式是不是和 `<i class='iconfont iconwaimai'></i>` 十分相近。

看一下页面，可以发现图标也终于正常显示了。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210327225733.png)

