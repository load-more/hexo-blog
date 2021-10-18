---
title: 21 Vue 自定义指令
date: 2021-03-04 16:32:14
tags: [Vuejs, directive]
categories: Vuejs
---

## 注册指令

### 1.1注册全局指令

通过 `Vue.directive(id, definition)` 可以注册一个全局自定义指令

该方法有两个属性：

- 指令ID
  - 是指令的唯一标识
- 定义对象
  - 定义对象是定义的指令的钩子函数

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
    <input type="text" placeholder="111">
    <input type="text" placeholder="222">
    <!-- 该指令实现当页面加载时，使元素自动获得焦点 -->
    <input type="text" placeholder="333" v-focus>
    <input type="text" placeholder="444">
  </div>
  <script src="../vue.js"></script>
  <script>
    // 注册全局指令
    Vue.directive('focus', {	// id不包括 v- 前缀
      // 当被绑定的元素插入DOM中执行
      inserted(el) {
        // 使元素获得焦点
        el.focus()
      }
    })
    let vm = new Vue({
      el: '#app'
    })
  </script>
</body>
</html>
```

### 1.2注册局部指令

局部自定义指令只能 在当前实例使用，无法在其它实例中调用

```javascript
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
    <!-- 小驼峰命名 -->
    <span v-add-border='myborder'>这是文字</span>
  </div>
  <script src="../vue.js"></script>
  <script>
    let vm = new Vue({
      el: '#app',
      data() {
        return{
          myborder: '1px solid blue'
        }
      },
      directives: {
        // 给元素添加边框
        addBorder: {
          inserted(el, binding) {
            el.style.border = binding.value
          }
        }
      }
    })
  </script>
</body>
</html>
```

## 钩子函数

### 2.1详解

在注册指令时，可以传入 definition 定义对象，对指令赋予一些特殊功能。

一个指令定义对象可以提供的钩子函数有：

- bind
  - 只调用一次，在指令第一次绑定到元素上时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化设置
- inserted
  - 被绑定元素插入父元素时调用
- update
  - 指令在bind之后立即以初始值为参数进行第一次调用，之后每次当绑定值发生变化时调用，接收的参数为新值和旧值
- componentUpdated
  - 指令所在组件及其子组件更新时调用
- unbind
  - 只调用一次，指令从元素上解绑时调用

这些钩子函数是可选的。每个钩子函数都可以传入 el、binding、vnode 三个参数。update 和 componentUpdated 还可以传入 oldVnode 参数。

- el
  - 指令绑定的元素，可以用来直接操作DOM
- binding
  - 一个对象，有如下属性：
    - name
      - 指令名，不包含 v-前缀
    - value
      - 指令的绑定值，如v-my-directive='1+1'，value=2
    - oldValue
      - 指令绑定的前一个值，仅在update和componentUpdated可用，无论值是否改变都可用
    - expression
      - 绑定值的表达式或变量名，如v-my-directive='1+1'，expression='1+1'
    - arg
      - 传给指令的参数，如v-my-directive: foo，arg='foo'
    - modifiers
      - 一个包含修饰符的对象，如v-my-directive.foo.bar，modifiers={foo:true, bar: true}
  - vnode
    - Vue 编译生成的虚拟节点
  - oldVnode
    - 上一个虚拟节点，仅在update和componentUpdated中可用

> 除了el参数外，其它参数都应该是只读的，切勿修改！

### 2.2示例

```javascript
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
    <div v-demo:hello.a.b='message'>自定义指令测试</div>
  </div>
  <script src="../vue.js"></script>
  <script>
    Vue.directive('demo', {
      bind(el, binding, vnode) {
        console.log(el);
        el.innerHTML = 
          'name: ' + binding.name + '<br>' + 
          'value: ' + binding.value + '<br>' + 
          'expression: ' + binding.expression + '<br>' + 
          'arg: ' + binding.arg + '<br>' + 
          'modifiers: ' + JSON.stringify(binding.modifiers) + '<br>' + 
          'vnode: ' + Object.keys(vnode) + '<br>'  +
          'oldValue: ' + binding.oldValue + '<br>'  // 仅在update和componentUpdated使用
      }
    })
    let vm = new Vue({
      el: '#app',
      data() {
        return {
          message: 'HELLO WORLD'
        }
      }
    })

  </script>
</body>
</html>
```

运行结果：

```
name: demo
value: HELLO WORLD
expression: message
arg: hello
modifiers: {"a":true,"b":true}
vnode: tag,data,children,text,elm,ns,context,fnContext,fnOptions,fnScopeId,key,componentOptions,componentInstance,parent,raw,isStatic,isRootInsert,isComment,isCloned,isOnce,asyncFactory,asyncMeta,isAsyncPlaceholder
oldValue: undefined
```

### 2.3案例

输入数字，给图片设置边框宽度

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
    <img src="./test.jpg" width="300px" v-set-border='border'>
    <br>
    <label for="input_border">设置边框厚度</label>
    <input type="number" id="input_border" v-model='border'>
  </div>
  <script src="../vue.js"></script>
  <script>
    let vm = new Vue({
      el: '#app',
      data() {
        return {
          border: 0
        }
      },
      directives: {
        setBorder: {
          update(el, binding) {
            el.border = binding.value
          }
        }
      }
    })
  </script>
</body>
</html>
```

## 自定义指令的绑定值

### 3.1绑定data的属性

### 3.2绑定数值常量

### 3.3绑定字符串常量

### 3.4绑定对象字面量

```javascript
<div id="app">
    <input v-my-directive='border'>	// data属性
    <input v-my-directive='12'>		// 数值常量
    <input v-my-directive="'12px'">		// 字符串常量
    <input v-my-directive='{size: 30, color: "red"}'> // 对象字面量
  <script src="../vue.js"></script>
  <script>
    let vm = new Vue({
      el: '#app',
      data() {
        return {
          border: 0
        }
      },
      directives: {
        myDirective: {
          update(el, binding) {
            el.border = binding.value
          }
        }
      }
    })
  </script>
```