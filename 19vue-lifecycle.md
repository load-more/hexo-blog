---
title: 19 Vue的生命周期
date: 2021-03-03 09:18:40
tags: [Vuejs, hooks]
categories: Vuejs
---

## 一、Vue的钩子函数

- beforeCreate
- created
- beforeMount
- mounted
- beforeUpdate
- updated
- beforeDestroy
- destroyed

<!-- more -->

## 二、钩子函数详解

### 2.1 beforeCreate

**作用**：

- 在实例初始化之后，数据观测(data observer) 和 event/watcher 事件配置之前被调用

- el、data、methods 未初始化，无法调用属性和方法

**测试**：

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
  <div id="app">
    <h1>{{message}}</h1>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 实例化Vue，ViewModel
    let vm = new Vue({
      el: '#app',
      data() {
        return {
          message: 'Hello World'
        }
      },
      methods: {
        test() {
          console.log('这是test方法');
        }
      },
      // 检测数据变化，一旦发生变化，调用函数
      watch: {
        // message函数名必须和data中的数据名相同
        message() {
          console.log('watch：message变了！');
        }
      },
      beforeCreate() {
        console.log('-----beforeCreate-------');
        console.log('el: ' + this.$el);   // 调用Vue实例的属性或方法需要加上$
        console.log('data: ' + this.$data);
        console.log('message: ' + this.message);  // 调用Vue实例里data或methods里的属性或方法不加$
        this.test()
      }
    })
  </script>
</body>
</html>
```

**运行结果**：

![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_13-39-17.png)

可以看到Vue中的data和方法都是取不到的，并且是在watch之前执行

### 2.2 created

**作用**：

- 实例已经创建完成之后被调用
- 完成以下的配置：数据观测(data observer)、属性和方法的运算、watch/event 事件回调
- 挂载阶段还没开始，$el 属性目前不可见
- 主要应用：调用数据、调用方法、调用异步函数

**测试**：

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
  <div id="app">
    <h1>{{message}}</h1>
    <div id="icon">
      This is icon
    </div>
    <li v-for='(item, index) of list' :key='index'>
      {{item}}
    </li>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 实例化Vue，ViewModel
    let vm = new Vue({
      el: '#app',
      data() {
        return {
          message: 'Hello World',
          list: ['aaa','bbb','ccc']
        }
      },
      methods: {
        test() {
          console.log('这是test方法');
        }
      },
      // 检测数据变化，一旦发生变化，调用函数
      watch: {
        // message函数名必须和data中的数据名相同
        message() {
          console.log('watch：message变了！');
        }
      },
      created() {
        console.log('-------created-------');
        // 调用Vue实例的data
        console.log('data: ' + this.$data);
        console.log('message: ' + this.message);  
        // 调用Vue实例的方法
        this.test()
        // 未挂载，无法获取v-for渲染的标签
        console.log('li: ' + document.getElementsByTagName('li').length);
        // 直接加载的DOM可以获取到
        console.log(document.getElementById('app'))
        // 能够监听data
        this.message = 'LLLL'
      }
    })
  </script>
</body>
</html>
```

**运行结果**：

![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_14-02-24.png)

可以看到：created钩子可以获取Vue的data、调用Vue方法、获取原本HTML上的直接加载出来的DOM，但是无法获取到通过挂载模板生成的DOM（例如：v-for循环遍历Vue.list生成li）

**注意**：

- 如果要在created阶段中进行dom操作，就要将操作都放在 Vue.nextTick() 的回调函数中，因为created() 钩子函数执行的时候 DOM 其实并未进行任何渲染，而此时进行 DOM 操作无异于徒劳，所以此处一定要将 DOM 操作的 js 代码放进 Vue.nextTick() 的回调函数中。
- Vue.nextTick( [callback, context] )：在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

### 2.3 beforeMount

**作用**：

在created阶段到beforeMount阶段之间，主要完成了两个判断：

