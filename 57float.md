---
title: 57 CSS 之 float
date: 2021-04-03 20:30:45
tags: [CSS]
categories: CSS
---

深入了解 CSS 中的浮动布局、BFC、高度塌陷以及外边距折叠等问题。

<!-- more -->

## float 布局

### float 属性

- none 默认值，不浮动
- left 元素向左浮动
- right 元素向右浮动

注意：

1. 元素设置浮动以后，水平布局的等式将不需要强制成立，即一些如margin-right的值将不再自动设置；
2. 元素设置浮动以后，会完全从文档流中脱离，不再占用文档流的位置，所以元素下面的还在文档流中的元素会自动向上移动。

### 浮动的特点

1. 浮动元素会完全脱离文档流，不再占用文档流的位置；
2. 设置浮动以后元素会向父元素的左侧或右侧移动；
3. 浮动元素默认不会从父元素中移除；
4. 浮动元素向左或向右移动时，不会超过它前边的其它浮动元素；
5. 如果浮动元素上边还有其它未浮动的元素，则其不会向上移动，就好像被一堵墙挡住一样；
6. 浮动元素不会超过它上边的浮动元素，最多和它一样高。

### 脱离文档流后的特点

- 块元素
  - 块元素将不再独占一行
  - 块元素的高度和宽度默认都被内容撑开
- 行内元素
  - 行内元素脱离文档流之后会变成块元素，特点和块元素一样
- 因此，脱离文档流之后，将不再需要区分块元素和行内元素了。

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403153337.png)

## BFC

BFC（Block Formating Context）：块级格式化环境

### 特点

- 内部的Box会在垂直方向，一个接一个地放置。

  ```
  COPY<style>
      .box1, .box2, .box3 {
        width: 200px;
        height: 100px;
      }
      .box1 {
        background-color: orange;
      }
      .box2 {
        background-color: red;
      }
      .box3 {
        background-color: blue;
      }
    </style>
  </head>
  
  <body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div>
  </body>
  ```

  效果：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403154252.png)

  由于这里的 div 是块元素，所以会垂直排列，如果设置为 `inline-block` ，则会变为水平排列：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403154423.png)

- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。

  ```
  COPY  <style>
      .box1, .box2, .box3 {
        width: 200px;
        height: 100px;
      }
      .box1 {
        background-color: orange;
        margin: 50px;
      }
      .box2 {
        background-color: red;
        margin: 50px;
      }
      .box3 {
        background-color: blue;
        margin: 50px;
      }
    </style>
  </head>
  
  <body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div>
  </body>
  ```

  我们给三个元素都设置外边距，上下左右的 margin 都为 50px，而元素高度都为 100px，所以可以推测两个元素间的间隔为一个元素的高度值，然而效果却不是这样：

  ![image-20210403154913501](file:///C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210403154913501.png)

  可以看到，间隔竟然还是 50px ！

  这种现象就称为**外边距的折叠**，原因是这三个元素都同属于一个 BFC 。

  所以，将这三个元素设置在三个 BFC 之下，就能解决这个问题。

  ```
  COPY  <style>
      .box1, .box2, .box3 {
        width: 200px;
        height: 100px;
        display: inline-block;
      }
      .box1 {
        background-color: orange;
        margin: 50px;
      }
      .box2 {
        background-color: red;
        margin: 50px;
      }
      .box3 {
        background-color: blue;
        margin: 50px;
      }
      .container {
        overflow: hidden;
      }
    </style>
  </head>
  
  <body>
    <div class="container">
      <div class="box1"></div>
    </div>
    <div class="container">
      <div class="box2"></div>
    </div>
    <div class="container">
      <div class="box3"></div>
    </div>
  </body>
  ```

  效果：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403155317.png)

- 每个盒子（块盒与行盒）的margin box的左边，与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。

- BFC的区域不会与float box重叠。

  ```
  COPY  <style>
      .box1, .box2, .box3 {
        width: 200px;
        height: 100px;
      }
      .box1 {
        background-color: orange;
        float: left;
      }
      .box2 {
        background-color: red;
      }
      .box3 {
        background-color: blue;
      }
    </style>
  </head>
  
  <body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div>
  </body>
  ```

  当给 box1 设置向左浮动后，box1 就脱离了文档流，box2 和 box3 就会向上移动，由于三个元素大小一样，所以 box2 就被 box1 挡在了下面。

  为了解决这一问题，我们就可以利用 BFC 不会与 float box 重叠这一特点。

  ```
  COPY  <style>
      .box1, .box2, .box3 {
        width: 200px;
        height: 100px;
      }
      .box1 {
        background-color: orange;
        float: left;
      }
      .box2 {
        background-color: red;
      }
      .box3 {
        background-color: blue;
      }
      .container {
        overflow: hidden;
      }
    </style>
  </head>
  
  <body>
    <div class="box1"></div>
    <div class="container">
      <div class="box2"></div>
    </div>
    <div class="box3"></div>
  </body>
  ```

  效果：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403160131.png)

  可以看到，box2 已经被 box1 挤到了右边，两者并没有重叠。

- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。

  ```
  COPY<style>
      .box1, .box2, .box3 {
        width: 200px;
        height: 100px;
      }
      .box1 {
        background-color: orange;
      }
      .box2 {
        width: 30px;
        height: 30px;
        background-color: black;
        margin: 300px;
      }
      .box3 {
        background-color: blue;
      }
      .container {
        width: 200px;
        height: 100px;
        background-color: red;
        overflow: hidden;
      }
    </style>
  </head>
  
  <body>
    <div class="box1"></div>
    <div class="container">
      <div class="box2"></div>
    </div>
    <div class="box3"></div>
  </body>
  ```

  效果：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403160707.png)

  可以看出，container 是一个 BFC，box2 是其中的一个子元素，设置了 `margin: 300px`，虽然已经超出了 container 的范围，但也没有影响到其它元素的布局。而且超出部分在页面中不可见（`overflow:hidden`）。

- 计算BFC的高度时，浮动元素也参与计算。

  ```
  COPY <style>
      .container {
        border: 1px solid black;
        width: 300px;
      }
      .box {
        float: left;
        height: 300px;
        width: 100px;
        background-color: red;
      }
    </style>
  </head>
  
  <body>
    <div class="container">
      <div class="box"></div>
    </div>
  </body>
  ```

  效果：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403161320.png)

  可以看出，当子元素设置为浮动之后，父元素的高度塌陷了。

  为了解决这一个问题，可以将父元素设置成一个 BFC。

  ```
  COPY<style>
      .container {
        border: 1px solid black;
        width: 300px;
        overflow: hidden;
      }
      .box {
        float: left;
        height: 300px;
        width: 100px;
        background-color: red;
      }
    </style>
  </head>
  
  <body>
    <div class="container">
      <div class="box"></div>
    </div>
  </body>
  ```

  效果：

  ![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403161526.png)

### 创建

1. 根元素 html（注意，不是body，有的博客说是body），html 本身就是一个 BFC；
2. float的值不是none，即 float 的属性值为 left 或者 right；
3. position 的属性值为 absolute 或者 fixed；
4. display的值是inline-block、table-cell、flex、table-caption或者inline-flex
5. overflow的值为 hidden、scroll 或者 auto。

### 作用

- 防止外边距重叠
- 自适应两栏布局
- 清除浮动的影响

## clear

由于一个元素的浮动，会导致之后的元素位置向上移动，即之后的元素也会受到浮动元素的影响。

如果不希望某个元素因为其他元素的浮动而改变位置，可以通过 clear 属性来清除浮动元素对当前元素的影响。

### 属性值

- left：清除左侧浮动元素对当前元素的影响
- right：清除右侧浮动元素对当前元素的影响
- both：清除两侧浮动元素中影响最大的

### 原理

设置 clear 之后，浏览器会自动元素添加一个上边距，以使其位置不受其他元素的影响。

## 解决高度塌陷

问题：

```
COPY  <style>
    .container {
      border: 1px solid black;
      width: 300px;
    }
    .box {
      float: left;
      height: 300px;
      width: 100px;
      background-color: red;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="box"></div>
  </div>
</body>
```

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210403161320.png)

### 1.利用 html 结构 + clear 属性

在浮动元素后面添加一个 div，给这个 div 设置一个 clear 属性以清除浮动元素对这个 div 的影响，而父元素也会被撑开。

```
COPY  <style>
    .container {
      border: 1px solid black;
      width: 300px;
    }
    .box {
      float: left;
      height: 300px;
      width: 100px;
      background-color: red;
    }
    .clear {
      clear: both;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="box"></div>
    <div class="clear"></div>
  </div>
</body>
```

虽然这种方法能够解决问题，但是却改变了 html 的结构。由于“高度塌陷”是 CSS 中的问题，所以尽量只用 CSS 解决。

### 2.使用 overflow 触发 BFC

```
COPY  <style>
    .container {
      border: 1px solid black;
      width: 300px;
      overflow: hidden; /* 触发BFC，计算浮动元素高度，防止高度塌陷 */
    }
    .box {
      float: left;
      height: 300px;
      width: 100px;
      background-color: red;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="box"></div>
  </div>
</body>
```

### 3.使用 ::after 伪元素 + clear 属性

