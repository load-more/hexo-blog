---
title: 38 “幸福外卖”项目【2】
date: 2021-03-17 23:09:50
tags: [Vuejs, swiper, v-for]
categories: 项目
---

【swiper的使用】以及【v-for循环图片路径，图片不显示的解决办法】

<!-- more -->

晚上在做项目里的轮播图的时候，遇到了一些问题。通过查阅网上的资料，也得到了解决。

先从简单的说起，我在使用 v-for 遍历图片路径的时候，图片怎么也显示不出来。

```html
      <div class="swiper-slide" v-for="(item, index) in 5" :key="index">
        <img :src="'@/assets/img/banner'+(index+1)+'.jpg'" alt="">
      </div>
```

上面，我通过字符串拼接将图片的路径动态传入，乍一看，也没有明显的错误，但为什么图片就是显示不出来呢？

后来，百度了一下，第一条方案就解决了问题。

解决办法就是用 `require()` 将图片引入：

```html
      <div class="swiper-slide" v-for="(item, index) in 5" :key="index">
        <img :src="require('@/assets/img/banner'+(index+1)+'.jpg')" alt="">
      </div>
```

---

再来说说第二个问题，就是轮播图的问题。

在学 JS 的时候，尝试过用原生的 JS 去图片无缝轮播的效果，虽然原理不难，但是实现起来还是比较复杂的。而网上也有相应的第三方库，直接引用即可，不用重复造轮子。

我这里使用到的第三方库就是 `swiper`，以下是使用方法：

1. 安装 swiper：

   ```shell
   npm install swiper@4
   ```

   **注意，要安装版本4，如果安装最新版本，会出现一些小问题。（亲自踩坑）**

2. 导入文件：

   ```javascript
     import Swiper from 'swiper'
     import "swiper/dist/css/swiper.min.css"
   ```

3. 编写模板：

   ```vue
   <template>
     <div class="swiper-container">
         <div class="swiper-wrapper">
             <slot></slot>
         </div>
         <div class="swiper-pagination"></div>
         
         <div class="swiper-button-prev"></div>
         <div class="swiper-button-next"></div>
   
     </div>
   </template>
   ```

4. 配置：

   ```vue
   mounted() {
         new Swiper ('.swiper-container', {
             direction: 'horizontal',
             loop: true,
             // 分页器
             pagination: {
               el: '.swiper-pagination',
               clickable: true
             },
             observer: true, // 修改swiper自己或子元素时，自动初始化swiper，必须加入
             observeParents: true, // 修改swiper的父元素时，自动初始化swiper，必须加入
   
             // 自动播放
             autoplay: {
               delay: 2000,
               disableOnInteraction: false
             },
             // 前进后退按钮
             navigation: {
               nextEl: '.swiper-button-next',
               prevEl: '.swiper-button-prev',
             }, 
           })        
   }
   ```

   