1. 判断 el 选项

   - **在 created 之后，会先检测对象中是否有 el 选项，如果没有，则停止编译，即停止了生命周期，当该 vue 实例中调用了 `vm.$mount(el)`，继续编译。**

   - 当我们注释掉 `el: '#app'`，可以发现结果

     ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_14-40-16.png)

   - 当我们在console中输入 `vm.$mount('#app')`时，结果：

     ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_14-41-39.png)

2. 判断 template 选项

   - 如果实例对象中有 template ，则将其作为模板编译成 render 函数
   - 如果实例对象中没有 template ，则将外部 HTML 作为模板编译

   修改代码：

   ```html
   <div id="app">
       <h2>{{'这是外部HTML中的：' + message}}</h2>
     </div>
     <script src="../vue.js"></script>
     <script>
       let vm = new Vue({
         el: '#app',    
         data() {
           return {
             message: 'Vue的生命周期'
           }
         },
         template: `
         <h1>{{'这是template中的：' + message}}</h1>
         `,
       })
   ```

   运行结果：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_10-03-47.png)

   注释掉 template，运行结果：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_10-04-47.png)

   **可以看出，template 选项的优先级要高于外部HTML。因此，要先判断 el 选项，然后判断 template 选项，因为只有拿到 el，才能通过 el 去找到外部HTML**

   在vue对象中还有一个**render函数**，它是以createElement作为参数，然后做渲染操作

   我们加入以下代码：

   ```html
   new Vue({
       el: '#app',
       render(createElement) {
           return createElement('h1', 'this is createElement')
       }
   })
   ```

   渲染结果为：

   ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_10-15-02.png)

   **所以，render函数 > template选项 > 外部HTML**

**作用**：

- 载入前（完成了data和el数据初始化）

- 但是页面中的内容还是vue中的占位符，data中的message信息没有被挂载到DOM节点中

  ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_14-34-45.png)

- 在这里可以在渲染前最后一次更改数据，不会触发其他的钩子函数，一般可以在这里做初始数据的获取

### 2.4 mounted

**作用**：

- 给vue实例对象添加**$el成员**，并且替换挂载的DOM元素，将data中的数据渲染到占位符中去