```
COPY <style>
    .container {
      border: 1px solid black;
      width: 300px;
    }
    .box {
      float: left;
      height: 300px;
      width: 100px;
      background-color: red;
    }
    .clearfix::after{
      /* 通过伪元素，在container末尾添加内容 */
      content: "";
      /* 设置其不会受到浮动元素的影响 */
      clear: both;
      /* 因为添加的文本属于行内元素，应将其转换成块元素，独占一行 */
      display: block;
    }
  </style>
</head>

<body>
  <div class="container clearfix">
    <div class="box"></div>
  </div>
</body>
```

比如（没有设置 display：block）：

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210824214650.png)

## 解决外边距折叠

### 1.使用 overflow 触发 BFC

 BFC 中有示例

### 2.使用 ::before 伪元素 + display

```
COPY.clearfix::before{
    /* 在父元素的前面添加一个元素,起到占位符的作用 */
    content: "";
    display: table;
}
```

## clearfix

为同时解决 **高度塌陷** 和 **外边距折叠** 问题，可以在相应的父元素中添加 clearfix 属性。

```
COPY/* 两种方法合并 */
.clearfix::before,
.clearfix::after{
    content:"";
    display:table;
    clear:both;
}
```

## 自适应两栏布局

BFC 的另一个重要作用是可以创建自适应布局。

### 1.float

```
COPY<style>
    * {
      margin: 0;
      padding: 0;
    }
    .left {
      height: 300px;
      width: 100px;
      background-color: red;
      float: left;
    }
    .right {
      height: 300px;
      background-color: green;
      overflow: hidden;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="left"></div>
    <div class="right">
      <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. At accusantium officia quam iusto. Laborum accusamus, sed libero inventore numquam in, eos nemo adipisci error reprehenderit iure incidunt possimus placeat vitae!</p>
    </div>
  </div>
</body>
```

效果：

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210423203131.png)

这种实现方法的原理就是利用 **BFC 区域不会和 float Box 重叠的特点**，将左栏设置左浮动，右栏设置 `overflow: hidden` 创建一个 BFC，使得右栏不会和左栏重叠，而右栏的宽度会自适应。

### 2.使用绝对定位

```
COPY  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .container {
      position: relative;
    }
    .left {
      height: 300px;
      width: 100px;
      background-color: red;
      position: absolute;
    }
    .right {
      height: 300px;
      background-color: green;
      margin-left: 100px;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="left"></div>
    <div class="right">
      <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. At accusantium officia quam iusto. Laborum accusamus, sed libero inventore numquam in, eos nemo adipisci error reprehenderit iure incidunt possimus placeat vitae!</p>
    </div>
  </div>
</body>
```

效果：

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210423203732.png)

这种方法的原理是将左栏设置绝对定位，使其脱离文档流，然后给右栏设置一个 `margin-left`，让左外边距的大小刚好等于左栏的宽度，实现右栏的自适应。

### 3.使用flex

```
COPY  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .container {
      display: flex;
    }
    .left {
      height: 300px;
      background-color: red;
      flex: 0 0 200px; /* 前两个0代表当空间剩余或不足时，元素既不自动放大也不自动缩小，后面200px代表给主轴分配的空间，该值优先级比width更高 */
    }
    .right {
      height: 300px;
      background-color: green;
      flex: 1;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="left"></div>
    <div class="right">
      <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit. At accusantium officia quam iusto. Laborum accusamus, sed libero inventore numquam in, eos nemo adipisci error reprehenderit iure incidunt possimus placeat vitae!</p>
    </div>
  </div>
</body>
```

效果：

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210423204807.png)

此方法原理是将左栏设置为 `flex: 0 0 100px`，即相当于 `flex-grow: 0; flex-shrink: 0; flex-basis:100px`

当主轴空间剩余或不足时，左栏的宽度不会自动放大或缩小以充满父元素；

给右栏设置为 `flex: 1`，即表示当空间剩余时，右栏会将空间撑满。

### 4.使用inline-block

```
COPY...
```

### 5.使用table布局

```
COPY...
```

总结：可以发现，实现自适应布局的原理基本上是利用了创建 BFC 那五种情况而已。

## 圣杯布局

主要使用 flex 布局实现。

```
COPY  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .header, .footer {
      height: 50px;
      background-color: gray;
      text-align: center;
      line-height: 50px;
    }
    .container {
      display: flex;
      color: white;
      height: 400px;
      line-height: 400px;
      text-align: center;
    }
    .left {
      width: 100px;
      background-color: green;
    }
    .middle {
      background-color: red;
      flex: 1;
    }
    .right {
      width: 100px;
      background-color: blue;
    }
  </style>
</head>
<body>
  <div class="header">#header</div>
  <div class="container">
    <div class="left">#left</div>
    <div class="middle">#middle</div>
    <div class="right">#right</div>
  </div>
  <div class="footer">#footer</div>
</body>
```

效果：

![img](https://gitee.com/gainmore/imglib/raw/master/img/20210423214423.png)