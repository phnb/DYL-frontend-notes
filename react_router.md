# React Router 参考文档
React-Router 是 React 生态的基础路由库，它通过管理 URL，实现组件的切换和状态的变化。

## 前端路由
#### 1.1 URL的hash
- URL的hash就是锚点，本质上是改变window.location的href属性；
- 我们可以直接赋值location.hash来改变href，但是页面不发生刷新；

``` HTML
    <body>
        <div id="app">
            <a href="#/home">home</a>
            <a href="#/about">about</a>
            <div class="router-view"></div>
        </div>
    </body>
    <script>
        const routerView = document.getElementsByClassName("router-view")[0];
        //监听URL的改变
        window.addEventListener("hashchange",()=>{
            switch(location.hash){
                case "#/home":
                    routerView.innerHTML = "home";
                    break;
                case "#/about":
                    routerView.innerHTML = "about";
                    break;
                default:
                    routerView.innerHTML = "";
            }
        })
    </script>
```
<br>

#### 1.2 HTML5: history
- 有六种模式可以改变URL而不刷新页面

| 方法 | 解释 |
| --  | -- |
history.back() | 在浏览器历史记录里前往上一页, 用户可点击浏览器左上角的返回按钮   等价于 history.go(-1)
history.forward() | 在浏览器历史记录里前往下一页，用户可点击浏览器左上角的前进   等价于 history.go(1)
history.go() | 通过当前页面的相对位置从浏览器历史记录( 会话记录 )加载页面。比如：参数为-1的时候为上一页，参数为1的时候为下一页.
history.pushState() | 按指定的名称和URL（如果提供该参数）将数据push进会话历史栈，数据被DOM进行不透明处理；你可以指定任何可以被序列化的javascript对象
history.replaceState() | 按指定的数据，名称和URL(如果提供该参数)，更新历史栈上最新的入口。这个数据被DOM 进行了不透明处理。你可以指定任何可以被序列化的javascript对象。

``` HTML
<body>
    <div>
        <div id="app">
            <a href="/home">首页</a>
            <a href="/about">关于</a>

            <div class="router-view"></div>
        </div>
    </div>
    <script>

        const routerView = document.getElementsByClassName("router-view")[0];

        const aEL = document.getElementsByTagName("a");
        for (let i = 0; i < aEL.length; i++) {
            aEL[i].addEventListener("click", e => {
                e.preventDefault();
                const href = aEL[i].getAttribute("href");
                history.pushState({}, "", href);
                urlChange();
            })
        }
        for(let el of aEL){
            el.addEventListener("click",e => {
                e.preventDefault();
                const href = el.getAttribute("href");
                history.pushState({},"",href);
            })
        }

        //执行返回操作时，依然来到urlChange
        window.addEventListener("popstate",urlChange);

        function urlChange() {
            switch (location.pathname) {
                case "/home":
                    routerView.innerHTML = "首页";
                    break;
                case "/about":
                    routerView.innerHTML = "关于";
                    break;
                default:
                    routerView.innerHTML = "";
            }
        }

    </script>
</body>
```
<br>

## Router的基本使用

- <big> BrowserRouter或HashRouter </big>
    
    1. Router中包含了对路径改变的监听，并且会将相应的路径传递给子组件；
    2. BrowserRouter使用history模式；
    3. HashRouter使用hash模式；
    <br>

- <font size = 4> Link和NavLink </font>
    
    1. 通常路径的跳转是使用Link组件，最终会被渲染成a元素；
    2. NavLink是在Link基础上增加了一些样式属性；
    3. to属性：Link中最重要的属性，用于设置跳转到的路径；
    <br>

- <font size = 4> Route </font>
    1. Route用于路径的匹配；
    2. path属性：用于设置匹配到的路径；
    3. component属性：设置匹配到路径后，渲染的组件；
    4. exact：精准匹配，只有精准匹配到完全一致的路径，才会渲染对应的组件；
    <br>


#### 2.1 NavLink
NavLink是Link的特定版本，会在匹配上当前的url的时候给已渲染的元素添加参数

- activeStyle(object)：当元素被选中时，为此元素添加样式
- activeClassName(string)：设置选中样式，默认值为active
- exact(boolean)：是否精准匹配
    Attention：在组件添加exact属性，能够让路由精准匹配到url
    若不添加exact属性的话，在"/about"的组件和"/profile"组件中也会渲染出"/"的组件

```HTML
    <NavLink  exact to="/" activeStyle={{color:"blue"}}>首页</NavLink>
    <NavLink to="/about"  activeStyle={{color:"blue"}}>关于</NavLink>
    <NavLink to="/profile"  activeStyle={{color:"blue"}}>首页</NavLink>
```
<br>

#### 2.2 switch
渲染第一个被location匹配到的并且作为子元素的Route或者Redirect

> <br> 问题：在/about路径匹配到的同时，/:userid也被匹配到，并且最后一个NoMatch组件总是被匹配到，这该如何解决？
<br>

```HTML
    <Route exact path="/" exact component={home} />
    <Route path="/about" component={about} />
    <Route path="/profile" component={profile} />
    <Route path="/:id" component={user} />
    <Route path="/user" component={noMatch} />
```

> <br>答：这个时候可以用Switch组件将所有的Route组件进行包裹。
<br>

```HTML
    <Switch>
        <Route exact path="/" exact component={home} />
        <Route path="/about" component={about} />
        <Route path="/profile" component={profile} />
        <Route path="/:id" component={user} />
        <Route path="/user" component={noMatch} />
    </Switch>
```
<br>

#### 2.3 Redirect
Redirect可用于路由的重定向，它会执行跳转到对应的to路径中

``` Javascript
import React, { PureComponent } from 'react'
import { Redirect } from 'react-router-dom';

export default class user extends PureComponent {
    constructor(props){
        super(props);
        this.state ={
            isLogin: true
        }
    }

    render() {
        return this.state.isLogin ? (
            <div>
                <h2>user</h2>
                <h2>用户名：boge</h2>
            </div>
        ) : <Redirect to="/login" />
    }
}
```

[英文文档](https://reactrouter.com/web/guides/quick-start)
[中文文档](https://react-guide.github.io/react-router-cn/docs/API.html)