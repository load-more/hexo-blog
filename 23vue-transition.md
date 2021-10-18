---
title: 23 Vue 过渡
date: 2021-03-05 11:58:42
tags: [Vuejs, transition, CSS]
categories: Vuejs
---

## 单元素过渡

### 1.1CSS过渡

Vue.js 提供了内置的过渡封装组件 `transition`，该组件用于包裹要实现过渡效果的DOM元素。

transition 组件只会把过渡效果应用到其包裹的内容上，而不会渲染额外的DOM元素。

为元素或组件添加过渡效果主要应用在以下情形中：

- 条件渲染（v-if）
- 条件展示（v-show）
- 动态组件
- 组件根节点

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
  <style>
    /* 设置CSS属性名和持续时间（设置淡入淡出效果） */
    .fade-enter-active, .fade-leave-active {
      transition: opacity 1s;
    }
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
  </style>

  <div id="app">
    <button @click='isShow=!isShow'>切换</button>
    <transition name="fade" mode="">
      <p v-if='isShow'>理想是人生的太阳。</p>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          isShow: true
        }
      }
    })
  </script>

</body>
</html>
```

### 1.2过渡的类名介绍

Vue.js 在元素的显示和隐藏的过渡中，提供了6个class来切换

- v-enter
  - 定义进入过渡的开始状态
  - 在元素被插入前生效，在元素被插入之后的下一帧移除
- v-enter-active
  - 定义进入过渡生效时的状态
  - 在整个进入过渡的阶段中使用
  - 这个类可用用来定义进入过渡的时间、延迟和曲线函数
- v-enter-to
  - 定义进入过渡的结束状态
  - 在元素被插入之后下一帧生效（与此同时v-enter被移除），在过渡/动画完成之后移除
- v-leave
  - 定义离开过渡的开始状态
- v-leave-active
  - 定义离开过渡生效时的状态
- v-leave-to
  - 定义离开过渡的结束状态

> 如果<transition> 没有 name 属性，则这些类名默认使用 v- 前缀；
>
> 如果设置了name 属性，使用 name 属性拼接类名作为类名，
>
> 如 <transition name='test'>，则 v-enter 会替换为 test-enter

### 1.3CSS动画

CSS动画用法和CSS过渡类似，但在动画中v-enter类名在节点插入DOM后不会立即删除，而是在animationed事件触发时删除。

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
  <style>
    .scaling-enter-active {
      animation: scaling 1s;
    }
    .scaling-leave-active {
      animation: scaling 1s reverse;
    }


    @keyframes scaling {
      0% {
        transform: scale(0);
      }
      50% {
        transform: scale(1.2);
      }
      100% {
        transform: scale(1);
      }
    }
  </style>

  <div id="app">
    <button @click='isShow=!isShow'>切换显示</button>
    <transition name="scaling" mode="">
      <my-content v-if='isShow'></my-content>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      components: {
        myContent: {
          template: `
          <div>
            <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Non, fuga quo consectetur odio, inventore distinctio tempora illo sit vero veritatis repellat nesciunt dicta nisi qui voluptatibus officiis soluta recusandae facere?</p>
            <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Non, fuga quo consectetur odio, inventore distinctio tempora illo sit vero veritatis repellat nesciunt dicta nisi qui voluptatibus officiis soluta recusandae facere?</p>
          </div>
          `
        }
      },
      data () {
        return {
          isShow: false
        }
      }
    })
  </script>
</body>
</html>
```

### 1.4自定义过渡的类名

除了使用普通的类名（如 -enter、-leave等）外，Vue.js 也允许自定义过渡类名。

自定义的过渡类名优先级高于普通的类名。

通过自定义过渡类名可以使过渡系统和其他第三方CSS动画库（如 animate.css）相结合，实现更丰富的动画效果。

过渡类名可以通过以下六个属性实现：

- enter-class
- enter-active-class
- enter-to-class
- leave-class
- leave-active-class
- leave-to-class

### 1.5 JavaScript 钩子函数

元素过渡效果还可以使用 JavaScript 钩子函数来实现

```html
<transition @before-enter='beforeEnter'
            @enter='enter'
            @after-enter='afterEnter'
            @enter-cancelled='enterCancelled'
            @before-leave='beforeLeave'
            @leave='leave'
            @after-leave='afterLeave'
            @leave-cancelled='leaveCancelled'>
</transition>

new Vue({
	el: '#app',
	methods: {
		beforeEnter(el) {
			...
		},	
		enter(el,done) {
			...
			done()
		},	
		afterEnter(el) {
			...
		},	
		enterCancelled(el) {
			...
		},	
		beforeLeave(el) {
			...
		},	
		leave(el,done) {
			...
			done()
		},	
		afterLeave(el) {
			...
		},	
		leaveCancelled(el) {
			...
		},	
	}
})
```

当只用 JavaScript 过渡时，在enter 和 leave 钩子函数中必须使用 done 进行回调，否则，它们将被同步调用，过渡会立即完成。

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
  <style>
    @keyframes scaling {
      0% {
        transform: scale(0);
      }
      50% {
        transform: scale(1.2);
      }
      100% {
        transform: scale(1);
      }
    }
  </style>

  <div id="app">
    <button @click='isShow=!isShow'>切换显示</button>
    <transition @enter='enter'
                @leave='leave'>
      <my-content v-if='isShow'></my-content>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      components: {
        myContent: {
          template: `
          <div>
            <h2>勤能补拙</h2>
          </div>
          `
        }
      },
      data () {
        return {
          isShow: false
        }
      },
      methods: {
        enter(el, done) {
          el.style.animation = 'scaling 1s'
          done()
        },
        leave(el, done) {
          el.style.animation = 'scaling 1s reverse'
          done()
        }
      }
    })
  </script>
