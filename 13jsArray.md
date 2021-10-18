---
title: 13 JS 数组方法
date: 2021-02-24 21:23:56
tags: [JavaScript, 数组方法]
categories: JavaScript
---

## JS 的数组方法

---

参考教程：https://www.cnblogs.com/xiaohuochai/p/5682621.html

### 查询所有方法

```javascript
console.log(Array.prototype)
```

### 1.对象继承方法

#### `toString()`

```javascript
[1,2,3].toString();//'1,2,3'
['a','b','c'].toString();//'a,b,c'
[1,[2,'c']].toString();//'1,2,c'

// 由于alert()要接收字符串参数，它会在后台调用toString()方法，会得到与toString()方法相同的结果
alert([1,2,3]);//'1,2,3'
```

#### `toLocaleString()`

```javascript
// toLocaleString()是toString()方法的本地化版本，经常返回与toString()方法相同的值
var person1 = {
    toLocaleString: function(){
        return 'Nikolaos';
    },
    toString: function(){
        return 'Nicholas';
    }
};
var person2 = {
    toLocaleString: function(){
        return 'Grigorios';
    },
    toString: function(){
        return 'Greg';
    }
};
var people = [person1,person2];
console.log(people.toString());//'Nicholas,Greg'
console.log(people.toLocaleString());//'Nikolaos,Grigorios'

// 如果数组中的某一项的值是null或者undefined，则该值在toLocaleString()和toString()方法返回空字符串
var colors = [1,undefined,2,null,3];
console.log(colors.toString());//'1,,2,,3'
console.log(colors.toLocaleString());//'1,,2,,3'
```

#### `valueOf()`

```javascript
// valueOf()方法返回数组对象本身
var a = [1, 2, 3];
console.log(a.valueOf());// [1, 2, 3]
console.log(a.valueOf() instanceof Array);//true
```



### 2.数组转换方法

### 3.栈和队列方法

### 4.数组排序方法

### 5.数组拼接方法

### 6.创建子数组方法

### 7.数组删改方法

### 8.数组位置方法

### 9.数组归并方法

### 10.数组迭代方法



### concat

### constructor

### copyWithin

### entries

### every

### fill

### filter

### find

### findIndex

### flat

### flatMap

### forEach

### includes

### indexOf

### join

### keys

### lastIndexOf

### map

### pop

### push

### reduce

### reduceRight

### reverse

### shift

### slice

### some

### sort

### splice

### toLacaleString

### toString

### unshift

### values





