---
title: 56 CSS 常见问题【汇总】
date: 2021-04-02 22:41:34
tags: [CSS, 面试题]
categories: CSS
---

汇总。

<!-- more -->

## 画一条 0.5px 的线

- 使用 viewport

  在 html 中加入下面一个标签，表示将页面所有元素都缩放到原来的 0.5 倍。

  ```html
    <meta name="viewport" content="width=device-width, initial-scale=0.5, minmum-scale=0.5, maxmum-scale=0.5">
  ```

  *注意，这种方法只能在移动端上生效。*

- **采用transform: scale()的方式**

  ```html
    <div style="height: 1px; background-color: black;margin-bottom: 20px;"></div>
    <div style="height: 1px; background-color: red; transform: scaleY(0.5);"></div>
  ```

  Y 方向缩小到 0.5 倍， 这样就能作出 0.5 px 的线了。

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210402225204.png)

## transition 和 animation 的区别

- transition 的使用方法

  1. transition-property

  ​        \- 设置要过渡的属性，如宽度、高度、颜色等

  2. transition-duration

  ​        \- 设置过渡持续的时间, 单位有s、ms

  3. transition-timing-function

  ​        \- 设置过渡的时序函数

  ​        \- 可选值：

  ​          \- ease (default)  慢速开始，先加速，后减速停止

  ​          \- linear      匀速运动

  ​          \- ease-in      加速运动

  ​          \- ease-out     减速运动

  ​          \- ease-in-out    先加速，后减速

  ​          \- cubic-bezier()  指定贝塞尔曲线

  ​            https://cubic-bezier.com

  ​          \- steps()      分步执行过渡效果

  4. transition-delay

  ​        \- 等待一段时间后执行过渡

  5. transition

  ​        \- 简写，位置之间可随意调换，但transition-duration必须要在transition-delay的前面,并且

  ​         属性与对应的持续时间要用空格隔开

- animation 的使用方法

  1. animation-name

  ​          \- 设置动画名字

  2. animation-duration

  ​          \- 设置动画持续时间

  3. animation-timing-function

  ​          \- 设置动画的时序函数

  4. animation-delay

  ​          \- 设置动画延迟时间

  5. animation-iteration-count

  ​          \- 设置动画的持续次数

  6. animation-direction

  ​          \- 设置动画的方向

  ​          \- 可选值：

  ​            normal (default)  从from到to

  ​            reverse       从to到from

  ​            alternate      交替，从from到to，再从to到from

  ​            alternate-reverse  交替，从to到from，再从from到to

  7. animation-play-state

  ​          \- 设置动画的状态

  ​          \- 可选值：

  ​            running (default)

  ​            paused

  8. animation-fill-mode

  ​          \- 设置动画的填充模式

  ​          \- 可选值：

  ​            none (default) 动画执行完毕后元素回到原来的位置

  ​            forwards    动画执行完毕后元素会停止在动画结束的位置

  ​            backwards    动画在延迟等待时，元素就会处于开始位置，例如给颜色设置了动画，延迟时颜色就变成了一开始的颜色

  ​            both      结合了forwards和backwards的特点

  9. animation

  ​          \- 简写

  ​          \- animation: name duration timing-function delay iteration-count direction fill-mode;

  ​	之后，需要设置一个关键帧

  ```css
          /* 首先要设置一个关键帧 */
          @keyframes move{
              form{
                  margin-left:0;
                  background-color: seashell;
              }
              30% {
                  background-color: red;
              }
              60% {
                  background-color: blue;
              }
              to{
                  margin-left: 950px;
                  background-color: seagreen;
              }
          }
  ```

  最后，调用即可。

  ```css
              animation: move 4s infinite linear both 2s;
  ```

- 两者区别

  Animation 和 transition 大部分属性是相同的，它们都是随时间改变元素的属性值，它们的主要区别是 transition 需要触发一个事件才能改变属性，而 animation 不需要触发任何事件就能随时间改变属性值，并且 transition 为 2 帧，从 from .... to，而 animation 可以一帧一帧的执行。



## BFC

> 原文链接：https://blog.csdn.net/sinat_36422236/article/details/88763187

### BFC 定义

BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。

### BFC 布局规则

- 内部的Box会在垂直方向，一个接一个地放置。

- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。

- 每个盒子（块盒与行盒）的margin box的左边，与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。

- BFC的区域不会与float box重叠。

- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。

- 计算BFC的高度时，浮动元素也参与计算。

### BFC 创建

- 1、float的值不是none。
- 2、position的值不是static或者relative。
- 3、display的值是inline-block、table-cell、flex、table-caption或者inline-flex
- 4、overflow的值不是visible

### BFC 作用

- 防止外边距重叠
- 自适应两栏布局
- 清除浮动的影响

## 垂直居中的方法

### margin: auto 法

```html
  <style>
    .box {
      height: 100px;
      width: 100px;
      background-color: red;
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      margin: auto;
    }
  </style>
<body>
  <div class="box"></div>
</body>
```

### translate() 法

```html
  <style>
    .box {
      height: 100px;
      width: 100px;
      background-color: red;
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%); /* 相对自身尺寸平移 */
    }
  </style>
<body>
  <div class="box"></div>
</body>
```

### table-cell（未脱离文档流的）

```html
  <style>
    .container {
      width: 600px;
      height: 600px;
      border: 1px solid black;
      display: table-cell;
      vertical-align: middle; /* 只能垂直居中 */
    }
    .box {
      height: 100px;
      width: 100px;
      background-color: red;
    }
  </style>
<body>
  <div class="container">
    <div class="box"></div>
  </div>
</body>
```

### 使用 Flex 布局

```html
 <style>
    .container {
      width: 600px;
      height: 600px;
      border: 1px solid black;
      display: flex;
      align-items: center; /* 一根主轴垂直居中 */
      justify-content: center; /* 水平居中 */
    }
    .box {
      height: 100px;
      width: 100px;
      background-color: red;
    }
  </style>
<body>
  <div class="container">
    <div class="box"></div>
  </div>
</body>
```

## 块元素和行元素

- 块元素：独占一行，会自动填满父元素，可以设置 margin 和 pading 以及高度和宽度；
- 行元素：不会独占一行，width 和 height 会失效，并且在垂直方向的 padding 和 margin 会失效。

### 多行文本的溢出省略号

```css
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;
```

## visibility=hidden, opacity=0，display:none

- visibility=hidde，元素隐藏起来了，不会改变页面布局，而且不会触发该元素绑定的事件；

- opacity=0，该元素隐藏起来了，不会改变页面布局，如果该元素已经绑定一些事件，如 click 事件，那么点击该区域，也能触发点击事件的回调函数；
- display=none， 把元素隐藏起来，并且会改变页面布局，可以理解成在页面中把该元素删除掉一样。

## 双边距重叠问题（外边距折叠）

多个相邻（兄弟或者父子关系）普通流的块元素垂直方向 marigin 会重叠折叠的结果为：

- 两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。
- 两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。
- 两个外边距一正一负时，折叠结果是两者相加的和。