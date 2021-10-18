---
title: 66 class 继承
date: 2021-04-26 20:18:27
tags: [class, ES6, extends]
categories: JavaScript
---

面试中被问到 class 继承，了解一下。

<!-- more -->

```javascript
class Person {
  constructor(name, gender, age) {
    this.name = name
    this.gender = gender
    this.age = age
  }
  sayName() {
    console.log('Name:' + this.name);
  }
}

class Student extends Person {
  constructor(name, gender, age, score) {
    super(name, gender, age)
    this.score = score
  }
  showScore() {
    console.log('Score:' + this.score);
  }
}

let stu = new Student('xiaoming', 'male', 18, 100)
console.log(stu);
stu.showScore()
stu.sayName()
```

总结：

- 相对于原型链继承，class 只要使用 extends 就能够继承父类的属性和方法，非常方便
- 注意，在子类的 constructor 中需要调用 super() ，传入参数，将父类的属性初始化一下