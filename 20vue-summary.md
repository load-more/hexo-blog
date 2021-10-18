---
title: 20 Vue 知识总结
date: 2021-03-03 18:51:45
tags: [Vuejs, $set, filter, watch]
categories: Vuejs
---

## 细节

### 改变数组

由于 JavaScript 的限制，Vue 不能检测到下面两种情况引起的数组变化：

- 直接使用数组索引设置元素，如：`vm.items[1] = 'hhh'`
- 修改数组长度，如：`vm.items.length = 2`

为了解决第一种情况，可以使用全局方法 `Vue.set(array, index, value)` 或者 `vm.$set(array, index, value)` 来设置数组元素的值。**这种方法是响应式的，可以触发视图层更新。**

```javascript
Vue.set(vm.items, 2, 'newValue') 	// vm.$set(vm.items, 2, 'newValue')
```

> 实例方法 `vm.$set()` 是全局方法 `Vue.set()` 的别名

为了解决第二种情况，可以使用 `vm.items.splice()` 的方法修改数组长度

```javascript
vm.items.splice(2)	// 删除索引为2以后的所有元素，只保留前两个元素
```



### 改变对象

与数组不同的是，当使用 `vm.items['name'] = 'xxx'` 修改对象的属性时，可以触发视图层更新。

但如果使用 `vm.items[key] = value` 这种方法**添加响应式属性**，就不会触发视图更新

解决办法，仍然使用全局方法 `Vue.set(object, key, value)` 或者实例方法 `vm.$set(object, key, value)`

```javascript
Vue.set(vm.items, 'hobby', 'basketball')	// vm.$set(vm.items, 'hobby', 'basketball')
```

如果要添加多个响应式属性，可以使用 `Object.assign()`。在使用该方法时，需要将源对象的属性和新添加的属性合并为一个新的对象。

```javascript
vm.items = Object.assign({}, vm.items, {
    hobby: 'basketball',
    address: '11111'
})
```



### 过滤器

对于一些需要经过复杂计算的数据绑定，简单的表达式可能无法实现，这是可以通过使用过滤器进行处理。通过自定义的过滤器可以对文本进行格式化。

过滤器可以使用在**双大括号插值**和**v-bind指令**中，过滤器需要被添加到JavaScript表达式的尾部，由管道符号 | 表示，格式如下：

```html
{{ message | myfilter}}	
或者
<div v-bind:id='ID | formatID'></div>
```

定义过滤器方式主要有两种：

- 全局方法 `Vue.filter()`

- 应用选项对象中的 filters 选项定义

**1.全局方法定义**

```javascript
{{ data | ID }}
Vue.filter(ID, function(value){
    
})
let vm = new Vue({})
// 第一个参数为过滤器ID，作为过滤器的唯一标识
// 第二个参数为过滤器函数，value对应表达式的值，即 data
```

> 使用全局方法 `Vue.filter()` 定义的过滤器必须定义在创建的Vue实例之前。

**2.filters选项定义**

```javascript
let vm = new Vue({
    el: '#app',
    filters: {
        myfilter(value) {
            ...
        }
    }
})
```

**串联使用**

多个过滤器可以串联使用，如：

```javascript
{{ data | filterA | filterB }}
```

首先会调用 filterA 对应的函数，然后调用 filterB对应的函数。

filterA 的函数以 data 作为参数， filterB 的函数以 filterA 的结果作为参数

**传参**

过滤器实质也是一个函数，因此可以接收额外的参数，如：

```javascript
{{ data | filterA(arg1, arg2, ...) }}

 filterA(value, arg1, arg2, ...) {}
```



### 监听属性

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
  <div id="app"></div>
  <script src="../vue.js"></script>
  <script>
    let vm = new Vue({
      el: '#app',
      data() {
        return {
          price: 12,
          info: {
            name: 'Tom',
            gender: 'male',
            age: 12
          }
        }
      },
      watch: {

        info: {
          handler(newVal, oldVal) {
            // 当监听的数据是一个数组或对象时，回调函数的新值和旧值是相等的，因为这两个形参指向同一个数据对象
            console.log(newVal, oldVal)
            alert('you change "info"')
          },
          deep: true    // 监听对象内部值变化，需要将deep设为true
        },
    
        price() {       // 简写
          alert('you change "price"')
        },
        // price: {       // 完整写法
        //   handler() {
        //     alert('you change "price"')
        //   }
        // }
      }
    })
  </script>
</body>
</html>
```

