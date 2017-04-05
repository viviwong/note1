---
title: ref如何去使用。
---

### ref的作用
获取DOM后可以方便结合现有非 react 类库的使用，通过 ref/refs 可以取得组件实例，进而取得原生节点，不过尽量通过 state/props 更新组件，不要使用该功能去更新组件的DOM

- 用来调用子组件里面的方法。
- 用来获取页面的节点

### ref的使用方式
三种方法都可以获取DOM

- ReactDOM.findDOMNode(this.refs.xxx);
- this.refs.xxx(官方不推荐)

```js
import React from "react";
import ReactDOM from "react-dom";

class HandleDOMComponent extends React.Component {
  componentDidMount(){
    // 两种方式都可以获取到元素
    let ele = ReactDOM.findDOMNode(this.refs.content);
    let ele2 = this.refs.content;
    // 如果想用 jquery，那么这是个好时机
    console.log( ele );
    console.log( ele.innerHTML );
    console.log( ele2.innerHTML );

  }
  render(){
    return (
      <div>
        <div ref='content'>这是我DOM元素里面的内容</div>
      </div>
    );
  }
}
export default HandleDOMComponent;
```

另外一种官方推荐的方式：

```
class App extends React.Component{
  componentDidMount(){
    console.log(this.newAttr);
  }
  render(){
    return (
      <div>
        <div ref={(jiedian)=>this.newAttr=jiedian}>aaa</div>
      </div>
    )
  }
}
```
ref直接等于一个函数，把节点当做参数传递进去，并且给当前组件绑定  `newAttr` 属性上，后面直接 `this.newAttr` 这样使用就行了。

### 使用ref调用子组件里面的方法

```
//App.js
import React from 'react';
import ReactDOM from "react-dom";
import Test from "./Test";

class App extends React.Component{
  componentDidMount(){
    console.log(this.refs.aaa.getValue());
  }
  render(){
    return (
      <div ref="text">
        hellow
        <Test ref="aaa"/>
      </div>
    )
  }
}
export default App;

```

```
//Test.js
class Test extends React.Component{
  getValue(){
    return this.refs.input.value;
  }
render(){
    return(
      <div>
        <input ref="input" type="text" defaultValue="yewenxiang" />
      </div>
    )
  }
}
```
