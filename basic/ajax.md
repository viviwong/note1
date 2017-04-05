---
title: AJAX
---

### 什么是AJAX?
AJAX就是使用JS来实现页面的局部刷新，官方的解释是（异步的Javascript和XML）,以前和服务器端传输或者请求数据是用的XML的格式，HTML标签和数据是混杂在一起的，非常的不方面。现在都是使用JSON这种格式来实现数据的传输了。

### 原生的JS来实现AJAX技术

```js
var ajax = new XMLHttpRequest();
ajax.onreadystatechange=function(){
  if(ajax.readyState==4 && ajax.status==200){
    let data = JSON.parse(ajax.responseText);
    document.getElementById("name").innerHTML = data.login;
  }
}
ajax.open("GET","https://api.github.com/users/yewenxiang23",true);
ajax.send();
```

### JQ库来实现AJAX技术

```js
<script src="http://cdn.bootcss.com/jquery/3.1.1/jquery.min.js"></script>
<script>
  $.ajax({
    type:"GET",
    dataType:"jsonp",
    jsonp:"callback",
    url:"https://api.douban.com/v2/book/1220562",
    success:function(data,status){
      console.log(data, status);
    }
  })
</script>

```

[官方的API](http://www.css88.com/jqapi-1.9/jQuery.ajax/)

### ES6的新语法来实现

```js
<script>
  //get
  fetch("https://api.github.com/users/yewenxiang23",{method:"GET"})
  .then(response => response.json())
  .then(json => console.log(json))


  //post
  fetch("https://...",{
    method:"POST",
    header:{"Content-Type":"application/json"},
    body:JSON.stringify({
      accesstoken: "....."
    })
  })
  .then(res => res.json())
  .then(json => console.log(json))
</script>
```

[github参考地址](https://github.com/github/fetch)

### 使用一个HTTP库axios来实现

```js
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
  //get
  axios.get("https://api.github.com/users/yewenxiang23")
  .then(res=>console.log(res.data))
  .catch(error=>console.log(error));

  //post
  axios.post("https://......",{accesstoken:"....."})
  .then(res=>console.log(res.data))
  .catch(error=>console.log(error))
</script>
```

[github参考资料](https://github.com/mzabriskie/axios)

### 跨域请求

跨域请求http，协议的规定就是不同源之间不能进行资源共享。http://api.github.com:80 这一段如果有不同的都叫跨域，一般后台设置Access-Control-
jsonp 原理 ：

```
dataType:"jsonp",
jsonp:"callback",
//jsonp 只能请求 get
```
