---
title: React-demo
---

### 一个简单的抽奖

```
import React from "react";
let data = ["青菜",'萝卜','西红柿','大豆'];
class EatWhat extends React.Component {
  constructor() {
    super();
    this.state = {
      start:false,
      data,
      text:""
    }
  }
  select(){
    let result = this.state.data[Math.floor(Math.random()*this.state.data.length)];
    this.setState({text:result});
  }
  handelClick(){
    if(this.state.start){
      clearInterval(this.interval);
      this.setState({start:false});
    }else{
      this.interval = setInterval(() => this.select(),100);
      this.setState({start:true});
    }
  }
  render(){
    return (
      <div>
        <button onClick={this.handelClick.bind(this)}>{this.state.start?"停止":"开始"}</button>
        <span>{this.state.text}</span>
      </div>
    )
  }
}
export default EatWhat;
```

### map函数妙用

```
import React from "react";

let data = [
  "第一条消息",
  "第二条消息",
  "第三条消息",
];
class App extends React.Component{
  constructor(){
    super();
    this.state = {
      text:data
    }
  }
  render(){
    let content = this.state.text.map(
      item => <div key={Math.random()}><p>今天的消息:{item}</p><button>删除</button></div>
    )
    return (
      <div>
        {content}
      </div>
    )
  }
};
export default App;
```

>key是React底层做diff计算用的，数据需要一个key，跟新数据时React根据key来做出精确的数据更改，不然整个数组中一个数据更改，其他项没改变数据也要刷新，浪费性能。 可以使用随机数或者map中的index参数作为key
>

### 选项卡切换

```
import React from "react";
import Tab1 from "./Tab1";

class SelectBar extends React.Component{
  constructor(){
    super();
    this.state = {show:0}
  }
  handleClick(num){
    this.setState({show:num});
  }
  render(){
    return (
    <div>
      <button onClick={this.handleClick.bind(this,1)}>选项卡一</button>
      <button onClick={this.handleClick.bind(this,2)}>选项卡二</button>
      <button onClick={this.handleClick.bind(this,3)}>选项卡三</button>
      {this.state.show === 1 ? <Tab1 /> :
          this.state.show === 2 ? "这是选项卡二" : "这是选项卡三"
      }
    </div>
    )
  }
}
export default SelectBar;
```

>这里的三目运算符嵌套表示 if(){}else if(){} else{}
>

### 一个上拉和下拉面板demo

```
import React from "react";
class Trans extends React.Component{
  constructor(){
    super();
    this.state = {
      show:true
    }
  }
  handelClick(){
    this.setState({show:!this.state.show})
  }
  render(){
    let styles = {
      top:this.state.show? "30%" : "5%"
    }
    let heights = {
      height:this.state.show? "100px" : "0"
    }
    return (
      <div className="container" style={styles}>
        <a href="javascript:" onClick={this.handelClick.bind(this)} className ="btn">{this.state.show?"向上":"向下"}</a>
        <div className="content" style={heights}>
          <p>哈哈哈哈哈啊啊啊啊啊啊啊啊啊啊啊啊</p>
          <p>哈哈哈哈哈啊啊啊啊啊啊啊啊啊啊啊啊</p>
          <p>哈哈哈哈哈啊啊啊啊啊啊啊啊啊啊啊啊</p>
        </div>
      </div>
    )
  }
}
export default Trans;
```
