---
title: 79 WebSocket 协议
date: 2021-09-04 13:55:16
tags: [WebSocket]
categories: WebSocket
---

为实现实时聊天功能，简单学习一下 WebSocket 协议。

<!-- more -->

# WebSocket

## 介绍

WebSocket 是一种网络通信协议。RFC6455 定义了它的通信标准。

WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

HTTP 协议是一种无状态、无连接、单向的应用层协议。它采用了`请求 - 响应`模型，通信请求只能由客户端发起，服务端对请求做出应答处理。

这种通信模式就会有一个弊端：HTTP 协议无法实现服务器主动向客户端发起消息。

这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦，大多数 Web 应用程序将通过频繁的异步 AJAX 请求实现长轮询。轮询的效率低，非常浪费资源，因为必须不停地连接，或者 HTTP 连接始终打开。

HTTP 协议：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210904104045.png)

WebSocket 协议：

![](https://gitee.com/gainmore/imglib/raw/master/img/20210904104121.png)

WebSocket 协议分为两个阶段：**握手**和**数据传输**。



## 建立连接（握手）

WebSocket 复用了 HTTP 协议的握手通道。具体指的是，客户端通过 HTTP 请求与 WebSocket 服务端协商升级协议。协议升级完成后，后续的数据交换则遵照 WebSocket 的协议。

### 1.客户端：申请协议升级

首先，客户端发起协议升级请求。可以看到，采用的是标准的 HTTP 报文格式，且只支持`GET`方法。

```http
GET ws://localhost:8080 HTTP/1.1
Host: localhost:8080
Origin: http://127.0.0.1:3000
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: w4v7O6xFTi36lq3RNcgctw==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

字段说明：

| 字段名                    | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| Connection: Upgrade       | 表示要升级协议                                               |
| Upgrade: websocket        | 表示要升级到websocket协议                                    |
| Sec-WebSocket-Version: 13 | 表示websocket的版本。如果服务端不支持该版本，<br />需要返回一个`Sec-WebSocket-Version`header，里面包含服务端支持的版本号 |
| Sec-WebSocket-Key         | 与后面服务端响应首部的`Sec-WebSocket-Accept`是配套的，<br />提供基本的防护，比如恶意的连接，或者无意的连接 |
| Sec-WebSocket-Extensions  | 表示协议扩展类型                                             |



### 2.服务端：响应协议升级

服务端返回内容如下，状态代码`101`表示协议切换。到此完成协议升级，后续的数据交互都按照新的协议来。

```http
HTTP/1.1 101 Switching Protocols
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: Oy4NRAQ13jhfONC7bP8dTKb4PTU=
```

其中，`Sec-Websocket-Accept` 头部的值是根据如下规则计算的：

- 首先将一个固定的字符串 `258EAFA5-E914-47DA-95CA-C5AB0DC85B11` 拼接到 `Sec-WebSocket-Key` 对应值的后面。
- 对拼接后的字符串进行一次 `SHA-1` 计算
- 将计算结果进行 Base64 编码

### 3.客户端：验证报文格式

客户端收到服务端的握手包之后，验证报文格式是否符合规范。（即用 2 中同样的方式计算 Sec-WebSocket-Accept 并与服务端握手包里的值进行比对）

### 总结

- 首先，客户端会向服务端发送一个 HTTP 协议的握手包，表示申请协议升级。这个握手包必须是 GET 请求而且 HTTP 版本不能小于 1.1，然后会有这些字段：`Connection: Upgrade`，`Upgrade: websocket`、`Sec-Websocket-version: 13`、`Sec-Websocket-key: 值为Base64格式的16字节随机字符串`；
- 之后，服务端也会响应一个握手包并且状态码为 101 （切换协议），表示响应协议升级。这个握手包也有 `Connection: Upgrade` 和 `Upgrade: websocket` 字段，同时还增加了 `Sec-Websocket-Accept` 字段，这个字段的值是将一个固定字符串拼接到 `Sec-Websocket-key` 的后面，然后进行 `SHA-1` 加密并且转换成 Base64 格式得到的；
- 最后，客户端收到握手包之后，会用同样的方式得到一个 `Sec-Websocket-key` 的值，然后和服务端发来的 `Sec-websocket-Accept` 比较，验证通过后就建立起 WebSocket 连接。



## 数据传输

### 客户端

#### WebSocket对象

创建 `WebSocket` 对象：

`const ws = new WebSocket(url)`

> 其中 url 格式如：`ws://ip地址:端口号/资源名称`

#### WebSocket事件

| 事件    | 监听函数                                        | 描述                       |
| ------- | ----------------------------------------------- | -------------------------- |
| open    | ws.onopen / ws.addEventListener('open', ()=>{}) | 连接建立时触发             |
| message | ws.onmessage                                    | 客户端收到服务端数据时触发 |
| error   | ws.onerror                                      | 通信发生错误时触发         |
| close   | ws.onclose                                      | 连接关闭时触发             |

#### WebSocket方法

| 方法   | 描述                   |
| ------ | ---------------------- |
| send() | 客户端向服务端发送数据 |



#### 代码

```js
const ws = new WebSocket('ws://localhost:8080')

ws.onopen = function() {
  console.log('WebSocket open')
  ws.send(JSON.stringify({ data: 'Hello Server!' })) // 注意，发送的必须是字符串
}

// 错误
ws.onerror = function() {
  console.log('WebSocket error')
}

// 关闭
ws.onclose = function() {
  console.log('WebSocket close')
}

// 接收消息
ws.onmessage = function(e) {
  // 接收的是一个Event对象，需要将数据解析出来
  console.log(JSON.parse(e.data))
}
```



### 服务端

这里服务端用了`ws`这个库。相比大家熟悉的`socket.io`，`ws`实现更轻量，更适合学习的目的。

#### 代码

```js
const WebSockect = require('ws')

const server = new WebSockect.Server({ port: 8080 })

// server 实例监听连接事件，回调函数接收到一个 WebSocket 实例
server.on('connection', ws => {
  console.log('连接成功')

  // 监听客户端发来的消息
  ws.on('message', msg => {
    console.log(server.clients.size) // 连接客户端的数量
    console.log(JSON.parse(msg))
    server.clients.forEach(client => {
      client.send(JSON.stringify({ data: 'Hello Client!' }))
    })
  })

  // 连接关闭
  ws.on('close', () => {
    console.log('连接关闭')
  })
})
```



## 参考资料

- https://juejin.cn/post/6844903544978407431
- https://juejin.cn/post/6844904001654226958
- https://juejin.cn/post/6844903478045704200

