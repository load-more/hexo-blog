---
title: 12 node.js 学习之路（六）
date: 2021-02-24 18:14:57
tags: [Nodejs, MongoDB, Mongoose]
categories: Nodejs
---

## MongoDB

[入门教程](https://www.runoob.com/mongodb/mongodb-databases-documents-collections.html)

---

### 开启与关闭 MongoDB

- 开启

  ```shell
  # mongodb 默认使用执行 mongod 命令所处磁盘根目录下的 /data/db 作为自己的数据存储目录
  # 所以在第一次执行命令之前先自己手动新建一个 /data/db
  
  $ mongod
  
  # 如果想要修改默认的数据存储目录，可以：
  $ mongod --dbpath=数据存储目录路径
  ```

- 关闭

  ```shell
  # Ctrl + C 或者 直接关闭命令窗口
  ```

- 创建批处理文件

  由于每次都要指定数据库的 --dbpath，比较麻烦，所以可以创建一个批处理文件(.bat)，每次启动时只要打开这个文件就可以了。

  首先，新建一个文本文档文件，以 .bat 为后缀名。

  在文件内写入以下代码：

  ```
  echo "MongoDB starting..."
  mongod --dbpath=E:/db/MongoDB
  pause
  ```

### 连接数据库和断开连接

- 连接

  ```shell
  # 在执行 mongod 之后，开启另一个窗口
  $ mongo
  # 该命令默认连接本机的 MongoDB 服务
  # 也可以直接使用可视化工具进行连接
  ```

- 断开连接

  ```shell
  exit
  ```

  


### 基本命令

- `show dbs` 显示所有数据的列表
- `db`     显示当前数据库对象或集合
- `use`   连接到一个指定的数据库
- `show collections`  显示所有集合
- `db.xxx.find()`  显示xxx集合所有数据

### 在 node 中操作 MongoDB

- 使用官方的 mongodb 包

  http://github.com/mongodb/node-mongodb-native

  操作繁琐，不推荐

- 使用第三方包 `mongoose` 

  `mongoose` 基于上面的官方包再一次做了封装，操作更简单

  http://mongoosejs.com/

### mongoose

#### 起步

- 官网：http://mongoosejs.com/
- 官方指南：http://mongoosejs.com/docs/guide.html
- 官方 API 文档：http://mongoosejs.com/docs/api.html

#### 安装

```shell
npm install mongoose
```

#### demo

```javascript
// 1.引包
const mongoose = require('mongoose');
// 2.连接数据库，数据库名不存在时自动创建
mongoose.connect('mongodb://localhost/test');
// 3.设计模型
const Cat = mongoose.model('Cat', { name: String });
// 4.实例化模型
const kitty = new Cat({ name: 'Zildjian' });
// 5.保存实例
kitty.save().then(() => console.log('meow'));
```

#### 基本概念

- 数据库 (database)
  - 可以有多个数据库
- 集合 (collection)
  - 一个数据库可以有多个集合
- 文档 (document)
  - 一个集合可以有多个文档

```javascript
{
    db1: {						// database
       collection1: [			 // collection
           {name: '', age: },	  // document
           {name: '', age: },
           {name: '', age: }
       ],
       collection2: [
           {name: '', age: },
           {name: '', age: }
       ]
    },
    db2: {
       ...
    }
}
```

MongoDB 非常灵活，不需要像 MySQL 一样先创建数据库、表、设计表结构

#### 使用

##### 创建结构并发布模型

```javascript
var mongoose = require('mongoose')
var Schema = mongoose.Schema

// 1.连接数据库
mongoose.connect('mongodb://localhost/myDB')

// 2.设计文档结构
var userSchema = new Schema({
  username: {
    type: String,
    required: true  // 不能省略
  },
  password: {
    type: String,
    required: true
  },
  email: {
    type: String
  }
})

// 3.将文档结构发布为模型
/* 
   第一个参数：传入一个大写名词单数字符串表示你的数据库名称，
              mongoose会自动将大写字符串转换成 小写复数 的集合名称，
              例如这里的 User 会变为 users 集合名称
   第二个参数：架构 Schema
*/
var User = mongoose.model('User', userSchema)
```

##### 增加数据

```javascript
var admin = new User({
  username: 'admin',
  password: '123456',
  email: 'admin@123.com'
})

// admin.save().then(() => {	新版用法
//   console.log('保存成功！');
// })
admin.save((err, res) => {
  if (err) {
    console.log('保存失败');
  } else {
    console.log('保存成功');
    console.log(res);
  }
})
```

##### 查询数据

- 查询所有

  ```javascript
  User.find((err, res) => {
    if (err) {
      console.log('查询失败');
    } else {
      console.log(res);
    }
  })
  ```

- 条件查询

  ```javascript
  User.find({
      username: 'admin',	// 可以添加多个条件，返回的是一个数组，即使只有一个元素
      password: '123'
  }, (err, res) => {
    if (err) {
      console.log('查询失败');
    } else {
      console.log(res);
    }
  })
  ```

- 查询一个

  ```javascript
  User.findOne({
      username: 'admin',	// 可以添加多个条件，返回的是一个对象
      password: '123'
  }, (err, res) => {
    if (err) {
      console.log('查询失败');
    } else {
      console.log(res);
    }
  })
  ```

##### 删除数据

- 删除多个

  ```javascript
  User.deleteMany({
    username: 'admin'	// 将符合条件的全部删除
  },(err, res) => {
    if (err) {
      console.log('删除失败');
    } else {
      console.log('删除成功');
      console.log(res);
    }
  })
  ```

- 删除一个

  ```javascript
  User.deleteOne({
    username: 'admin'	// 删除一个，如果符合条件有多个，则删除第一个
  },(err, res) => {
    if (err) {
      console.log('删除失败');
    } else {
      console.log('删除成功');
      console.log(res);
    }
  })
  ```

##### 更新数据

```javascript
User.findByIdAndUpdate('603647b8205e8f1918026868', {	// 根据id更新，也有其它的API
    password: '22222'		// 修改的数据
}, (err, res) => {			// 回调函数
    if (err) {
        console.log('更新失败')
    } else {
        console.log('更新成功')
    }
})
```

