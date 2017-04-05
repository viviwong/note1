---
title: React-Router
---

### 基本用法
使用时首先需要安装 `react-router` 这个包，`Router` 组件本身只是一个容器，真正的路由则需要通过 `Route` 组件定义。

```
import { Router, Route, hashHistory } from 'react-router';

render((
  <Router history={hashHistory}>
    <Route path="/" component={App}/>
  </Router>
), document.getElementById('app'));
```

访问 `/` (例如 localhost:8080/),组件 `App` 就会加载到 `document.getElementById('app')` 这个节点上。
当设置 `history:{hashHistory}` 时，路由的切换由 URL的hash变化决定的，即URL的 `#` 部分发生变化，举个例子:访问 `localhost:8080` 时，实际会看到的是 `localhost:8080/#/`.

##### `Route`组件定义了URL路径与组件的对应关系，可以同时使用多个 `Route` 组件

```
<Router history={hashHistory}>
  <Route path="/" component={App}/>
  <Route path="/repos" component={Repos}/>
  <Route path="/about" component={About}/>
</Router>
```

上面代码中，用户访问 `/repos`（比如 http://localhost:8080/#/repos）时，加载 `Repos` 组件；访问 `/about`（http://localhost:8080/#/about）时，加载 `About` 组件。

### 路由的嵌套

`Route` 组件还可以嵌套。

```
<Router history={hashHistory}>
  <Route path="/" component={App}>
    <Route path="/repos" component={Repos}/>
    <Route path="/about" component={About}/>
  </Route>
</Router>
```

上面的嵌套 ,当用户访问 `/repos` 时，会首先加载 `App` 组件然后在它的内部再加载 `Repos`组件，实际上就变成了下面这种关系.

```html
<App>
  <Repos/>
</App>
```

需要注意的是 `App` 组件需要写成下面的样子

```
export default React.createClass({
  render() {
    return <div>
      {this.props.children}
    </div>
  }
})
```
上面的代码， `App` 组件的 `this.props.children` 属性就代表子组件，正常的组件嵌套不也是需要这样定义吗，这样就比较好理解了。

##### 另一种定义嵌套路由的方式
可以不写在 `Router` 组件里面，单独传入 `Router` 组件的 `router` 属性

```
let routes = <Route path="/" component={App}>
                <Route path="/repos" component={Repos}/>
                <Route path="/about" component={About}/>
             </Route>;

<Router routes={routes} history={browserHistory}/>
```

### path属性
`Route` 组件的 `path` 属性指定路由的匹配规则，这个属性是可以省略的，但是只能是父级省略(父级省不省略都会加载父级，没什么意义)。同级省略也不会加载。

```
<Router history={browserHistory}>
  <Route path="/" component={Home}>
    <Route path="/about" component={About} />
    <Route path="/blog" component={Blog} />
  </Route>
</Router>
```
父级省略了 `path="/"` 效果一样，访问`/about`路由时，父级同样会加载，但是如果 `path="/blog"` 省略，当访问 `/`时 ，Blog组件不会加载。

##### `path` 属性可以使用通配符

- `:name` 匹配URL的一个部分，直到遇到 `/` `?` `#` 为止，这个路径参数可以通过 `this.props.params.name` 取出，举个例子

```
<Router history={browserHistory}>
    <Route path="/" component={Home}>
      <Route path="/about" component={About} />
      <Route path="/blog/:name" component={Blog} />
    </Route>
</Router>

//Home.js
render(){
    return(
      <div style={{border:"5px solid green"}}>
        <ul role="nav">
          <li><Link to="/about">About</Link></li>
          <li><Link to="/blog/yewenxiang">Blog</Link></li>
        </ul>
        我是Home组件.
        {this.props.children}
      </div>
    )
}
//Blog.js
render(){
    return(
      <div style={{border:"5px solid red"}}>
        我是Blog组件.
        <h3>{this.props.params.name}</h3>
      </div>
    )
}
```
这样在 `Blog`组件中就可以通过 `this.props.params.name` 拿到 `yewenxiang` 这个值了。
在 `Router` 定义的是匹配的规则，在 `Link` 中定义的就是传入 `Blog`组件的值了。

- `()` 表示URL的这个部分是可选的

```
<Route path="/hello(/:name)">
// 匹配 /hello
// 匹配 /hello/yewenxiang
```

- `*`匹配任意字符，直到模式里面的下一个字符为止。匹配方式是`非贪婪模式`。

```
<Route path="/files/*">
// 匹配 /files/
// 匹配 /files/a
// 匹配 /files/a/b

<Route path="/files/*.*">
// 匹配 /files/hello.jpg
// 匹配 /files/hello.html
```

- `**` 匹配任意字符，直到下一个`/`、`?`、`#` 为止。匹配方式是`贪婪模式`。

```
<Route path="/**/*.jpg">
// 匹配 /files/hello.jpg
// 匹配 /files/path/to/file.jpg
```

##### 什么是正则表达式的贪婪与非贪婪匹配