</body>
</html>
```



## 多元素过渡

### 2.1基本用法

最常见的多元素过渡是一个列表和描述这个列表为空消息的元素之间的过渡。

在处理多元素过渡时可以使用 v-if 和 v-else

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
  <style>
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    .fade-enter-active, .fade-leave-active {
      transition: opacity .7s;
    }
  </style>

  <div id="app">
    <button @click='clear'>清空</button>
    <transition name="fade" mode="">
      <ol v-if='items.length'>
        <li v-for='item in items'>{{item}}</li>
      </ol>
      <h2 v-else>内容为空</h2>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          items: ['HTML','CSS','JavaScript','Vue.js','Node.js']
        }
      },
      methods: {
        clear() {
          this.items.splice(0)
        }
      }
    })
  </script>
</body>
</html>
```

### 2.2key属性

当**相同标签名的多个元素**进行切换时，必须通过 key 属性设置唯一的值来标记以让 Vue 区分它们

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
  <style>
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    .fade-enter-active, .fade-leave-active {
      transition: opacity .7s;
    }
  </style>

  <div id="app">
    <button @click='isShow=!isShow'>切换</button>
    <transition name="fade" mode="">
      <p v-if='isShow' key="a">AAAA</p>	<!-- 如果没有设置key，过渡将不会生效 -->
      <p v-else key="b">BBBB</p>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          isShow: true
        }
      }
    })
  </script>
</body>
</html>
```

在一些场景中，也可以通过为同一个元素的 key 属性设置不同的状态来代替 v-if 和 v-else，上面代码可重写为：

```html
    <transition name="fade" mode="">
      <p :key='isShow'>{{isShow ? 'AAAA...' : 'BBBB...'}}</p>
    </transition>
```

使用多个 v-if 的多个元素的过渡可用重写为绑定了动态属性的单个元素过渡，如：

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
  <style>
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    .fade-enter-active, .fade-leave-active {
      transition: opacity .7s;
    }
  </style>

  <div id="app">
    <button @click='change'>切换</button>
    <transition name="fade" mode="">
      <p :key='cur'>{{items[cur]}}</p>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          cur: 0,
          items: ['一鼓作气','再而衰','三而竭']
        }
      },
      methods: {
        change() {
          this.cur = (++this.cur) % 3
        }
      }
    })
  </script>
</body>
</html>
```

### 2.3过渡模式

不难发现，上述代码存在一些问题：当点击切换显示时，新元素的进入和旧元素的离开是同时发生的。

为解决这种问题，可用加入过渡模式：

- in-out：新元素先进入，完成之后旧元素离开
- out-in：旧元素先离开，完成之后新元素进入

修改代码：

```html
    <transition name="fade" mode="out-in">
      <p :key='cur'>{{items[cur]}}</p>
    </transition>
```

可以发现，问题已经解决了。

## 多组件过渡

多个组件的过渡不需要使用 key 属性，只需要使用动态组件

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
  <style>
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    .fade-enter-active, .fade-leave-active {
      transition: opacity .5s;
    }
  </style>

  <div id="app">
    <button @click='cur=(++cur)%3'>切换组件</button>
    <transition name="fade" mode="out-in">
      <component :is='cName[cur]'></component>
    </transition>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          cName: ["cpnA","cpnB","cpnC"],
          cur: 0
        }
      },
      components: {
        cpnA: {
          template: `<div><h2>组件A</h2></div>`
        },
        cpnB: {
          template: `<div><h2>组件B</h2></div>`
        },
        cpnC: {
          template: `<div><h2>组件C</h2></div>`
        }
      }
    })
  </script>
</body>
</html>
```

## 列表过渡

实现列表过渡需要使用 v-for 和 <transition-group> 组件，该组件特点如下：

- 与 <transition> 不同， 它会渲染出一个真实的DOM，默认为一个 <span>，可以通过修改 tag 属性更换成其他标签
- 过渡模式不可用，因为不再相互切换特有的元素
- 列表中的元素需要提供唯一的 key 值

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
  <style>
    .nums-enter-active, .nums-leave-active {
      transition: all 1s;
    }
    .nums-enter, .nums-leave-to {
      opacity: 0;
      transform: translateY(30px);
    }
    .list-item {
      display: inline-block;
      width: 20px;
      background-color: green;
    }
  </style>

  <div id="app">
    <button @click='add'>插入一个数字</button>
    <button @click='remove'>移除一个数字</button>
    <transition-group tag="p" name="nums">
      <div v-for='item in items' :key='item' class="list-item">
        {{item}}
      </div>
    </transition-group>
  </div>

  <script src="../vue.js"></script>

  <script>
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          items: [1,2,3,4,5,6,7,8,9,10],
          nextNum: 11
        }
      },
      methods: {
        add() {
          this.items.splice(this.randomNum(), 0, this.nextNum++)
        },
        remove() {
          this.items.splice(this.randomNum(), 1)
        },
        randomNum() { //随机生成一个索引
          return Math.floor(Math.random() * this.items.length)
        }
      }
    })
  </script>
</body>
</html>
```

