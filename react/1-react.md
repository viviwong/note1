---
title: jsx语法基础(组件创建的方式，导入图片和css文件，css样式写法)
---

### React环境搭建完毕后
在主入口文件 `index.js` 文件中引入 `react` 模块。

```
import React from "react";
import ReactDOM from "react-dom";
```

### React的基本语法

```
let age = 24;
let sex = 1;
let obj = {name:"hahaha"};
let add = () => 5+8;
let a = <div>
          <h1>叶文翔</h1>
          {/* 我是注释 */}
          <h1>年龄:{age+1}</h1>
          <h1>{obj.name}</h1>
          <h1 className={sex ? "a" : "b"}>{add()}</h1>
          <h1>性别:{sex ? "男" : "女"}</h1>
        </div>;

ReactDOM.render(
  a,document.getElementById("app")
)

```
- JSX语法，允许我们直接在JS里面去写标签。
- 每个标签必须关闭。
- 必须由一个标签来包裹，不然会报错
    - Module build failed: SyntaxError: Adjacent JSX elements must be wrapped in an enclosing tag (7:6)。
- jsx标签语句里面注释 `{/* something */}`。
- 我们可以在jsx元素内嵌入变量，方法，对象，等 {a}。
- class 写为 className，tabindex 为 tabIndex，for 写为htmlFor。
- JSX 语法会被编译，通过 `ReactDOM.createElement()` 这个方法。

### 组件component的3种方式
注意函数的首字母要大写，不然会报错。

```
//第一种方法 ES5的写法，现在基本没人用了
var Greeting = React.createClass({
    render: function(){
      return <h1>ye</h1>;
    }
})
//第二种方法常用在DOM结构的组合
function Greeting(){
  return <h1>hello</h1>;
}

//第三种
class Greeting extends React.Component {
  render(){
    return (
      <div>
        <h1>hello</h1>
      </div>
    )
  }
}

ReactDOM.render(
  <Greeting />,document.getElementById("app")
)
```

- [三种方法详解](http://www.cnblogs.com/wonyun/p/5930333.html)

### 组件的导入导出

##### 导入组件：

```
//index.js
import ReactDOM from "react-dom";
import App from "./App";
//导入App组件

ReactDOM.render(
  <App />,document.getElementById("app")
);
//把组件渲染到真正的DOM结构中
```

##### 导出组件:

```
import React from "react";
import Header from './Header';
import Footer from './Footer';
import Banner from './Banner';

class App extends React.Component{
  render(){
    return(
      <div>
        <Header />
        <Banner />
        <Footer />
      </div>
    )
  }
}
export default App;
//导出App这个默认的组件。
```

### CSS样式的几种写法
直接写在行内（也就是标签内）需要 `style = {{}}` 这种写法，第一个大括号表示JS语法，第二个大括号表示一个对象。

```
class Signin extends React.Component{
  setStyle(){
    return {float:"right"}
  }
  render(){
    let color = 0;
    let style = {
      leftBtn:{
        background: color ? "red" : "yellow"
      },
      rightBtn:{
        background:"blue"
      }
    }
    return (
      <div style={this.setStyle()}>
        <button style={style.leftBtn}>登录</button>
        <button style={style.rightBtn}>注册</button>
      </div>
    )
  }
}
```

>在CSS属性值中可以写成三目运算符的形式。
this.setStyle() this表示的是 Signin 这个对象。如果是自定义函数中this 是指向 null 的。因为ES6中自定义的函数没有this指向，需要手动绑定，如 this.setStyle.bind(this) 来绑定this指向 Signin 。
>

直接增加className这个类，有两种方式：

- 一种是在 `index.html` 文件的头部中写好样式，标签中eg：`<div className="a"></div>`
- 还有一种方式是建立一个CSS文件，然后在 `index.js` 文件中导入CSS文件，然后在JSX语法的标签中添加类。

### 如何导入CSS文件到index.js中
首先需要安装两个包 `css-loader` 和 `style-loader`

```js
//webpack.config.js
module.exports = {
  resolve: { //  定义能够被打包的文件，文件后缀名
    extensions: [".js", ".css", ".jpg", ".png"] //扩展，定义能忽略的文件名后缀
  },
  module: {
  rules: [
    { test: /\.css$/,use: ['style-loader', 'css-loader'] }
    //定义css文件的编译loader，这两个loader是有载入顺序的。
  ]
}
}

```

然后导入到 `index.js` 文件中去就行

```js
//index.js
import "./main";
//由于上面加了忽略文件后缀，所以这里不用写 main.css
```

- [导入CSS官方教程](https://github.com/webpack-contrib/css-loader)

### 如何导入图片
首先需要装 `file-loader` 这个包

```js
//webpack.config.js
module.exports = {
    output: {
    publicPath: 'js/'
    //告诉index.html,打包后的文件路径
  },
    module: {
    rules: [
      { test: /\.(jpe?g|png)$/, use: "file-loader"}
      //定义图片的拷贝使用的loader
    ]
  }
}
```

配置完成后，导入需要图片的JS文件中

```
import React from "react";
import img from "./img/foundation_03";

class Logo extends React.Component{
  render(){
    return(
      <img src={img}/>
    )
  }
}

export default Logo;
```

- [导入文件官方教程](https://github.com/webpack-contrib/file-loader)
