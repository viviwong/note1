---
title: 学习React搭建的一个基本的开发环境
---

# 只是为了学习React而搭建一个最基本的环境

### 首先初始化项目

```bash
npm init -y
// 参数 -y 是yes的意思，不用一路回车
```
生成了一个 `package.json`，来记录包和其他的一些信息。

**例如：**

```json
// package.json
{
  "name": "React-demo",
  "version": "1.0.0",
  "description": "",
  "main": "./src/index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "./node_modules/.bin/webpack --watch",
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.23.1",
    "babel-loader": "^6.3.2",
    "babel-preset-env": "^1.1.8",
    "babel-preset-react": "^6.23.0",
    "webpack": "^2.2.1",
    "webpack-dev-server": "^2.3.0"
  },
  "dependencies": {
    "react": "^15.4.2",
    "react-dom": "^15.4.2"
  }
}
```

### 使用 npm 安装下面的包

- `webpack` 是一款强大的模块加载器兼打包工具，它能把各种资源，例如JS（含JSX）、coffee、样式（含less/sass）、图片等都作为模块来使用和处理。
- `webpack-dev-server` 是一个小型的node.js Express服务器，它使用webpack-dev-middleware中间件来为通过webpack打包生成的资源文件提供Web服务。
- `babel-core` 用来将 `ES6` 语法转换为兼容性好的 `ES5` 语法
- `babel-loader` loaders转换器
- `babel-preset-env`
- `babel-preset-react`
- `react` 是 React 的核心库。
- `react-dom` 是 React 的 DOM 适配库。

### webpack.config.js文件配置

```js
//webpack.config.js
module.exports = {
  entry: './src/index.js', //入口文件
  output: {      //出口文件
    path: './js/', //打包后文件存放目录
    filename: 'new.js' //打包后的文件名
  },
  devtool: 'eval', //报错后可以查看源码错误位置

  module: {
  //module: webpack将所有的资源都看做是模块，而模块就需要加载器。主要定义一些loaders,定义哪些后缀名的文件应该用哪些 loader
  rules: [
    { test: /\.js$/, exclude: /node_modules/, use: "babel-loader" }
    //test: 检测哪些文件需要此loader,是一个正则。
    //exclude：忽略哪些文件
  ]
}
}
```

### .babelrc文件配置

```js
//.babelrc
{
  "presets": ["env","react"]
}
```

### 结束
自此搭建一个React学习的开发环境基本结束，后面再做React项目中去进一步的学习 `webpack` 的各种配置

**一些学习的资源:**
- [逼哥的webpack教程](https://github.com/chenbin92/react-redux-webpack-starter/issues/1)
