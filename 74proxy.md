---
title: 74 学习Proxy
date: 2021-07-21 09:25:25
tags: [Javascript, Proxy]
categories: Javascript
---

学习一个 ES6 的新特性 Proxy。

<!-- more -->

# Proxy

## 概念

Proxy 是一个构造函数，需要传入两个参数：target 和 handler。它可以构造出代理对象，这个代理对象可以代理目标对象 target 做一些事，当执行某个操作时，它会执行 handler 所对应的函数。

示例：

```js
// 目标对象
const obj = {
  age: 18
}
// 返回的是代理对象
const proxy = new Proxy(obj, {
  get(target, key, receiver) {
    if (key in target) {
      return target[key]
    } else {
      throw new ReferenceError(`Prop name ${key} does not exist.`)
    }
  }
})

// 访问代理对象的属性会被拦截而触发get
console.log(proxy.age); // 18
console.log(proxy.ave); // ReferenceError: Prop name ave does not exist.
// 访问目标对象不会被拦截
console.log(obj.age); // 18
console.log(obj.ave); // undefined
```



## 捕获器

共有以下 13 种方法。

### get

该方法用于拦截某个属性的读取操作。

参数：

- target：目标对象
- key：对象的键名
- receiver：代理对象

```js
// 目标对象
const obj = {
  age: 18
}
// 返回的是代理对象
const proxy = new Proxy(obj, {
  get(target, key, receiver) {
    if (key in target) {
      console.log(receiver === proxy); // true
      return target[key]
    } else {
      throw new ReferenceError(`Prop name ${key} does not exist.`)
    }
  }
})
// 访问代理对象的属性会被拦截而触发get
console.log(proxy.age); // 18
console.log(proxy.ave); // ReferenceError: Prop name ave does not exist.
// 访问目标对象不会被拦截
console.log(obj.age); // 18
console.log(obj.ave); // undefined
```



### set

该方法用于拦截设置对象属性操作。

参数：

- target：目标对象
- key：对象的键名
- value：设置的值
- receiver：代理对象

```js
const obj = {
  age: 18
}

const proxy = new Proxy(obj, {
  set(target, key, value, receiver) {
    console.log('set successfully!');
    target[key] = value // 不需要返回值
  }
})

proxy.age = 22
```



### has

用于拦截`propKey in proxy`的操作，返回一个布尔值，表示属性是否存在。

参数：

- target
- key

```js
const obj = {
  age: 18
}

const proxy = new Proxy(obj, {
  has(target, key) {
    return key in target
  }
})

console.log('age' in proxy); // true
console.log('ave' in proxy); // false
```



### deleteProperty

用于拦截delete操作，返回一个布尔值，表示是否删除成功。

参数：

- target
- key

```js
const obj = {
  age: 18
}

const proxy = new Proxy(obj, {
  deleteProperty(target, key) {
    return delete target[key]
  }
})

console.log(delete proxy.age); // true
```



### ownKeys

用于拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环等类似操作，返回一个数组，表示对象所拥有的keys。

参数：

- target

```js
const obj = {
  age: 18
}

const proxy = new Proxy(obj, {
  ownKeys(target) {
    return ['1', '2', '4'] // 返回数组
  }
})

console.log(Object.getOwnPropertyNames(proxy)); // ['1', '2', '4']
```



### getOwnPropertyDescriptor

用于拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。

参数：

- target
- key

```js
const obj = {
  age: 18
}

const proxy = new Proxy(obj, {
  getOwnPropertyDescriptor(target, key) {
    return Object.getOwnPropertyDescriptor(target, key)
  }
})

// { value: 18, writable: true, enumerable: true, configurable: true }
console.log(Object.getOwnPropertyDescriptor(proxy, 'age'));
```



### defineProperty

用于拦截`Object.defineProperty(proxy, propKey, propDesc）`和`Object.defineProperties(proxy, propDescs)`的操作。

参数：

- target
- key
- desc：属性描述配置

```js
const obj = {
  age: 18
}

const proxy = new Proxy(obj, {
  deleteProperty(target, key, desc) {
    return Object.defineProperty(target, key, desc)
  }
})

const rst = Object.defineProperty(proxy, 'age', { value: 11 })
console.log(rst); // { age: 11 }
```



### preventExtensions

用于拦截`Object.preventExtensions(proxy)`操作，`preventExtensions`的作用是将一个对象变成不可扩展，也就是永远不能再添加新的属性。

参数：

- target

```js
const obj = {  age: 18}const proxy = new Proxy(obj, {  preventExtensions(target) {    return Object.preventExtensions(target)  }})Object.preventExtensions(proxy)proxy.name = 'John'console.log(proxy); // { age: 18 }
```



### getPrototypeOf

用于拦截 `Object.getPrototypeOf(proxy)`操作。

参数：

- target

```js
const obj = {  age: 18}const proxy = new Proxy(obj, {  getPrototypeOf(target) {    return Object.getPrototypeOf(target)  }})console.log(Object.getPrototypeOf(obj)); // [Object: null prototype] {}
```



### isExtensible

用于拦截`Object.isExtensible(proxy)`操作，返回一个布尔值，表示是否可扩展。

参数：

- target

```js
const obj = {  age: 18}const proxy = new Proxy(obj, {  isExtensible(target) {    return Object.isExtensible(target)  }})console.log(Object.isExtensible(proxy)); // true
```



### setPrototypeOf

用于拦截 `Object.setPrototypeOf(proxy, proto)` 操作。

参数：

- target
- proto：原型对象

```js
const obj = {  age: 18}const proxy = new Proxy(obj, {  setPrototypeOf(target, proto) {    return Object.setPrototypeOf(target, proto)  }})Object.setPrototypeOf(proxy, { test: 111 })console.log(Object.getPrototypeOf(proxy)); // { test: 111 }
```



### apply

当`Proxy`实例作为函数调用时触发，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。

参数：

- target
- object：调用对象
- args：传入参数

```js
function fn(a, b, c) {  return a + b + c}const proxy = new Proxy(fn, {  apply(target, object, args) {    console.log('fn is called');    return fn.call(object, ...args)  }})console.log(proxy(1, 2, 3));
```



### construct

当` Proxy` 实例作为构造函数时触发该函数调用，比如`new proxy(...args)`。

参数：

- target
- args

```js
function Fn(name, age) {
  this.name = name
  this.age = age
}

const proxy = new Proxy(Fn, {
  construct(target, args) {
    console.log('construct is called');
    return new target(...args)
  }
})

console.log(new proxy('jack', 18)); // Fn { name: 'jack', age: 18 }
```



## 参考文章

[一篇彻底理解Proxy](https://juejin.cn/post/6975858843729264653)

