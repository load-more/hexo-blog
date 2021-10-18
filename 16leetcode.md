---
title: 16 leetcode 283 移动零
date: 2021-02-26 19:41:22
tags: LeetCode
categories: LeetCode
---

## 描述

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**示例:**

```javascript
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

<!-- more -->

**说明**:

1. 必须在原数组上操作，不能拷贝额外的数组。
2. 尽量减少操作次数。

## 解答

### 1.双指针

创建一个快指针R和一个慢指针L，L用于指向0，R则遍历所有元素，当指到0时跳过，指到非0时，R和L指向的元素交换，同时L自增，这就实现了0向后排。

```javascript
var moveZeroes = function(nums) {
    let R = 0, L = 0, numsLen = nums.length
    while (R < numsLen) {
        if (nums[R] === 0) {
            R++
        } else {
            let temp = nums[R]
            nums[R] = nums[L]
            nums[L] = temp
            R++
            L++
        }
    }
}
```

简化代码：

```javascript
var moveZeroes = function(nums) {
    let R = 0, L = 0, numsLen = nums.length
    while (R < numsLen) {
        if (nums[R] === 0) {
            R++
        } else {
		   [nums[R], nums[L]] = [nums[L], nums[R]]	// 利用数组结构交换元素
            R++
            L++
        }
    }
}
```

继续简化：

```javascript
var moveZeroes = function(nums, R=-1, L=0, numsLen=nums.length) {
    while (R++ < numsLen) {
        // 利用 && 实现条件分支
        nums[R] && ([nums[R], nums[L]] = [nums[L], nums[R]], L++)
    }
}
```

### 2.使用 `reduce` 方法

`reduce` 的使用

参考：https://www.cnblogs.com/xiaohuochai/p/5682621.html

```javascript
/*
　　reduce()方法需要两个参数：
　　	1.第一个是执行化简操作的函数。化简函数的任务就是用某种方法把两个值组合或化简为一个值，并返回化简后的值
　　化简函数接受四个参数，分别是：
      【1】初始变量，默认为数组的第一个元素值。函数第一次执行后的返回值作为函数第二次执行的初始变量，依次类推
      【2】当前变量，如果指定了第二个参数，则该变量为数组的第一个元素的值，否则，为第二个元素的值
      【3】当前变量对应的元素在数组中的索引(从0开始)
      【4】原数组对象
　　	化简函数的这四个参数之中，只有前两个是必须的，后两个则是可选的
　　	2.reduce()方法第二个(可选)的参数是一个传递给函数的初始值
*/

[1, 2, 3, 4, 5].reduce(function(prev, cur, index, array){
    console.log(prev, cur)
    return prev+ cur;
});
```

化简参数的详细介绍：

```javascript
/*
	1.当未设置初始值时
		prev 为数组第一个元素，current 为数组第二个元素，index 为current的索引，array为原数组
		依次向后移动 index，遍历数组中的所有元素
*/
let arr = [10,5,40,25,1000,1]
arr.reduce((p, c, i, a) => {
    console.log("prev=" + p, 'current=' + c, 'index=' + i, 'array=' + a);
    return p
})
/*
prev=10 current=5 index=1 array=10,5,40,25,1000,1
prev=10 current=40 index=2 array=10,5,40,25,1000,1
prev=10 current=25 index=3 array=10,5,40,25,1000,1
prev=10 current=1000 index=4 array=10,5,40,25,1000,1
prev=10 current=1 index=5 array=10,5,40,25,1000,1
*/

/*
	2.当设置初始值时
		prev 为数组初始值，current 为数组第一个元素，index 为current的索引，array为原数组
		依次向后移动 index，遍历数组中的所有元素
*/
let arr = [10,5,40,25,1000,1]
arr.reduce((p, c, i, a) => {
    console.log("prev=" + p, 'current=' + c, 'index=' + i, 'array=' + a);
    return p
}, 0)
/*
prev=0 current=10 index=0 array=10,5,40,25,1000,1
prev=0 current=5 index=1 array=10,5,40,25,1000,1
prev=0 current=40 index=2 array=10,5,40,25,1000,1
prev=0 current=25 index=3 array=10,5,40,25,1000,1
prev=0 current=1000 index=4 array=10,5,40,25,1000,1
prev=0 current=1 index=5 array=10,5,40,25,1000,1
*/

