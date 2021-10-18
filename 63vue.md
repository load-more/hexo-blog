---
title: 63 Vuejs 响应式原理
date: 2021-04-19 21:40:34
tags: [Vuejs]
categories: Vuejs
---

深入理解 Vue 的响应式原理。

<!-- moer -->

我们知道，在 Vue 里，当我们修改某个元素里的属性后，会触发页面层的更新，那些在页面中引用了该属性的部分也会发生相应的改变。

那么，Vue 是如何实现这种效果的呢？

要实现这种效果，Vue 的内部主要做了两件事情：`数据劫持` 和 `发布者-订阅者模式`。

## 数据劫持

首先，我们来理解什么是数据劫持。通俗来讲，其实就是监听对象属性的变化。

例如，我们要操作一个数据，首先要知道如何对它操作，是改写这个数据的内容，还是读取这个数据的内容。

在 JavaScript 中，有一个方法是专门处理这类事情的 -- `Object.defineProperty`。

```javascript
let obj = {
  name: 'Tom'
}
let value = obj.name // 注意重新定义一个变量，如果直接使用obj.name的方式修改或调用，会引起死循环
Object.defineProperty(obj, 'name', {
  set(newVal) {
    value = newVal // 修改属性
    console.log('设置属性触发')
  },
  get() {
    console.log('获取属性触发')
    return value
  }
})
obj.name = 'Jack'
console.log(obj.name); // Jack
```

在 Vue2 中，就是通过 `Object.defineProperty` 这种方法劫持数据，给数据添加监听函数的。

### Object.defineProperty()

```js
var obj = {}
Object.defineProperty(obj, "name", {
  value : 'Tom',
  writable : false, // 控制属性是否可以被赋值，默认为 false
  enumerable : false, // 控制属性是否可以被遍历，默认为 false
  configurable : false // 控制属性是否可以被删除，默认为 false
})

console.log(obj) // {} （空对象，因为没有开启 enumerable）

// --------------------------------------------------------
var obj = {}
Object.defineProperty(obj, "name", {
  value : 'Tom',
  writable : true, // 控制属性是否可以被赋值，默认为 false
  enumerable : true, // 控制属性是否可以被遍历，默认为 false
  configurable : true // 控制属性是否可以被删除，默认为 false
})

// enumerable
console.log(obj) // { name: 'Tom' }
for (let key in obj) {
  console.log(key) // name
}

// writable
obj.name = 'Tommy'
console.log(obj.name) // Tommy

// configurable
console.log(delete obj.name) // true
console.log(obj) // {}
```



## 发布者-订阅者模式

当监听到一个数据的改变，接下来就要对这种改变作出响应了，即对每个引用到这个数据的地方进行更新。

首先，我们可以抽象出两个概念：`发布者` 和 `订阅者`。

在 Vue 中，一个数据（假设为 name 属性）可以在多个地方（假设为 A、B、C...）被引用，当使用 `Object.defineProtperty` 监听到 name 属性改变时，我们就需要通知 A、B、C 这些引用者，告诉它们 name 属性已经被修改了，让它们更新 name 属性。

那么如何记录这些引用者呢？这就要抽象出一个概念：`订阅者`。订阅者，表示 “订阅” 数据的对象，该对象包含更新引用数据的方法。

那么当数据改变时，如何告诉订阅者呢？这也要抽象出一个概念：`发布者`。发布者，用于将所有订阅者添加到一起，当数据改变时，通知这些订阅者更新数据。

```javascript
// 发布者
class Dep {
    constructor() {
        this.subs = []
    }
    addSub(sub) {
        this.subs.push(sub)
    }
    notify() {
        this.subs.forEach(sub => {
            sub.update()
        })
    }
}

// 订阅者
class Watcher {
    constructor() {
        this.update()
    }
    update() {
        
    }
}
```

## 流程图

