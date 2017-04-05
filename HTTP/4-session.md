---
title: 前后端不分离实现登录
---

### 返回一个静态 HTML 页面

```js
//index.js
app.get('/', function(req, res){
   res.sendFile('index.html', {root: 'public'});  // public/index.html
 })
```
浏览器打开 `localhost:3006` 或者 `localhost:3006/` 没问题。但是打开 `localhost:3006/index.html` 结果访问不到解决如下:

```diff
+ app.use(express.static('public'));
```
上面这一行的作用，就是把 public/ 文件夹，架设成了一个静态（ static ）服务器，也就 public/ 中所有的 html 页面，未来都可以不走 API ，直接在浏览器中，用链接的形式访问到。

### 获取请求体中的属性值

```js
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/x-www-form-urlencoded
app.post('/login', function(req, res){
  console.log(req.body);
})
```

### 重定向 redirect

redirect 接口实现的是“页面重定向”,点击登陆后会跳到 `/` 这个API。
```js
app.post('/login', function(req, res){
   let username = req.body.username;
   // User.find({username: username}) 如果数据库中能找到这个用户
   // 同时密码也对，这样才算登录成功
   if(true) {
     res.redirect('/');
   }
 })
```

### 使用 req.session 接口

```diff
+ const session = require('express-session')
+ app.use(session({
+   secret: 'keyboard cat',
+   resave: false,
+   saveUninitialized: true
+ }))

app.post('/login', function(req, res){
   let username = req.body.username;
+  req.session.username = username;
   if(true) {
     res.redirect('/');
   }
 })
```
`req.session` 可以理解为一个特殊的变量，上面吧 username 存进了这个变量中，可以在各个API之间（页面之间）共享。只要本次回话不结束，这个变量就不会消失。

补充：Session 是会话的概念，一次会话其实就是一段时间，从用户登录网站开始，到用户退出网站结束的这个时间区间，就叫一次会话。上面的 req.session 是一个临时的存储区域，这个存储区域的生命周期是一次会话。

### 观察浏览器中是否有 sessionId

浏览器中，我们到 login 页面，填写用户名，提交，那么里面会有 session 的操作，也就是

```JS
req.session.username = username
```
也就是，服务器端的 session 已经创建了。那么浏览器中的体现就是有一个 cookie 被创建了，到 Application -> storeage -> Cookie -> localhost:3006 之下，就可以看到，有这样的 cookie 数据：

```
connect.sid   xxxxxfdsjfkldsjfklsdjxxxx
```
上面的 sid 意思就是 Session Id ，也就是是服务器端 req.session 的对应的 id 。由于，客户端，每次请求都会携带 cookie 去服务器端，所以后续每次请求，都可以拿到服务器端 req.session 中存储的数据。

例如：

```js
app.get('/hello', function(req, res){
  res.send(req.session.username)
})
```
小陷阱：后台每次添加代码，服务器都会重启，所以要重新执行一下 POST /login 生成一下 session 才能测试。

### 一个问题

```diff
app.get('/', function(req, res){
+  console.log('home page', req.session.username);
  res.sendFile('index.html', {root: 'public'});
})
```
我们期待的是，每次访问 `/` 这个 API ，都能打印出 sesssion 变量的值。实际中是不能打印出来的。因为直接访问 `http://localhost:3006/` 这个位置，就相当于访问 `http://localhost:3006/index.html` ,而我们已经把 public 文件夹架设成静态服务器了， `app.get('/')` 这个接口根本不会执行。

删除 `app.use(express.static('public'));` 就正常了。

### 静态路径修改

由于取消了静态服务器，所以 login.html 也直接访问不到了。

```diff
</head>
<body>
-  <a href="/login.html">login</a>
+  <a href="/login">login</a>
</body>
```

```js
//index.js
app.get('/login', function(req, res){
  res.sendFile('login.html', {root: 'public'});
})
```
通过API来发送回 login 页面。

### 使用 pug 模板

如何在页面中显示 `req.session.username` 这个变量？需要通过模板引擎来渲染一个HTML页面，底层是通过JS代码来渲染一个HTML页面的，然后发送给客户端.

- [pug模板](https://pugjs.org/api/getting-started.html)

```diff
+ const pug = require('pug');
+ app.set('view engine', 'pug')
/*设置模板文件路径和使用模板*/

app.get('/', function(req, res){
-  res.sendFile('index.html', {root: 'public'});
+  let currentUser = req.session.username;
+  res.render('index', { username: currentUser })
})

+app.get('/logout', function(req, res){
+  req.session.destroy();
+  res.redirect('/');
+})
```

```
//views/index.pug

html
  body
    if currentUser
      span= currentUser
      a(href='/logout') 退出
    else
      a(href='/login') 登录

```
