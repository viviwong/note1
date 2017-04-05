---
title: Cookie
---

### Cookie的诞生

HTTP的最大一个特点是 `无状态的协议` ，客户端发送一次请求给服务器，走一个请求和响应的循环后，服务器就立刻忘记了客户端，当这个客户端再次访问服务器的时候，服务器是不认识这个客户端的，也就是，客户端和服务器之间是不能保持一种 **链接状态** 的。
为了实现这种链接状态，cookie 被发明出来了。

### Cookie的机制

当客户端第一次与服务器发请求时，通过http响应的头部返回给浏览器，浏览器拿到Cookie数据后保存在Cookie文件夹中，后面每次发送请求，Cookie中的信息会被设置成http请求的一个header发送给服务器，这样客户端和服务器之间就保持了一种链接的状态。

例子：

```js
response.setHeader('Set-Cookie','id=123')
```
使用curl测试

```bash
curl -I tiger.haoduoshiping.com/cookie-test
```
可以看到响应头部中包含 `'Set-Cookie','id=123'`,后续再去访问任意其他接口，服务器端都可以收到 cookie 中的数据了。

### 使用 curl 测试带 cookie 的接口

curl 不是浏览器，所以不是每次 curl 请求都会自带 cookie 到服务器，需要明文指出

```bash
$ curl -v --cookie "id=123" http://tiger.haoduoshipinc.com/hello
```

### cookie的限制

cookie 中储存的数据不能太多（小于4K），后面又出现了HTML5的API local storeage 内存大小为5M

### session的基本原理

发明 cookie 是为了解决服务器和客户端不想认的问题，但是新的问题又来了，cookie的储存能力有限，如果一个用户买100000件商品，这些商品信息存储在哪呢？

当前最流行的做法就是，每次有一个新的浏览器用户登录进来，服务器端都会为这个浏览器开辟一个下的内存区域（这个区域可以认为是一个文件，但这个文件存在服务器端），通常术语就叫`创建了一个新的session` ，在这个session之中，服务器就可以为这个特定的用户保存任意的信息到session之中了（例如：用户名，购物车中的商品信息）。但是同一时刻，连接到服务器的浏览器成千上万，服务器是通过使用cookie来识别不同的浏览器的。

每个服务器端的 session 被创建的时候，都会有一个类似于文件名的东西，叫做 sessionId。每个session 在服务端被创建的时候，sessionId 都会自动被发送到浏览器的 cookie 中保存了，这样每个浏览器就跟服务器上各自的 session 有了绑定关系。

### 代码演示

自己手动基于 cookie 机制，去服务器上开辟 session 比较麻烦。所以各大语言框架都有自己的 session 管理接口，例如，express 的接口就是 [express-session](https://github.com/expressjs/session) 。

```js
//后台代码
const session = require('express-session')

app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: true
}))

app.post('/login', function(req, res){
  let username =  req.body.username;
  //来自浏览器请求的 username 的值
  req.session.username = username ;
  //把字符串保存在 session 中，之后服务器会自动把 sessionId 返回给浏览器，存储在cookie中，req.session 接口内置cookie操作功能
  res.send(req.session.username);
})
```

> req.session 可以认为是一个可以跨越请求始终存在的变量。用户访问了 POST /login 之后再去访问其他的 API 也依然可以读取到 req.session.username 的值。
>

### 使用curl测试

```bash
$ curl -v -X POST -H "Content-Type: application/json" -d '{"username": "peter"}' http://tiger.haoduoshipin.com/login
```

### 单页面应用中 Cookie 使用不方便了

涉及到跨域问题，在 React-Axios 环境下，收发 cookie 默认都是不允许的，所以 req.session 的使用意义不大了。