![](https://gitee.com/gainmore/imglib/raw/master/img/20210419230843.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210419232544.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210712141206.png)

过程分析：

1. 首先新建一个 Vue 实例，里面传入了 el 和 data 两个属性

2. 解析 data 数据

   - 设置 Observer ，遍历 data 属性，通过 `Object.defineProperty()` 给每个属性添加 `setter`、`getter`
   - 同时创建一个 Dep 发布者对象 

3. 解析 el 模板数据

   - 在 el 模板中的引用触发了 Observer 中的 getter，将会创建一个 Watcher 订阅者对象，一个引用对应一个订阅者，同时会将订阅者添加到发布者中
   - 在 el 模板中的初次引用会渲染到页面上，完成对视图层的初始化

4. 如果数据变化

   如，通过 `vue.name = 'test'`，修改某个属性。

   - 上面这种再赋值的操作，触发了 Observer 中的 setter，将会调用 Dep 中的 notify 方法，通知每一个订阅者完成数据更新（即调用 Watcher 的 update 方法）

## 代码实现

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
    <div>
      <input type="text" v-model="message">
      message: {{ message }}
    </div>
    <div>
      <input type="text" v-model="info.name">
      info.name: {{ info.name }}
    </div>
    <div>
      <input type="text" v-model="info.gender">
      info.gender: {{ info.gender }}
    </div>
    <div>
      <input type="text" v-model="info.age">
      info.age: {{ info.age }}
    </div>
  </div>
</body>
<script src="./response.js"></script>
<script>
class Vue {
  constructor(options) {
    // 1.保存数据
    this.$options = options
    this.$data = options.data

    // 2.将 data 添加到响应式中
    new Observer(this.$data)

    // 3.代理 this.$data 的数据（可以用 vm.xxx 的形式调用 data 里的属性）
    Object.keys(this.$data).forEach(key => {
      this._proxy(key)
    })

    // 4.处理 el
    new Compile(options.el, this)
  }

  _proxy(key) {
    Object.defineProperty(this, key, {
      configurable: true, // 可以被删除
      enumerable: true, // 属性可以被遍历
      get() {
        return this.$data[key]
      },
      set(newVal) {
        this.$data[key] = newVal
      }
    })
  }
}

class Observer {
  constructor(data) {
    this.data = data

    this.observe(this.data)
  }

  observe(data) {
    if (!data || typeof data !== 'object') {
      return
    }
    Object.keys(data).forEach(key => {
      this.defineReactive(data, key, data[key])
    })
  }

  defineReactive(data, key, val) {
    this.observe(val) // 递归调用，给所有是对象的子元素添加响应式

    const dep = new Dep() // 为每个属性创建一个订阅器
    Object.defineProperty(data, key, {
      enumerable: true, // 属性可以被遍历
      configurable: true, // 属性可以被删除
      get() {
        // 如果缓存有值则将缓存的 Watcher 加入 dep，
        // 因为 Watcher 的 update 会触发 getter，利用缓存可以避免重复添加 Watcher 到 dep
        if (Dep.target) {
          dep.addSub(Dep.target)
        }
        return val
      },
      set(newVal) {
        if (val === newVal) { // 如果新值等于旧值，不添加响应式
          return
        }
        val = newVal
        dep.notify() // 属性改变，通知订阅器更新
      }
    })
  }
}

// 订阅器（收集订阅者 + 通知订阅者更新）
class Dep {
  constructor() {
    this.subs = [] // 依赖的 Watcher 列表
  }
  addSub(watcher) {
    this.subs.push(watcher)
  }
  notify() {
    this.subs.forEach(watcher => {
      watcher.update() // 更新每个 Watcher
    })
  }
}

class Watcher {
  // vm: Vue 实例，里面保存了所有 data
  // key: 属性名，通过 key 可以找到对应的属性
  // cb: 当前 watcher 如何更新自己的文本内容的回调函数
  constructor(vm, key, cb) {
    this.vm = vm
    this.key = key
    this.cb = cb

    // 初始化
    Dep.target = this // 1.把当前 watcher 缓存起来
    this.update() // 2.更新触发 getter，因为缓存中有值，所以会把 watcher 加入 dep
    Dep.target = null // 3.清空缓存
  }

  update() { // 更新（直接调用 watcher 的 update 不会将 watcher 加入 dep）
    const value = this.key.split('.').reduce((v, k) => v[k], this.vm) // 会触发 getter
    this.cb(value)
  }
}


// 对 html 文档进行模板编译
class Compile {
  constructor(el, vm) {
    vm.$el = document.querySelector(el)
    this.vm = vm

    // 创建文档片段（放在内存中），把所有 DOM 操作放到文档片段中，
    // 操作完成之后重新加入到文档流中，减少回流和重绘，提高 DOM 操作的性能。
    const fragment = document.createDocumentFragment()

    // 把所有子节点都加入到文档片段中（即将原文档放到内存中）
    let childNode
    while (childNode = vm.$el.firstChild) {
      fragment.appendChild(childNode)
    }

    // 进行模板编译
    this.replace(fragment)

    // 将文档片段加到文档中（即将文档重新放到原文档中）
    vm.$el.appendChild(fragment)
  }
  
  // 
  replace(node) {
    // 匹配插值表达式，\s 表示空格，\S 表示非空格
    const reg = /\{\{\s*(\S+)\s*\}\}/

    // 如果 node 是文本，则进行文本替换，并且停止递归
    if (node.nodeType === 3) {
      // 获取文本内容
      const text = node.textContent
      // 正则匹配和提取
      const rst = reg.exec(text)
      // 如果匹配成功
      if (rst) {
        // 如果有 `info.name` 这类属性，则需要拆分，然后使用 reduce 转换为 vm[info][name] 调用
        const value = rst[1].split('.').reduce((v, key) => v[key], this.vm)
        // 将文本中的插值替换成值（初始渲染）
        node.textContent = text.replace(reg, value)
        // 创建 watcher 实例
        new Watcher(this.vm, rst[1], newVal => {
          // 闭包，引用外层函数的变量，使变量持续保存在内存中
          node.textContent = text.replace(reg, newVal)
        })
      }
      return
    }

    // v-model 双向数据绑定
    // 如果当前节点是 input 输入框
    if (node.nodeType === 1 && node.tagName.toUpperCase() === 'INPUT') {
      // 1.data 数据变化使输入框数据更新
      const attrs = Array.from(node.attributes) // 判断输入框是否有 v-model 属性
      const rst = attrs.find(item => item.name === 'v-model')
      if (rst) {
        // v-model 的值
        const key = rst.value
        const value = key.split('.').reduce((v, k) => v[k], this.vm)
        node.value = value // 初始化数据

        // 创建 watcher 实例
        new Watcher(this.vm, key, newVal => {
          node.value = newVal // 更新数据
        })
        
        // 2.输入框数据变化使 data 数据更新
        node.addEventListener('input', event => {
          const keyArr = key.split('.') // 属性名数组
          let len = keyArr.length
          const value = event.target.value // input 的内容
          const obj = keyArr.slice(0, len - 1).reduce((v, k) => v[k], this.vm)
          obj[keyArr[len - 1]] = value // 给 data 属性赋值，触发 setter
        })
      }

    }

    // 如果不是文本节点，可能是一个 DOM 元素，则需要进行递归
    node.childNodes.forEach(childNode => this.replace(childNode))
  }
}  
    
    
  const app = new Vue({
    el: '#app',
    data: {
      message: 'Hello',
      info: {
        name: 'Tom',
        gender: 'male',
        age: 18
      }
    }
  })
</script>
</html>
```

