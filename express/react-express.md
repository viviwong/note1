---
title: react-express 实现前后台通信
---

### Express简介
Express 的 [官网位置](http://www.expressjs.com.cn/) 。官网上，首页最能吸引我们注意的就是 API 这个关键字。API （ Application Program Interface ）是应用开发接口，简称 **接口** 。而 Express 就是用来 **制作后台接口** 的，或者说叫制作后台 API 的。

### 如何制作一个Express后台

- 要新建文件夹，并把它初始化为一个 Nodejs 项目，这样文件夹内就会生成一个 package.json ，有了这个文件，我们这个文件夹就可以 叫做一个 Nodejs 项目 了 。
- 进行装包 `npm install --save express`
- 在项目中创建 `index.js`

```js
const express = require("express");

let app = express();
app.get("/username",function(req,res){
  res.send({username:"yewenxiang"});
});
app.listen(3000,function(){
  console.log("running on port 3000..");
});
```
我们就自己动手实现了一个 **服务器** ( server ) 。服务器（这里指的是软件）的作用是，始终**监听客户端的请求，或者说前端不给服务器发信号，服务器 就什么都不做，但是也不死，只是去循环执行，或者就叫始终在监听（listen）。
上面的 3000 指的是 3000端口 ，端口的英文是 port ，一个服务器好比一座大厦，有很多个门，3000 是其中一个门的门牌号。

- 然后执行 `node index.js` 就可以执行进行监听了。
- 或者装一个全局的包 `nodemon`,然后 `nodemon index.js` 当文件修改后会自动重启服务。

### Express 服务器运行起来了。

服务器监听端口后，唯一的作用就是来根据端口传入的请求，来执行特定代码。
比如，我们在上面的 index.js 中，app.listen 语句的上面，添加如下代码：

```js
app.get("/username",function(req,res){
  res.send({username:"yewenxiang"});
});
```

- get 是一个http请求的动词，类似的还有 post/delete/put。
- "/username" 是一个路径 ，英文 path。
- res.send('Hello World'); 的作用是从后端向前端浏览器返回字符串 Hello World 。
- req 是 request 请求的简写， res 是 response 响应的简写 。

### 如何发送请求

可以发请求的方式不唯一，可以用浏览器地址栏，可以用页面的 form 发， 也可以用 axios 发，后者使用专门的 API 调试工具 curl/postman 来发。
Postman 是一个辅助工具，用它的目的就是美观方便。但是 Postman 需要 用到谷歌的服务，所以需要我们的机器可以翻墙才行。
现在，我们就用浏览器的地址栏来发请求。地址栏中输入

```
http://localhost:3000/username
```
注： localhost 就是我们自己机器的域名。(ip:127.0.0.1),上面的请求，默认动词就是 GET ，同时 :3000 用来指定端口号。

### 前端和后端

前端，front-end，或者也可以叫前台。后端，back-end 也可叫后台。
前端代码运行环境是什么呢？对于我们 Web 开发者来说，就是浏览器。注意，浏览器是安装在用户自己的机器上的。也就是说前端代码运行在我们自己的笔记本或者 ipad 上，如果前端代码写的烂，那么考验的是我们自己设备的内存大小。
后端代码运行环境是？是一个放在人家机房里的刀片机。上面运行 Linux 操作系统。刀片机根本就没有显示器，当然也不能跑浏览器。所以后端代码的运行是脱离浏览器的。如果后端写的烂，那么考验的就是刀片机的内存够不够了。
然后，再从 API 的角度来聊聊。前端是 API 的消费者，后端是 API 的生产者。后台 API 写好之后，默认不运行，只有当前端发送过请求来的时候才会触发后台 API 代码运行。
当然，在平常开发的时候，我们并没购买刀片机，所有只能是用自己的笔记本来当刀片机用了。这时候，基本可以认为 express 写的代码就是后端代码，react 写的代码就是前端代码。

### 用 curl 来测试 API

curl 是一个安装在系统上的命令，可以用来发 http 请求，最适合用来测试 API 。
动手写前端代码之前，如果用 curl 测试一下 API ，会让写前端代码的时候，我们心里更踏实。

```
curl -X GET 'http://localhost:3000/username'
```

### axios 发请求

我们当前的请求不希望是通过按钮来触发，而是希望，页面加载的时候，自动发出 http 请求，向服务要数据，所以，代码非常适合写到生命周期函数中：

```js
componentWillMount() {
  axios.get('http://localhost:3000/username').then(function(response){
      return console.log(response);
    }
  )
}
```

### 跨域请求 Access-Control-Allow-Origin

代码进行到上面，浏览器中用前台请求后台，会在 chrome console 中看到，如下 错误：

```bash
XMLHttpRequest cannot load http://localhost:3000/. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'null' is therefore not allowed access.
```
无法加载后台 http://localhost:3000 . 被请求的资源中没有设置 Access-Control-Allow-Origin 头部。源头设置为 Null ，所以不允许 访问。

那么，如何开通服务器上的这个资源访问权限呢？就是要在服务器上做下面的设置

```
Access-Control-Allow-Origin: *
```
有了这个设置，所有的第三方网站都可以访问服务器上的资源了。

### 跨域请求的解决方案

解决方案采用： https://github.com/expressjs/cors
cors 是 Cross Origin Resource Share ，安装了这个包就可以完成

```
curl -I http://localhost:3000/
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 11
ETag: W/"b-sQqNsWTgdUEFt6mb5y4/5Q"
Date: Thu, 08 Dec 2016 01:51:44 GMT
Connection: keep-alive
```
curl 的 -I 选项用来专门拿到服务器返回的 header 。命令返回的信息，就是服务器端被请求资源的的 header 。上面很明显是没有 Access-Control-Allow-Origin 这一项的。下面我们安装 [cors](https://www.npmjs.com/package/cors) 这个包，就可以解决这个问题。

然后按照文档，添加下面两行代码，再重启服务器代码：

```js
const cors = require('cors')
app.use(cors());
```

接下来再用 curl -I 看看输出如下：

```
HTTP/1.1 200 OK
X-Powered-By: Express
Access-Control-Allow-Origin: *
Content-Type: text/html; charset=utf-8
Content-Length: 11
ETag: W/"b-sQqNsWTgdUEFt6mb5y4/5Q"
Date: Thu, 08 Dec 2016 02:17:23 GMT
Connection: keep-alive
```

这样，我们就看到了 Access-Control-Allow-Origin: * 。
浏览器中，刷新一下，可以看到后台返回的 response 数据了。错误没有了。