- 替换挂载的DOM有以下三种情况：

  1. 没有 template，则用外部HTML替换

     ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_15-02-22.png)

  2. 有 template，但没 render，则用 template 替换

     ![](https://gitee.com/gainmore/imglib/raw/master/img/Snipaste_2021-03-03_15-06-55.png)

  3. 有 template 且有 render，则用 render 替换

     ![Snipaste_2021-03-03_15-09-06](E:\Web Front End\HexoBlog\source\_posts\19vue-lifecycle\Snipaste_2021-03-03_15-09-06.png)

### 2.5 beforeUpdate

**作用**：

- 视图层的数据改变时触发（只有渲染到页面的数据发生改变时，才会触发beforeUpdate）

  例如：

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
    <div id="app">
      <h1>{{message}}</h1>
    </div>
    <script src="../vue.js"></script>
    <script>
      // 实例化Vue，ViewModel
      let vm = new Vue({
        el: '#app',
        data() {
          return {
            message: 'Hello World',
            list: ['aaa','bbb','ccc'],
            counter: 0
          }
        },
        methods: {
          test() {
            console.log('这是test方法');
          }
        },
        // 检测数据变化，一旦发生变化，调用函数
        watch: {
          // message函数名必须和data中的数据名相同
          message() {
            console.log('watch：message变了！');
          }
        },
        mounted() {
          this.counter++
        },
        beforeUpdate() {
          console.log('---beforeUpdate---');
        }
      })
    </script>
  </body>
  </html>
  ```

  由于，counter没有放在视图层中，所以即使在mounted中改变了它的值，也不会触发beforeUpdate；如果此时改变的是message，那么就会触发beforeUpdate，因为message在<h1>中引用了，出现在了视图层上。

- 如果视图层数据被改变后，会触发**beforeUpdate**，那么在**beforeUpdate**里再一次改变数据，此时会再一次触发**beforeUpdate**吗？

  经测试，不会。因为mounted中改变了数据，触发了beforeUpdate，再一次改变数据，由于mounted里修改的数据还没有更新到视图层中，所以beforeUpdate修改的是没有更新到视图层的数据，所以不会再次触发beforeUpdate

### 2.6 updated

**作用**：

- 视图层的数据改变并且DOM更新完成时触发（只有渲染到页面的数据发生改变时，才会触发beforeUpdate）
- 与beforeUpdate不同的是：
  - 只要视图层中的数据一发生改变，就会马上触发beforeUpdate，此时视图层中的数据还没有更新完成（速度快）
  - 而updated是，当视图层数据发生改变，而且改变后的数据已经渲染到视图层时，才会触发updated（有延迟）
- 所以针对上面那个问题，如果视图层数据被改变后，会触发**beforeUpdate**，在**beforeUpdate**里再一次改变数据，就不会再一次触发**beforeUpdate**了，因为beforeUpdate响应速度非常快，视图层还没来得及更新。
- 那么如果视图层数据被改变后，在**updated**里再一次改变数据，结果就不同了，因为视图层更新之后才会触发updated，所以在updated里再一次改变数据，就会直接触发beforeUpdate，然后更新完成，updated再一次改变数据，再一次触发beforeUpdate......陷入死循环

### 2.7 beforeDestroy

- 销毁前执行（$destroy方法被调用的时候就会执行）
- 在这一步，实例仍然完全可用
- 一般在这里善后:清除计时器、清除非指令绑定的事件等等…

### 2.8 destroyed

- 销毁后 （Dom元素仍然存在，只是不再受vue控制）
- 卸载watcher、事件监听、子组件

## 三、总结

- beforeCreate
  - 初始化一个空的 Vue 实例对象，还没有对 data 、methods 进行初始化
  - 可以在这里加个loading事件
- created
  - 初始化 data、methods、computed 以及一些事件的监听
  - 在这里结束loading，还可以做一些初始数据的获取，实现函数自执行
- beforeMount
  - 对 Vue 进行模板编译，将编译好的模板放入到内存中，此时页面还没有更新
- mounted
  - 给 Vue 的实例创建 $el 属性，将内存中编译好的模板赋值给它，然后用它替换掉原来的 el，此时更新的 DOM 就挂载到了页面上
  - 在这发起后端请求，拿回数据，配合路由钩子做一些事情
- beforeUpdate
  - 当视图层的数据发生更新就会触发这个钩子函数，此时数据是最新的，但页面还没有更新，所以数据和页面是不同步的
- updated
  - 此时页面也发生了更新，数据和页面同步
- beforeDestroy
  - 当实例对象的 $destroy 方法被调用后就会触发这个钩子函数，此时 data、methods 以及其他一些数据仍然可以调用
  - 你确认删除XX吗？
- destroyed
  - 实例对象卸载 Watcher、事件监听以及子组件，data、methods 等属性不可用
  - 当前组件已被删除，清空相关内容

**常用属性**

- el属性
  - 用来指示vue编译器从什么地方开始解析 vue的语法，可以说是一个占位符。
- data属性
  - 用来组织从view中抽象出来的属性，可以说将视图的数据抽象出来存放在data中。
- template属性
  - 用来设置模板，会替换页面元素，包括占位符。
- methods属性
  - 放置页面中的业务逻辑，js方法一般都放置在methods中
- render属性
  - 创建真正的Virtual Dom
- computed属性
  - 用来计算属性
- watch属性
  - watch:function(new,old){}，监听data中数据的变化，两个参数，一个返回新值，一个返回旧值

## 四、过程图

![](https://gitee.com/gainmore/imglib/raw/master/img/20210424083249.png)

