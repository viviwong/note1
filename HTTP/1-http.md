---
title: HTTP的请求(request)和响应(response)
---

HTTP ，是超文本传输协议 Hypertext Transfer Protocol 的缩写。

### 资源 和 URL

HTTP是浏览器发出的请求，请求的是服务器上的资源(Resource)，对应的每一个资源都有一个URL，也就是 **统一资源定位地址** ，指向这个资源，分两种：

- 静态资源：也就是各种文件，最常见的是静态HTML，但是也可以是PDF，JSON文件等等，例子：http://haoqicat.com/peter.txt
- 动态资源：也就是URL指向的地方不是一个文件，而是一段代码的入口，服务器经过运算后（比如数据库的增删改查等操作），才返回运算结果给客户端。例子：http://haoqicat.com/username 这个可能就是指向动态资源的，后台对应的可能就是一个API

### curl 发请求

```bash
$ curl -X GET "http://haoqicat.com" -v
```
如果看一下 `man curl` 也就是查看 `curl` 的手册，可以看到 `-X` 选项后面是专门用来指定 `HTTP` 方法的。最后的 `-v` 用来显示详情。所以我们才能看到比较详尽的后续输出内容。

### HTTP请求的格式介绍

##### 返回内容的头三行

```
* Rebuilt URL to: http://haoqicat.com/
*   Trying 182.92.203.18...
* Connected to haoqicat.com (182.92.203.18) port 80 (#0)
```

上面的输出表示请求正在被发出，首先找到了 `haoqicat.com` 对应的 IP 地址：`182.92.203.18` 。然后去连接 这个 IP 对应的服务器的 80 端口。端口 也是一个重要的术语，一个服务器好比一座大厦，可以有多个大门， 一个端口就是一道门。`80` 端口是 HTTP 协议默认走的端口。

##### 请求头 Header

```
> GET / HTTP/1.1                
> Host: haoqicat.com
> User-Agent: curl/7.51.0
> Accept: */*
```

对照上面输出依次解释

- GET/POST [URL] HTTP/[version]
- Host 代表被请求的主机，也就是 haoqicat.com
- User-Agent 代表用户使用的客户端，我们这里用的是 curl
- Accept 后面指明客户端可以接受的返回资源的类型，* 代表所有类型都接受
- 更多的header字段讲解可以参考[Wikepeida](https://zh.wikipedia.org/wiki/HTTP%E5%A4%B4%E5%AD%97%E6%AE%B5%E5%88%97%E8%A1%A8)

> 当我们用 GET 发请求的时候，一般我们就是想要从服务器上 GET （拿到）一些内容，而不是想去修改服务器数据。POST 正好就是用来修改服务器上的数据的。
请求主体：没有。一般 GET 请求，是没有请求主体的。POST 一般是有主体的。
>

##### 负载数据 payload

请求头之中可能还包含一个负载数据（ payload ），GET 请求都是不带负载数据的，POST 请求带负载数据。这个挺好理解，POST 方法的请求 都是要改动服务器数据的，当然要在请求中携带数据过去。比如有一个表单 form ，我们填写几项数据，然后一点提交，这个就会发出一个 POST 请求，而我们填写的数据， 就会作为 payload 成为请求的一部分。

### HTTP响应的格式

##### 状态行
对于请求有请求行，响应的第一行也很特别，叫做状态行 （ status line ） ，基本格式如下

```
HTTP[版本号] [状态码] [状态信息]
```

一个实例

```bash
HTTP/1.1 200 OK
```

状态码:

- 20x：代表成功
- 30x：意味着资源已经被移动到其他地方了，但是响应中给出了应该跳转到哪里去找到这个资源。这个行为的 术语就叫做 redirect （重定向）。
- 40x：代表一种客户端请求错误 。一个最常见的状态码 404 ，它的意 义也跟它后面紧跟的状态信息所说的 一样：Page Not Found （页面未找到）。
- 50x：意味着服务器端在处理请求的时候出错 。50x 出现，对于开发者，一般意味着服务器端代码出了错误。

##### 响应头

```bash
Server: nginx/1.4.6 (Ubuntu)
Date: Fri, 09 Dec 2016 09:23:59 GMT
Content-Type: text/html; charset=utf-8
Transfer-Encoding: chunked
Connection: keep-alive
Vary: Accept-Encoding
```
这些信息基本上都是用来描述后面要介绍的响应主体数据的，例如，响应数据返回的时间，响应数据的格式（ html ）等等。

##### 响应主体

响应主体，response body ，也可以叫做 payload 。在前面的例子中，响应主体就是这些：

```html
<!DOCTYPE html>
<html>
<head>
  <title>haoqicat</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  ...
</head>
<body>
...

</body>
</html>
```
