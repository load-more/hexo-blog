---
title: 48 “幸福外卖”项目【11】
date: 2021-03-24 20:26:04
tags: [Vuejs, SMS]
categories: 项目
---

实现“发送验证码”功能

<!-- more -->

目前知道两种发送验证码的方式：

- 使用 `容联云通讯`
  - 官网：https://www.yuntongxun.com/
- 使用 `SMS`
  - 官网：http://sms.webchinese.com.cn/
  - 参考教程：https://www.pianshen.com/article/9137152038/

由于第二种方式要花钱，而第一种方式会免费赠送八块钱，所以果断使用第一种方法！

使用步骤：

1. 在官网注册个账号，绑定测试号码，并获取主要参数。

![](https://gitee.com/gainmore/imglib/raw/master/img/20210324212138.png)

2. 编写后端接口。

   这里主要涉及两个文件：

   - routes 下的 index.js
     - 用于编写 /sendcode 接口
   - util 下的 sms_util.js
     - 用于发送验证码（主要）

   ![](https://gitee.com/gainmore/imglib/raw/master/img/20210324212331.png)

    下面看看这两个文件的主要内容，首先是 sms_util.js:

   ```javascript
   var md5 = require('blueimp-md5')
   var moment = require('moment')
   var Base64 = require('js-base64').Base64;
   var request = require('request');
   
   /*
    生成指定长度的随机数
    */
   function randomCode(length) {
       var chars = ['0','1','2','3','4','5','6','7','8','9'];
       var result = ""; //统一改名: alt + shift + R
       for(var i = 0; i < length ; i ++) {
           var index = Math.ceil(Math.random()*9);
           result += chars[index];
       }
       return result;
   }
   // console.log(randomCode(6));
   exports.randomCode = randomCode;
   
   /*
   向指定号码发送指定验证码
    */
   function sendCode(phone, code, callback) {
       var ACCOUNT_SID = '8aaf0708780055cd01785f42eede2325';
       var AUTH_TOKEN = '089ae9bf4c804bbdb75b15fabf8fc282';
       var Rest_URL = 'https://app.cloopen.com:8883';
       var AppID = '8aaf0708780055cd01785f42efb9232c';
       //1. 准备请求url
       /*
        1.使用MD5加密（账户Id + 账户授权令牌 + 时间戳）。其中账户Id和账户授权令牌根据url的验证级别对应主账户。
        时间戳是当前系统时间，格式"yyyyMMddHHmmss"。时间戳有效时间为24小时，如：20140416142030
        2.SigParameter参数需要大写，如不能写成sig=abcdefg而应该写成sig=ABCDEFG
        */
       var sigParameter = '';
       var time = moment().format('YYYYMMDDHHmmss');
       sigParameter = md5(ACCOUNT_SID+AUTH_TOKEN+time);
       var url = Rest_URL+'/2013-12-26/Accounts/'+ACCOUNT_SID+'/SMS/TemplateSMS?sig='+sigParameter;
   
       //2. 准备请求体
       var body = {
           to : phone,
           appId : AppID,
           templateId : '1',
           "datas":[code,"1"]
       }
       //body = JSON.stringify(body);
   
       //3. 准备请求头
       /*
        1.使用Base64编码（账户Id + 冒号 + 时间戳）其中账户Id根据url的验证级别对应主账户
        2.冒号为英文冒号
        3.时间戳是当前系统时间，格式"yyyyMMddHHmmss"，需与SigParameter中时间戳相同。
        */
       var authorization = ACCOUNT_SID + ':' + time;
       authorization = Base64.encode(authorization);
       var headers = {
           'Accept' :'application/json',
           'Content-Type' :'application/json;charset=utf-8',
           'Content-Length': JSON.stringify(body).length+'',
           'Authorization' : authorization
       }
   
       //4. 发送请求, 并得到返回的结果, 调用callback
   	  // callback(true);
       request({
           method : 'POST',
           url : url,
           headers : headers,
           body : body,
           json : true
       }, function (error, response, body) {
           console.log(error, response, body);
           callback(body.statusCode==='000000');
           // callback(true);
       });
   }
   exports.sendCode = sendCode;
   
   /*
   sendCode('13716962779', randomCode(6), function (success) {
       console.log(success);
   })*/
   
   ```

   然后是 index.js ：

   ```javascript
   router.get('/sendcode', function (req, res, next) {
     //1. 获取请求参数数据
     var phone = req.query.phone;
     //2. 处理数据
     //生成验证码(6位随机数)
     var code = sms_util.randomCode(6);
     //发送给指定的手机号
     console.log(`向${phone}发送验证码短信: ${code}`);
     sms_util.sendCode(phone, code, function (success) {//success表示是否成功
       if (success) {
         console.log('保存验证码: ', phone, code)
         res.send({"code": 0, data: code})
       } else {
         //3. 返回响应数据
         res.send({"code": 1, msg: '短信验证码发送失败'})
       }
     })
   })
   ```

   完成上述操作后，后端接口就实现了，接下来就是前端调用了。

   首先，新建一个 axios 实例，用于验证码的发送。

   ```javascript
   export function ajax2(url, params={}) {
     const instance = axios.create({
       baseURL: 'http://localhost:3000',
       timeout: 5000,
     })
   
     instance.interceptors.request.use(config => {
       return config
     }, err => {
       console.log(err);
     })
     instance.interceptors.response.use(response => {
       return response.data
     }, err => {
       console.log(err);
     })
   
     return instance({
       url,
       params, // 用于 get请求，加入url后
       // method: 'get' // 默认使用 get 请求
     })
   }
   ```

   **这里有个注意点，instance 传入参数中网络请求的参数有两个 -- params和data，params用于get请求，即拼接在url后面的字符串部分；data 用于post请求，构成请求体；默认是get请求方式，所以这里不用声明method。**

3. 调用函数。

   ```javascript
   export function sendCode(phone) {
     return ajax2('/sendcode', {phone})
   }
   ```

   调用上面的函数即可。