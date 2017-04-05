---
title: React表单
---

### 表单
表单不同于其他 HTML 元素，因为它要响应用户的交互，显示不同的状态，所以在 React 里面会有点特殊。

### 状态属性
表单元素有这么几种状态属性：

- `value` 对应 `<input>` 和 `<textarea>`
- `checked` 对应 `checkbox` 和 `radio` 的 `<input>` 所有
- `selected` 对应 `<option>` 所有

在 HTML 中 `<textarea>` 的值可以由子节点（文本）赋值，但是在 React 中，要用 `value` 来设置。
表单元素包含以上任意一种状态属性都支持 `onChange` 事件监听状态值的更改。
针对这些状态属性不同的处理策略，表单元素在 React 里面有两种表现形式。

### 受控组件
设置了 `value` 的` <input>` 是一个受限组件。 对于受限的 `<input>`，渲染出来的 HTML 元素始终保持 value 属性的值。例如：

```
render: function() {
  return <input type="text" value="Hello!" />;
}
```

上面的代码将渲染出一个值为 Hello! 的 input 元素。用户在渲染出来的元素里输入任何值都不起作用，因为 React 已经赋值为 Hello!。如果想响应更新用户输入的值，就得使用 onChange 事件：

```
class From extends React.Component{
  constructor(){
    super();
    this.state = {
      inputValue:""
    }
  }
  handle(e) {
        e.preventDefault();
        this.refs.form.reset();
        //点击提交按钮也会被重置表单
    }
  render(){
    return (
            <div id="text">
                <form action="" method="GET" onSubmit={this.handle.bind(this)} ref="form" id="ye">
                    <input type="text" value={this.state.inputValue} onChange={(event)=>
                      this.setState({inputValue:event.target.inputValue})}> </input>
                </form>
            </div>
      )
  }
}
```

React 将用户输入的值更新到 `<input>` 组件的 value 属性,这样页面会重新被渲染显示出输入的值。

### 非受控组件
没有设置 value(或者设为 null) 的 `<input>` 组件是一个不受限组件，对于不受限的 `<input>` 组件，渲染出来的元素直接反应用户输入

```
render: function() {
    return <input type="text" />;
  }
```

上面的代码将渲染出一个空值的输入框，用户输入将立即反应到元素上。和受限元素一样，使用 onChange 事件可以监听值的变化。
如果想给组件设置一个非空的初始值，可以使用 defaultValue 属性。例如：

```
ender: function() {
    return <input type="text" defaultValue="Hello!" />;
  }
```
上面的代码渲染出来的元素和受限组件一样有一个初始值，但这个值用户可以改变并会反应到界面上。

- `radio` 、 `checkbox` 的 `<input>` 支持 `defaultChecked`属性
- `<select>` 支持 `defaultValue` 属性

```
class Form extends React.Component {
    constructor() {
        super();
        this.state = {
            inputValue: "",
            textareaValue: "我是文本域",
            selectValue:"",
            radio:"woman"
        }
    }
    handle(e) {
        e.preventDefault();
        this.refs.form.reset();
    }
    render() {
        return (
            <div id="text">
                <form action="" method="GET" onSubmit={this.handle.bind(this)} ref="form">
                    <input type="text" value={this.state.inputValue} onChange={(e)=>
                    this.setState({inputValue:e.target.value})}></input>


                    <textarea value={this.state.textareaValue} onChange={(e)=>
                    this.setState({textareaValue:e.target.value})}/>

                    <input type="radio" value="man" name="sex1" defaultChecked/>男
                    <input type="radio" value="woman" name="sex2" checked={this.state.radio==="woman"} onChange={(e)=>
                    this.setState({radio:e.target.value})}/>女

                    <button type="submit">提交</button>
                    <button type="reset">重置</button>
                </form>
            </div>
        )
    }
}
```

### 为什么使用受控组件？

```
<input type="text" name="" value="ye" />
```
在React中使用表单组件时。上面的代码在HTML 中将渲染初始值为 `ye` 的输入框。用户改变输入框的值时，节点的 `value` 属性（property）将随之变化，但是 `node.getAttribute('value')` 还是会返回初始设置的值 `ye`,与 HTML 不同，React 组件必须在任何时间点描绘视图的状态，而不仅仅是在初始化时。

### 为什么 `<textarea>` 使用` value` 属性？
在 HTML 中， `<textarea> `的值通常使用子节点设置：

```html
<!-- 反例：在 React 中不要这样使用！ -->
  <textarea name="description">This is the description.</textarea>
```

对 HTML 而言，让开发者设置多行的值很容易。但是，React 是 JavaScript，没有字符限制，可以使用 \n 实现换行。简言之，React 已经有 value、defaultValue 属性，</textarea> 组件的子节点扮演什么角色就有点模棱两可了。基于此， 设置 `<textarea>` 值时不应该使用子节点：

```
<textarea name="description" value="This is a description." />
```

### 为什么 `<select>` 使用 `value` 属性
HTML 中 `<select>` 通常使用 `<option>` 的 selected 属性设置选中状态；React 为了更方面的控制组件，采用以下方式代替：

```
<select onChange={(e)=>{
  this.setState({selectValue: e.target.value});
}} defaultValue="2">
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
  <option value="4">4</option>
</select>
```

>注意：给 value 属性传递一个数组，可以选中多个选项：`<select multiple={true} value={['B', 'C']}>`。
>

### 复选框如何实现受控组件？

```
class Form extends React.Component {
    constructor() {
        super();
        this.state = {
            checkboxValue:["apple"]
        }
    }
    handleCheckbox(e){
      let ckValue = this.state.checkboxValue;
      let clickValue = e.target.value;
      let index = ckValue.findIndex(num=>num===clickValue);
      // 或者使用 indexOf()
      // let index = ckValue.indexOf(clickValue );

      if(index === -1){
        ckValue.push(clickValue);
      }else{
        ckValue.splice(index,1);
      }
      this.setState({checkboxValue:ckValue});
    }
    render() {
        return (
                <div>
                  <input type="checkbox" value="apple" name="fruits" onChange={this.handleCheckbox.bind(this)} defaultChecked/>苹果
                  <input type="checkbox" value="banana" name="fruits" onChange={this.handleCheckbox.bind(this)}/>香蕉
                  <input type="checkbox" value="pear" name="fruits" onChange={this.handleCheckbox.bind(this)}/>梨子
                </div>
        )
    }
}
```

### 总结

- 表单定义 `value` 属性和值之后，用户无法在表单输入值或者点击 `radio` 。
- 受控组件 定义了 `onchange()` 方法，value的值交给state来控制。
- 非受空组件 定义了 `defaultValue` 不受state管理数据 不能跟新value的值，但是用户可以在表单中输入数据。