- 贪婪匹配:正则表达式一般趋向于最大长度匹配，也就是所谓的贪婪匹配。
- 非贪婪匹配：就是匹配到结果就好。

##### 路由的匹配规则

- path属性也可以使用相对路径（不以/开头），匹配时就会相对于父组件的路径。
- 路由匹配规则是从上到下执行，一旦发现匹配，就不再其余的规则了。

```
<Router>
  <Route path="/:userName/:id" component={UserPage}/>
  <Route path="/about/me" component={About}/>
</Router>
```

上面代码中，用户访问`/about/me`时，不会触发第二个路由规则，因为它会匹配`/:userName/:id`这个规则。因此，带参数的路径一般要写在路由规则的底部。

### IndexRoute 指定根路由的默认子组件

```
<Router history={browserHistory}>
    <Route path="/" component={Home}>
      <Route path="/about" component={About} />
      <Route path="/blog/:name" component={Blog} />
    </Route>
</Router>
```
上面的代码，当访问根路径 `/` 时候没有加载任何的子组件，这是正常的现象，但是如果我们需要访问 `/` 加载一个默认的组件如何去解决呢。
`IndexRoute` 就是来解决这个问题的。

```
<Router history={browserHistory}>
    <Route path="/" component={Home}>
      <IndexRoute component={In} />
      <Route path="/about" component={About} />
      <Route path="/blog/:name" component={Blog} />
    </Route>
</Router>

// Home.js
{this.props.children || <In />}
```
这样定义后，当访问 `/`(根路由),时就会默认加载 `In` 这个组件了,加载的组件结构如下:

```
<Home>
  <In />
</Home>
```
可以把`IndexRoute`想象成某个路径的`index.html`。
这种组件结构就很清晰了：`Home`只包含下级组件的共有元素，本身的展示内容则由`In`组件定义。这样有利于代码分离，也有利于使用React Router提供的各种API。

### 路由嵌套和组件嵌套的一个误区

```
<Router history={browserHistory}>
    <Route path="/" component={Home}>
      <Route path="/about" component={About} />
      <Route path="/blog/:name" component={Blog} />
    </Route>
</Router>
```
我在 `Home` 组件中定义了 `{this.props.children}`,访问 `/` 时，以为 `About` 和 `Blog`组件也会显示，其实不会，拿上面的代码来说。 `/` 只会加载 `Home`组件，而不会加载 `About`,`Blog`。结构如下

```
<Home>
</Home>
```

我错误的想成了

```
//这是我错误的想法
<Home>
  <About />
  <Blog />
</Home>
```

### Redirect 路由的跳转组件
`<Redirect>`组件用于路由的跳转，即用户访问一个路由，会自动跳转到另一个路由。

```
<Router history={browserHistory}>
    <Route path="/" component={Home}>
      <IndexRoute component={In} />
      <Route path="/about" component={About}>
        <Route path="/me" component={Me}>
          <Redirect from="/messages" to="/about" />
        </Route>
      </Route>
      <Route path="/blog/:name" component={Blog} />
    </Route>
</Router>
```
访问 `/messages` 时会跳转到 `/about`,结构如下

```
<Home>
  <About />
</Home>
```
注意 `<IndexRoute component={In} />` 这个组件没加载,只有访问 `/`时会加载。

### IndexRedirect 组件
用于访问根路由的时候，将用户重定向到某个子组件。

```
<Router history={browserHistory}>
    <Route path="/" component={Home}>
      <IndexRedirect to="/me" />
      <Route path="/about" component={About}>
        <Route path="/me" component={Me}>
        </Route>
      </Route>
      <Route path="/blog/:name" component={Blog} />
    </Route>
</Router>
```
上面的代码中，用户访问`/`根目录时，将重定向到子组件 `/me`，重定向后页面中渲染的组件结构如下:

```
<Home>
  <About>
    <Me />
  </About>
</Home>
```

### Link
`Link`组件用于取代`<a>`元素，生成一个链接，允许用户点击后跳转到另一个路由。它基本上就是`<a>`元素的React 版本，可以接收`Router`的状态。

```
render() {
  return <div>
    <ul role="nav">
      <li><Link to="/about">About</Link></li>
      <li><Link to="/repos">Repos</Link></li>
    </ul>
  </div>
}
```

如果需要当前的路由链接与其他路由链接有不同的样式，来突出当前的位置，可以使用 `Link` 组件的 `activeStyle` 属性

```
<Link to="/about" activeStyle={{color: 'red'}}>About</Link>
<Link to="/repos" activeStyle={{color: 'red'}}>Repos</Link>
```
上面的代码，当前的链接会显示红色，这种定义方法可以想象成HTML里面定义行内的样式，另外一种更好的方式是，使用`activeClassName` 指定当前路由链接的 `Class`，也就是当前页面的路由链接才会添加上这个类，可以想象成外联样式。

```
<Link to="/about" activeClassName="active">About</Link>
<Link to="/repos" activeClassName="active">Repos</Link>
```

