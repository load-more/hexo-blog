---
title: 14 JS 中的解构
date: 2021-02-24 21:35:07
tags: [JavaScript, 解构]
categories: JavaScript
---

## JS 中的解构

### 数组解构

- 变量个数 = 数组元素个数

  ```javascript
  var arr = ['tom', 'jeery', 'jacky', 'doinb', 'clearlove8'];
  let [a, b, c, d, e] = arr;
  console.log(a, b, c, d, e);
  //输出结果：tom jeery jacky doinb clearlove8
  ```

- 变量个数 < 数组元素个数

  ```javascript
  var arr = ['tom', 'jeery', 'jacky', 'doinb', 'clearlove8'];
  let [a, b] = arr;
  console.log(a, b);
  // 输出结果：tom jeery
  ```

- 变量个数 > 数组元素个数

  ```javascript
  var arr = ['tom', 'jeery', 'jacky', 'doinb', 'clearlove8'];
  let [a, b, c, d, e, f, g, h, i] = arr;
  console.log(a, b, c, d, e, f, g, h, i);
  // 输出结果：tom jeery jacky doinb clearlove8 undefined undefined undefined undefined
  ```

- 使用丢弃变量进行解构

  ```javascript
  var arr = ['tom', 'jeery', 'jacky', 'doinb', 'clearlove8'];
  let [a, , , d] = arr;
  console.log(a, d);
  // 输出结果：tom doinb
  ```

- 使用可变变量进行结构

  ```javascript
  var arr = ['tom', 'jeery', 'jacky', 'doinb', 'clearlove8'];
  let [a, b, ...c] = arr;
  console.log(a, b);
  console.log(c);
  输出结果：tom jeery
  		[ 'jacky', 'doinb', 'clearlove8' ]
  ```

- 使用缺省值进行解构

  ```javascript
  var arr = ['tom', 'jeery', 'jacky', 'doinb', 'clearlove8'];
  let [a, b, c=100, d, e=200, f=300] = arr; // 数组元素值会覆盖默认值
  console.log(a, b, c, d, e, f);
  // 输出结果：tom jeery jacky doinb clearlove8 300
  ```

- 将数组解构成单个元素

  ```javascript
  let arr = ['aaa', 'bbb', 1, 22]
  let a = [...arr]
  console.log(a)
  // ['aaa', 'bbb', 1, 2]
  ```

### 对象解构

- 简单对象解构

  ```javascript
  const obj = {
  		a:100,
  		b:200,
  		c:300
  		}
  
  var {x, y, z} = obj;
  console.log(x, y, z);
  输出结果：undefined undefined undefined
  
  var {a, b, c} = obj;
  console.log(a, b, c);
  输出结果：100 200 300
  
  // 对象的解构所用的变量要和对象的属性名一样才能拿到相应的值
  ```

- 对象解构同时重命名

  ```javascript
  const obj = {
  		a:100,
  		b:200,
  		c:300
  		}
  
  var {a:x, b:y, c:z} = obj;
  console.log(x, y, z);
  输出结果：100 200 300
  
  console.log(a, b, c);  // 报错  a is not defined
  
  /*
  从这个例子可以看出，对象解构虽然要使用相应的名称才可以拿到值，但是我们可以为其进行重命名，从例子上分析出，js会使用a, b, c到对象上取值，取到值之后赋值给x, y, z，这样通过x, y, z进行使用相应的值，a, b, c则未定义。
  */
  ```

- 复杂解构

  ```javascript
  var data = {
  		a:100,
  		b:[
  		{
  			c:200,
  			d:[],
  			a:300
  		},
  		{
  			c:1200,
  			d:[1],
  			a:300
  		},
  	],
  	c:500
  }
  
  var {a:m, b:[{a:n}, {a:n1}]} = data;
  console.log(m, n, n1);
  // 100 300 300
  ```

  

---

参考教程：https://blog.csdn.net/qq_38727847/article/details/103401321