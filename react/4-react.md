---
title: React组件之间通信
---

### 组件之间如何通信
一个组件如果无法做到通信是无法得到很好的复用的，因为有时候组件复用我们希望组件的UI会有点点区别,下来实现一个简单的通信来说明。

```
//Btn.js
import React from 'react';
class Btn extends React.Component{
  render(){
    let styles = {
      padding:this.props.padd,
      fontSize:"18px",
      backgroundColor:this.props.bg
    }
    console.log(this.props);
    return (
      <button style={styles}>{this.props.title}</button>
    )
  }
}
export default Btn;
```

```
//App.js
import React from 'react';
import Btn from "./Btn";
class App extends React.Component{
  render(){
    return (
      <div>
        <Btn title="叶文翔" bg='yellow' padd='20px 30px'/>
        <Btn title="向往" bg='#777'/>
        <Btn title="田冬雪"/>
      </div>
    )
  }
}
export default App;

```

上面 `Btn.js组件` 导出到 `App.js组件` 中，那么App组件就是Btn组件的父组件，我们在父组件App自定义标签中插入我们自定义的属性，然后在 `Btn.js` 文件的 `render()` 函数中打印 `this.props` 发现得到下图:

![this.props-img](./4-react-img/1.png)

说明 `this.props` 可以拿到我们自定义属性的一个对象，然后在我们的 `Btn.js` 文件中，把需要自定义的属性全部用 `this.props.自定义属性名` 替换掉就行了，能够极大的提高组件的复用率。

### 组件中的默认属性
上面有个问题，如果没有传入相应的属性和属性值，组件可能会得不到正确的显示，这时候我们需要定义组件的默认属性了。定义方法：

```
//Btn.js
Btn.defaultProps = {
  title:'defaultTitle',
  bg:'#00bcd4',
  padd:'10px 20px'
}
```
这样当我们忘记传对应的属性的时候，组件就会显示默认的属性。

### 组件通信更好的方式
如果我们定义的属性很多，或者从后台传过来的属性来渲染，就会使用下面的方式

```
//Card.js
import React from 'react';
class Card extends React.Component{
  render(){
    return (
      <div className="card">
        <div className="card-index">{this.props.index}</div>
        <div className="card-desc">
          <h3>{this.props.title}</h3>
          <p>{this.props.data}</p>
        </div>
      </div>
    )
  }
}
Card.defaultProps = {
  index:1,
  title:"默认的一个标题",
  data:"2017.2.20"
}
export default Card;
```

```
//App.js
import React from 'react';
import Card from "./Card";
import "./main";
let arr = [
  {index:1,title:"标题一",date:"2017.2.22"},
  {index:2,title:"标题二",date:"2017.2.23"},
  {index:3,title:"标题三",date:"2017.2.24"},
  {index:4,title:"标题四",date:"2017.2.25"},
  {index:5,title:"标题五",date:"2017.2.26"}
]
class App extends React.Component{
  constructor(){
    super();
    this.state={
      date:arr
    }
  }
  render(){
    return (
      <div>
        {
          this.state.date.map( item => <Card key={Math.random()} title={item.title} index={item.index} date={item.date} />)
        }

        {/* 或者使用下面的方式 */}
        {/* {arr.map(
          item => <Card key={Math.random()} {...item} />
        )} */}
      </div>
    )
  }
}
export default App;
```
- 组件属性的数组最好交给 `state` 属性来统一管理。
- `{...item}` 这种写法更加的简便，`arr` 数组中每个对象都被当做了参数来传递，使用 `.map()` 方法，把属性插入了 `<Card />` 这个组件中。Spread 扩展操作符: `{...item}` ,直接把对象铺开放在了 `<Card />` 组件中。

### 子组件设置属性验证

通过 `Btn.propTypes = {}` 来设置属性的格式，当父组件传的值不符时，浏览器会报错。

```
//Btn.js
Btn.propTypes = {
  title:React.PropTypes.string,
  bg:React.PropTypes.string,
  padd:React.PropTypes.string
}
```

### 子组件传递函数和参数

```
//父组件 App.js
class App extends React.Component{
  constructor(){
    super();
    this.state={
      num:0
    }
  }
  addNum(num2){
    this.setState({num:this.state.num + num2});
  }
  render(){
    return (
      <div>
        数值是：{this.state.num} <br />
        <Btn title="减1" bg="#666" padd='20px 30px' addNum = {this.addNum.bind(this,-1)}/>
        <Btn title="加1" bg="pink" padd='20px 30px' addNum = {this.addNum.bind(this,1)}/>
      </div>
    )
  }
}
```

```
//子组件 Btn.js
class Btn extends React.Component{
  // handleClick(){
  //   this.props.addNum();
  // }
  render(){
    let styles = {
      padding:this.props.padd,
      fontSize:"18px",
      backgroundColor:this.props.bg
    }
    console.log(this.props);
    return (
      // <button style={styles} onClick = {this.handleClick.bind(this)}>{this.props.title}</button>
      // 或者
      <button style={styles} onClick = {() => this.props.addNum()}>{this.props.title}</button>
    )
  }
}
```

##### 子组件设置函数的验证

```
//Btn.js
Btn.propTypes = {
  addNum:React.PropTypes.func.isRequired
  //必须传一个函数，否则报错。
}

```

### 如何在组件中嵌套组件(this.props.children)

`this.props.children` 表示组件所有的子节点，也就是在父组件中包含的标签。
它有三种可能的值：

- 如果当前组件没有子节点，它就是 `undefined`
- 如果有一个子节点，数据类型是 `object`
- 如果有多个子节点，数据类型是 `array`

```
//Children.js
import React from 'react';
import ReactDOM from "react-dom";
import Son from "./Son";
import Test from "./Test";

class App extends React.Component {
    render() {
        return (
            <div>
                aaa
                <Son>
                  bbb
                    <Test></Test>
                    <Test></Test>
                    <Test></Test>
                    <Test></Test>
                    <Test></Test>
                </Son>
            </div>
        )
    }
}
export default App;
```

```
//Son.js
import React from 'react';
import ReactDOM from "react-dom";
class Son extends React.Component{
  componentDidMount(){

  }
  render(){
    console.log(this.props.children); //打印出 ["bbb", Object, Object, Object, Object, Object]
    return(
      <div>
        {this.props.children}
      </div>
    )
  }
}
export default Son;
```

```
//Test.js
import React from 'react';
import ReactDOM from "react-dom";
class Test extends React.Component{
  render(){
    console.log(this.props.children); //输出5个 undefined
    return(
      <div>
        我是Test组件
      </div>
    )
  }
}

export default Test;

```

React 提供一个工具方法 `React.Children` 来处理 `this.props.children` 。我们可以用 `React.Children.map` 来遍历子节点，而不用担心 `this.props.children` 的数据类型是 `undefined` 还是 `object`

```
return (
      <ol>
      {
        React.Children.map(this.props.children, function (child) {
          return <li>{child}</li>;
        })
      }
      </ol>
    );
```