##### 在Router组件之外，导航到路由页面，可以使用浏览器的History API，像下面这样写。

```
import { browserHistory } from 'react-router';
browserHistory.push('/some/path');
```

### IndexLink
如果链接到根路由`/`，不要使用`Link`组件，而要使用`IndexLink`组件。
这是因为对于根路由来说，`activeStyle`和`activeClassName`会失效，或者说总是生效一直会显示样式，因为`/`会匹配任何子路由。而`IndexLink`组件会使用路径的精确匹配。

```
<IndexLink to="/" activeClassName="active">Home</IndexLink>
```
另一种方式使用`Link`组件的`onlyActiveOnIndex`属性，也能达到同样效果。

```
<Link to="/" activeClassName="active" onlyActiveOnIndex={true}>Home</Link>
```
实际上，`IndexLink`就是对`Link`组件的`onlyActiveOnIndex`属性的包装。

### histroy 属性
`Router`组件的`history`属性，用来监听浏览器地址栏的变化，并将URL解析成一个地址对象，供 React Router 匹配。
`history`属性，一共可以设置三种值:

- browserHistory 路由将通过URL的hash部分（#）切换，URL的形式类似example.com/#/some/path。
- hashHistory 浏览器的路由就不再通过Hash完成了，而显示正常的路径example.com/some/path，背后调用的是浏览器的History API。但是，这种情况需要对服务器改造。否则用户直接向服务器请求某个子路由，会显示网页找不到的404错误。如果开发服务器使用的是webpack-dev-server，加上--history-api-fallback参数就可以了。
- createMemoryHistory 主要用于服务器渲染。它创建一个内存中的history对象，不与浏览器URL互动。`const history = createMemoryHistory(location)`

[官方文档](https://github.com/ReactTraining/react-router/blob/master/docs/guides/Histories.md#configuring-your-server)

### 表单处理
`Link`组件用于正常的用户点击跳转，但是有时还需要表单跳转、点击按钮跳转等操作。这些情况怎么跟React Router对接呢？
下面是一个表单。

```
<form onSubmit={this.handleSubmit}>
  <input type="text" placeholder="userName"/>
  <input type="text" placeholder="repo"/>
  <button type="submit">Go</button>
</form>
```
第一种方法是使用`browserHistory.push`

```
import { browserHistory } from 'react-router'

// ...
  handleSubmit(event) {
    event.preventDefault()
    const userName = event.target.elements[0].value
    const repo = event.target.elements[1].value
    const path = `/repos/${userName}/${repo}`
    browserHistory.push(path)
  },
```
第二种方法是使用`context`对象

```
export default React.createClass({

  // ask for `router` from context
  contextTypes: {
    router: React.PropTypes.object
  },

  handleSubmit(event) {
    // ...
    this.context.router.push(path)
  },
})
```

### 路由的钩子
每个路由都有Enter和Leave钩子，用户进入或离开该路由时触发。

```
<Route path="about" component={About} />
＜Route path="inbox" component={Inbox}>
  ＜Redirect from="messages/:id" to="/messages/:id" />
</Route>
```

上面的代码中，如果用户离开`/messages/:id`，进入`/about`时，会依次触发以下的钩子。

```
/messages/:id 的 onLeave
/inbox 的 onLeave
/about 的 onEnter
```

下面是一个例子，使用`onEnter`钩子替代`<Redirect>`组件。

```
<Route path="inbox" component={Inbox}>
  <Route
    path="messages/:id"
    onEnter={
      ({params}, replace) => replace(`/messages/${params.id}`)
    }
  />
</Route>
```

`onEnter`钩子还可以用来做认证。

```
const requireAuth = (nextState, replace) => {
    if (!auth.isAdmin()) {
        // Redirect to Home page if not an Admin
        replace({ pathname: '/' })
    }
}
export const AdminRoutes = () => {
  return (
     <Route path="/admin" component={Admin} onEnter={requireAuth} />
  )
}
```

下面是一个高级应用，当用户离开一个路径的时候，跳出一个提示框，要求用户确认是否离开。

```
const Home = withRouter(
  React.createClass({
    componentDidMount() {
      this.props.router.setRouteLeaveHook(
        this.props.route,
        this.routerWillLeave
      )
    },

    routerWillLeave(nextLocation) {
      // 返回 false 会继续停留当前页面，
      // 否则，返回一个字符串，会显示给用户，让其自己决定
      if (!this.state.isSaved)
        return '确认要离开？';
    },
  })
)

```

上面代码中，`setRouteLeaveHook`方法为`Leave`钩子指定`routerWillLeave`函数。该方法如果返回`false`，将阻止路由的切换，否则就返回一个字符串，提示用户决定是否要切换。


--[整理自阮一峰的博客](http://www.ruanyifeng.com/blog/2016/05/react_router.html?utm_source=tool.lu)

--[官方demo](https://github.com/reactjs/react-router-tutorial/tree/master/lessons)