/*
	3.当未设置返回值时
		prev 为上一次的返回值，没有返回值时为 undefined
*/
let arr = [10,5,40,25,1000,1]
arr.reduce((p, c, i, a) => {
    console.log("prev=" + p, 'current=' + c, 'index=' + i, 'array=' + a);
}, 0)
/*
prev=0 current=10 index=0 array=10,5,40,25,1000,1
prev=undefined current=5 index=1 array=10,5,40,25,1000,1
prev=undefined current=40 index=2 array=10,5,40,25,1000,1
prev=undefined current=25 index=3 array=10,5,40,25,1000,1
prev=undefined current=1000 index=4 array=10,5,40,25,1000,1
prev=undefined current=1 index=5 array=10,5,40,25,1000,1
*/

// 4.实现数组求和
let arr = [10,5,40,25,1000,1]
let res = arr.reduce((p, c) => {
    return p + c
}, 0)	// 也可以省略 0
console.log(res)

// 5.reduce()方法的返回结果类型和传入的初始值相同
let res = [1, 2, 3, 4, 5].reduce(function(prev, cur){
    prev.sum = prev.sum + cur;
    return prev;
},{sum:0});
console.log(res)
// { sum: 15 }
```

使用 `reduce` 实际和双指针的原理一样，也是有两个指针，快指针为 i，指向遍历的每个元素，慢指针为 p，根据返回值确定它的值

```javascript
var moveZeroes = function(nums) {
    // 注意，++放在前面表示返回之前自增，如果放在后面，则直接返回不会再自增了
    nums.reduce((p, c, i, a) => {
        return c ? ([a[p], a[i]] = [a[i], a[p]], ++p) : p
    }, 0)
    // a就表示nums数组，reduce会修改原数组
}

```

### 3.使用 `sort` 方法

`sort` 原理

```javascript
// sort 不传参数时，默认升序排列，而且是按照第一个字符的编码顺序排列的，如果是数字则会转为字符串，如果第一个字符/	相同则会比较第二个，如此往复
let arr = [200,5,1,0,33,22]
arr.sort()
console.log(arr);
// [ 0, 1, 200, 22, 33, 5 ]

// sort 可以传递一个比较函数
let arr = [200,5,1,0,33,22]

// a、b 为数组中任意两个元素，且a在b的左边
arr.sort((a, b) => {
    if (a > b) {
        // 当返回值大于0时，a、b交换位置
        return 1
    } else if (a < b) {
        // 当返回值小于0时，a、b不交换位置
        return -1
    } else {
        // 当返回值等于0时，a、b位置不变
        return 0
    }
    // 所以，以上的效果是：a > b 时，a换到b的后面，a <= 0 时，a、b位置不变
    // 所以，升序排列
})

console.log(arr);
// [ 0, 1, 5, 22, 33, 200 ]

// 以上代码可简化为：
arr.sort((a, b) => {
	return a - b	// 升序排列
})
```

因此，对于这个问题，我们可以通过判断前面的数是否为0，如果为0，交换两个数，使0往后排

```javascript
var moveZeroes = function(nums) {
	nums.sort((a, b) => {
        if (a === 0) {
            if (b === 0) {
                return 0
            }
            return 1
        } else {
            if (b === 0) {
                return 0
            }
            return 0
        }
    })
}
```

化简：

```javascript

var moveZeroes = function(nums) {
     nums.sort((a, b) => {
         return a === 0 ? (b !== 0 ? 1 : 0) : (b === 0 ? -1 : 0)
         // return nums.sort((a,b) => b? 0: -1)
     })
}

```



