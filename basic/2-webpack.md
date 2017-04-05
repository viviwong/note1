---
title: webpack自动刷新和模块热加载
---
### 前言

关于webpack-dev-server的简单介绍：webpack-dev-server是一个小型的node.js Express服务器,它使用webpack-dev-middleware中间件来为通过webpack打包生成的资源文件提供Web服务。它还有一个通过Socket.IO连接着webpack-dev-server服务器的小型运行时程序。webpack-dev-server发送关于编译状态的消息到客户端，客户端根据消息作出响应。

### webpack-dev-server后面的常用参数

- `webpack-dev-server --devtool eval` 为你的代码创建源地址。当有任何报错的时候可以让你更加精确地定位到文件和行号
- `webpack-dev-server --progress` 显示合并代码进度
- `webpack-dev-server --colors`  命令行中显示颜色
- `webpack-dev-server --content-base build` webpack-dev-server服务会默认以当前目录伺服文件，如果设置了content-base的话，服务的根路径则为build目录
- `webpack-dev-server --inline` 可以自动加上dev-server的管理代码，实现热更新
- `webpack-dev-server --hot` 开启代码热替换，可以加上HotModuleReplacementPlugin
- `webpack-dev-server --port 3000` 设置服务端口

##### 使用devServer字段配置上面的参数
在运行webpack-dev-server的时候，后面带了一串参数，这里我们可以使用devServer字段统一在webpack.config.js文件里面维护。

```js
/* webpack.config.js */
module.exports = {
  devServer: {
    publicPath: "/static/",
    stats: { colors: true },
    port: 3000,
    inline: true
  }
};
```

### 自动刷新配置

- 首先需要安装 `webpack-dev-server` 这个包。
- 然后拷贝 `webpack.config.js` 这个文件，重命名为 `webpack-dev-config.js` 这个文件，名字是业内大家都这样定义的命名，一个是开发版的配置文件，一个是开发完成后的配置文件
- 在 `package,json` 文件中写入脚本命令。

```json
"scripts": {
  "dev":"./node_modules/.bin/webpack-dev-server --config webpack.dev.config.js"
}
```

表示在命令行中输入 `npm run dev` 去查找 `webpack-dev-config.js` 这个配置文件。

##### webpack-dev-config.js 文件配置。
这个包的作用是在搭建起来一个本地化的服务器，运行命令 `npm run dev` 后，它并没有项目内新建一个js文件夹，而是在计算机内存中建立了一个文件夹(包括文件)。只能在浏览器的 `sources` 下面的 `localhost:8080` 中看到，他的根目录就是 `localhost:8080` 这个文件夹用 `/` 来表示，所以需要注意一下几点。

```js
//webpack-dev-config.js
output: {
    path: '/js/',          //这里必须加上 / 表示根目录
    filename: 'new.js',
    publicPath: '/js/'    //这里必须加上 /
    //告诉index.html,打包后的文件路径
  },
```

```html
<script src="/js/new.js"></script>
 <!-- 这里也是需要加上'/' -->
```
然后在浏览器中输入 `localhost:8080` 就可以实现自动刷新了。
