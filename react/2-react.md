---
title: React-state
---

### React状态的基本应用
React 把用户界面当作简单状态机。把用户界面想像成拥有不同状态然后渲染这些状态，可以轻松让用户界面和数据保持一致。
React 里，只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不要操作 DOM）。React 来决定如何最高效地更新 DOM。

```
import React from "react";

class App extends React.Component{
  constructor(){
    super();
    <!-- 定义属性前必须加上super() -->
    this.state = {
      num :0,
      num2 :false
    }
    <!-- 把需要改变的属性集合(一个对象)放在 this.state属性中 -->
  }
  handleAdd(){   //自定义的功能函数
    this.setState({num:this.state.num+1});
    //当用户点击组件，导致状态变化，this.setState 方法就修改状态值，每次修改以后，自动调用 this.render 方法，再次渲染组件。

  }
  handleCut(){
    this.setState({num:this.state.num-1});
  }
  show(){
    this.setState({num2:!this.state.num2});
  }
  render(){
    return (
      <div>
        数字：{this.state.num}<br/>
        <button onClick={this.handleAdd.bind(this)}>+1</button>
        <button onClick={this.handleCut.bind(this)}>-1</button>
        <button onClick={this.show.bind(this)}>{this.state.num2 ? "显示" : "隐藏"}</button>
        <p style={{display: this.state.num2? 'block' : 'none'}}>我是测试显示隐藏的文字</p>
      </div>
    )
  }
};

export default App;
```
