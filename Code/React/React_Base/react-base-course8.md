---
title: 08_React Router
date: 2021-11-20 21:22:37
tags:
---

## 今日目标

✔ 了解什么是单页应用。

✔ 掌握 react-router-dom 的使用。

<!-- more -->

## 了解 SPA

-   [网易云音乐](https://music.163.com/)。

-   SPA： `Single Page Application` 单页面应用程序，整个应用中只有一个页面（index.html）。

-   MPA : `Multiple Page Application` 多页面应用程序，整个应用中有很多个页面（\*.html）。

-   优势：页面响应速度快，体验好（无刷新），降低了对服务器的压力。

    a，传统的多页面应用程序，每次请求服务器返回的都是一整个完整的页面。

    b，单页面应用程序只有第一次会加载完整的页面，以后每次请求仅仅获取必要的数据。

-   缺点：不利于 SEO 搜索引擎优化。

    a，因为爬虫只爬取 HTML 页面中的文本内容，不会执行 JS 代码。

    b，可以通过 SSR（服务端渲染 Server Side Rendering）来解决 SEO 问题，即先在服务器端把内容渲染出来，返回给浏览器的就是纯 HTML 内容了。

## 前端路由

现代的前端应用大多都是 SPA，也就是只有一个 HTML 页面的应用程序，因为它的用户体验更好、对服务器的压力更小，所以更受欢迎。

为了有效的使用单个页面来管理原来多页面的功能，前端路由应运而生，功能：让用户从一个视图（页面）导航到另一个视图（页面）。

-   前端路由是一套<font color=e32d40>**映射规则**</font>，是 URL 路径 与组件之间的对应关系。

-   使用 React 路由简单来说就是：配置路径和组件（配对）。

<img src="/resource/images/ifer_router.png"/>

## 模拟 Hash 路由

### 案例目标

📝 需求：点击链接显示对应的组件。

<img src="/resource/images/ifer_router_hash.png" class="highlight2"/>

### 静态结构

`App.js`

```js
import React from 'react'

export default function App() {
    return (
        <>
            <nav className='navbar navbar-expand-lg navbar-light bg-light'>
                <div className='container'>
                    <a className='navbar-brand' href='##'>
                        网易云
                    </a>
                    <button
                        className='navbar-toggler'
                        type='button'
                        data-bs-toggle='collapse'
                        data-bs-target='#navbarSupportedContent'
                        aria-controls='navbarSupportedContent'
                        aria-expanded='false'
                        aria-label='Toggle navigation'
                    >
                        <span className='navbar-toggler-icon'></span>
                    </button>
                    <div className='collapse navbar-collapse' id='navbarSupportedContent'>
                        <ul className='navbar-nav me-auto mb-2 mb-lg-0'>
                            <li className='nav-item'>
                                <a className='nav-link active' aria-current='page' href='##'>
                                    发现音乐
                                </a>
                            </li>
                            <li className='nav-item'>
                                <a className='nav-link' aria-current='page' href='##'>
                                    我的音乐
                                </a>
                            </li>
                            <li className='nav-item'>
                                <a className='nav-link' aria-current='page' href='##'>
                                    关注
                                </a>
                            </li>
                        </ul>
                    </div>
                </div>
            </nav>
            <div className='container mt-3'>
                <div className='card'>
                    <div className='card-body'>发现音乐</div>
                </div>
            </div>
        </>
    )
}
```

### 代码实现

`App.js`

```js
import React, { useEffect, useState } from 'react'
import FindMusic from './pages/FindMusic'
import MyMusic from './pages/MyMusic'
import MyFollow from './pages/MyFollow'

export default function App() {
    const [current, setCurrent] = useState('')
    useEffect(() => {
        const hashChange = () => {
            setCurrent(window.location.hash.slice(1))
        }
        // 一上来也期望调用一次
        hashChange()
        window.addEventListener('hashchange', hashChange)
        return () => {
            window.removeEventListener('hashchange', hashChange)
        }
    }, [])
    return (
        <>
            <nav className='navbar navbar-expand-lg navbar-light bg-light'>
                <div className='container'>
                    <a className='navbar-brand' href='##'>
                        网易云
                    </a>
                    <button
                        className='navbar-toggler'
                        type='button'
                        data-bs-toggle='collapse'
                        data-bs-target='#navbarSupportedContent'
                        aria-controls='navbarSupportedContent'
                        aria-expanded='false'
                        aria-label='Toggle navigation'
                    >
                        <span className='navbar-toggler-icon'></span>
                    </button>
                    <div className='collapse navbar-collapse' id='navbarSupportedContent'>
                        <ul className='navbar-nav me-auto mb-2 mb-lg-0'>
                            <li className='nav-item'>
                                <a className='nav-link active' aria-current='page' href='#/findmusic'>
                                    发现音乐
                                </a>
                            </li>
                            <li className='nav-item'>
                                <a className='nav-link' aria-current='page' href='#/mymusic'>
                                    我的音乐
                                </a>
                            </li>
                            <li className='nav-item'>
                                <a className='nav-link' aria-current='page' href='#/myfollow'>
                                    关注
                                </a>
                            </li>
                        </ul>
                    </div>
                </div>
            </nav>
            <div className='container mt-3'>
                <div className='card'>
                    <div className='card-body'>
                        {current === '/findmusic' && <FindMusic />}
                        {current === '/mymusic' && <MyMusic />}
                        {current === '/myfollow' && <MyFollow />}
                    </div>
                </div>
            </div>
        </>
    )
}
```

## React Router

[官网](https://reactrouter.com/)，[Releases](https://github.com/remix-run/react-router/releases)，[v5](https://v5.reactrouter.com/)

1. 安装。

```bash
yarn add react-router-dom@5.3.0
```

2. `react-router-dom` 这个包提供了三个核心的组件。

```js
import { HashRouter, Route, Link } from 'react-router-dom'
```

3. 使用 `HashRouter` 包裹整个应用，一个项目中只会有一个 Router。

```html
<HashRouter>
    <div className="App">App</div>
</HashRouter>
```

4. 使用 Link 指定导航链接。

```js
<Link className='nav-link active' aria-current='page' to='/findmusic'>
    发现音乐
</Link>
```

5. 使用 `Route` 指定路由规则。

```js
// 在哪里写的 Route，最终匹配到的组件就会渲染到哪里
<Route path='/findmusic' component={FindMusic} />
```

`App.js`，改造上面的案例。

```js
import { HashRouter, Link, Route } from 'react-router-dom'
import FindMusic from './pages/FindMusic'
import MyMusic from './pages/MyMusic'
import MyFollow from './pages/MyFollow'

export default function App() {
    return (
        <HashRouter>
            <nav className='navbar navbar-expand-lg navbar-light bg-light'>
                <div className='container'>
                    <a className='navbar-brand' href='##'>
                        网易云
                    </a>
                    <button
                        className='navbar-toggler'
                        type='button'
                        data-bs-toggle='collapse'
                        data-bs-target='#navbarSupportedContent'
                        aria-controls='navbarSupportedContent'
                        aria-expanded='false'
                        aria-label='Toggle navigation'
                    >
                        <span className='navbar-toggler-icon'></span>
                    </button>
                    <div className='collapse navbar-collapse' id='navbarSupportedContent'>
                        <ul className='navbar-nav me-auto mb-2 mb-lg-0'>
                            <li className='nav-item'>
                                <Link className='nav-link active' aria-current='page' to='/findmusic'>
                                    发现音乐
                                </Link>
                            </li>
                            <li className='nav-item'>
                                <Link className='nav-link' aria-current='page' to='/mymusic'>
                                    我的音乐
                                </Link>
                            </li>
                            <li className='nav-item'>
                                <Link className='nav-link' aria-current='page' to='/myfollow'>
                                    关注
                                </Link>
                            </li>
                        </ul>
                    </div>
                </div>
            </nav>
            <div className='container mt-3'>
                <div className='card'>
                    <div className='card-body'>
                        <Route path='/findmusic' component={FindMusic} />
                        <Route path='/mymusic' component={MyMusic} />
                        <Route path='/myfollow' component={MyFollow} />
                    </div>
                </div>
            </div>
        </HashRouter>
    )
}
```

## Router 详细说明

-   常用有两种 Router：`HashRouter` 和 `BrowserRouter`，用来包裹整个应用，一个 React 应用只需要使用一次。

-   HashRouter：使用 URL 的哈希值实现（`http://localhost:3000/#/first`），是通过监听 window 的 `hashchange` 事件来实现的。

-   BrowserRouter：使用 H5 的 history API 实现（`http://localhost:3000/first`），是通过监听 window 的 `popstate` 事件来实现的。

```js
// 使用时建议通过 as 起一个别名，方便修改
import { HashRouter as Router, Route, Link } from 'react-router-dom'
```

## 路由的执行过程

1. 点击 Link 组件（a 标签），浏览器地址栏中的 url 发生变化。

2. ReactRouter 通过 `hashchange` 或 `popState` 监听到了地址栏 url 的变化。

3. ReactRouter 内部遍历所有 Route 组件，使用路由规则（path）与 pathname（hash）进行匹配。

4. 当路由规则（path）能够匹配地址栏中的 pathname（hash）时，就展示该 Route 对应的组件。

## Link 与 NavLink

-   `Link` 组件最终会渲染成 a 标签，用于指定路由导航。

    a，to 属性，将来会渲染成 a 标签的 href 属性。

    b，`Link` 组件无法实现导航的高亮效果。

-   `NavLink` 组件，一个更特殊的 `Link` 组件，可以用于指定当前导航高亮。

    a，to：用于指定地址，会渲染成 a 标签的 href 属性。

    b，activeClass：用于指定高亮的类名，默认 `active`。

    c，exact：精确匹配，表示必须精确匹配类名才会应用 class，默认是模糊模糊匹配。

## 案例案例 📝

需求：点击当前导航时高亮，期望访问 `/` 时就展示“发现音乐”组件。

`App.js`

1. 去掉默认的 active class。

2. 把 `<Link/>` 组件改成 `<NavLink/>` 组件。

3. 发现音乐的 Link 组件的 to 属性对应的值改成了 `/`，同时出口 Route 组件的 path 属性也改成了 `/`，代码如下。

```js
import React from 'react'
import { HashRouter, Route, NavLink } from 'react-router-dom'
import Home from './pages/Home'
import Search from './pages/Search'
import Comment from './pages/Comment'

export default function App() {
    return (
        <HashRouter>
            <div>
                <ul>
                    <li>
                        <NavLink to='/'>首页</NavLink>
                    </li>
                    <li>
                        <NavLink to='/search'>搜索</NavLink>
                    </li>
                    <li>
                        <NavLink to='/comment'>评论</NavLink>
                    </li>
                </ul>
                <hr />
                <Route path='/' component={Home} />
                <Route path='/search' component={Search} />
                <Route path='/comment' component={Comment} />
            </div>
        </HashRouter>
    )
}
```

问题：点击"我的音乐"和“关注”时，“发现音乐”也一直处于激活的状态。

原因：NavLink 组件 to 属性对应的值如果是 /，则表示，只要是以 / 开头的就会被添加 class，当访问 /mymusic 的时候，发现是以 / 开头的，所以这里也加了 class，/myfollow 同理。

```jsx
<NavLink to='/'>发现音乐</NavLink>
```

解决：添加 exact 属性，表示必须是 / 才会被加 class。

```jsx
<NavLink exact to='/'>
    首页
</NavLink>
```

代码

```js
import { HashRouter, Route, Link, NavLink } from 'react-router-dom'
import FindMusic from './pages/FindMusic'
import MyMusic from './pages/MyMusic'
import MyFollow from './pages/MyFollow'

export default function App() {
    return (
        <HashRouter>
            <nav className='navbar navbar-expand-lg navbar-light bg-light'>
                <div className='container'>
                    {/* 点击这里不需要添加 active class，还是用 Link 就好啦 */}
                    <Link className='navbar-brand' to='/'>
                        网易云
                    </Link>
                    <button
                        className='navbar-toggler'
                        type='button'
                        data-bs-toggle='collapse'
                        data-bs-target='#navbarSupportedContent'
                        aria-controls='navbarSupportedContent'
                        aria-expanded='false'
                        aria-label='Toggle navigation'
                    >
                        <span className='navbar-toggler-icon'></span>
                    </button>
                    <div className='collapse navbar-collapse' id='navbarSupportedContent'>
                        <ul className='navbar-nav me-auto mb-2 mb-lg-0'>
                            <li className='nav-item'>
                                <NavLink className='nav-link' aria-current='page' to='/' exact>
                                    发现音乐
                                </NavLink>
                            </li>
                            <li className='nav-item'>
                                <NavLink className='nav-link' aria-current='page' to='/mymusic'>
                                    我的音乐
                                </NavLink>
                            </li>
                            <li className='nav-item'>
                                <NavLink className='nav-link' aria-current='page' to='/myfollow'>
                                    关注
                                </NavLink>
                            </li>
                        </ul>
                    </div>
                </div>
            </nav>
            <div className='container mt-3'>
                <div className='card'>
                    <div className='card-body'>
                        <Route path='/' component={FindMusic} />
                        <Route path='/mymusic' component={MyMusic} />
                        <Route path='/myfollow' component={MyFollow} />
                    </div>
                </div>
            </div>
        </HashRouter>
    )
}
```

## Route 匹配规则

### 内容

-   Route 组件 path 属性对应的值表示：默认是以此值开头的路径就会被匹配，添加 exact 属性可以开启精确匹配。

-   所以默认情况下，path 为 `/` 能够匹配所有路由组件，因为所有路由组件都是以 `/` 开头的，一般来说，如果路径配置了 `/`，往往都需要配置 exact 属性。

-   如果 path 的路径匹配上了，那么对应的组件就会被 render，否则就会 render null。

-   如果没有指定 path，那么一定会被渲染，例如 `<Route component={NotFound}></Route>`。

### 小结

如何解决上面案例的第二个问题？

## Switch 与 404

-   通常，会把一个个的 `Route` 包裹在一个 `Switch` 组件中，这样只会渲染第一个匹配到的组件，往往是我们期望的。

-   通过 `Switch` 组件配合不带 path 属性的 Route 组件能实现 404 效果，即便不需要实现 404，也可以用 Switch 包裹来提升性能。

```js
<Switch>
    <Route exact path='/' component={FindMusic} />
    <Route path='/mymusic' component={MyMusic} />
    <Route path='/myfollow' component={MyFollow} />
    <Route component={NotFound}></Route>
</Switch>
```

## 路由重定向

```js
import { HashRouter, Route, Link, NavLink, Switch, Redirect } from 'react-router-dom'
import FindMusic from './pages/FindMusic'
import MyMusic from './pages/MyMusic'
import MyFollow from './pages/MyFollow'
import NotFound from './pages/NotFound'

export default function App() {
    return (
        <HashRouter>
            <nav className='navbar navbar-expand-lg navbar-light bg-light'>
                <div className='container'>
                    {/* 点击这里不需要添加 active class，还是用 Link 就好啦 */}
                    <Link className='navbar-brand' to='/'>
                        网易云
                    </Link>
                    <button
                        className='navbar-toggler'
                        type='button'
                        data-bs-toggle='collapse'
                        data-bs-target='#navbarSupportedContent'
                        aria-controls='navbarSupportedContent'
                        aria-expanded='false'
                        aria-label='Toggle navigation'
                    >
                        <span className='navbar-toggler-icon'></span>
                    </button>
                    <div className='collapse navbar-collapse' id='navbarSupportedContent'>
                        <ul className='navbar-nav me-auto mb-2 mb-lg-0'>
                            <li className='nav-item'>
                                {/* 这里的 to 属性值改成了 /findmusic */}
                                {/* exact 属性也就不再需要了，因为另外两个路由（/mymusic 和 /myfollow）也不是以 /findmusic 开头的 */}
                                <NavLink className='nav-link' aria-current='page' to='/findmusic'>
                                    发现音乐
                                </NavLink>
                            </li>
                            <li className='nav-item'>
                                <NavLink className='nav-link' aria-current='page' to='/mymusic'>
                                    我的音乐
                                </NavLink>
                            </li>
                            <li className='nav-item'>
                                <NavLink className='nav-link' aria-current='page' to='/myfollow'>
                                    关注
                                </NavLink>
                            </li>
                        </ul>
                    </div>
                </div>
            </nav>
            <div className='container mt-3'>
                <div className='card'>
                    <div className='card-body'>
                        <Switch>
                            {/* from 表示默认模糊匹配，表示以 '/' 开头就匹配了 Redirect */}
                            {/* 然后跳转到了 /findmusic，发现又是以 / 开头的，死循环... */}
                            {/* <Redirect from='/' to='/findmusic' /> */}
                            {/* 解决 */}
                            <Redirect exact from='/' to='/findmusic' />
                            <Route path='/findmusic' component={FindMusic} />
                            <Route path='/mymusic' component={MyMusic} />
                            <Route path='/myfollow' component={MyFollow} />
                            <Route component={NotFound}></Route>
                        </Switch>
                    </div>
                </div>
            </div>
        </HashRouter>
    )
}
```

## 嵌套路由的配置

<img src="/resource/images/ifer_qiantao.png" class="highlight2"/>

📝 需求：在发现音乐里面再展示推荐、排行榜。

### 静态结构

`pages/FindMusic/index.js`

```js
import React from 'react'
import './index.scss'

export default function FindMusic() {
    return (
        <div className='findmusic d-flex'>
            <ul className='nav flex-column bg-light me-3'>
                <li className='nav-item'>
                    <a className='nav-link active' aria-current='page' href='##'>
                        推荐
                    </a>
                </li>
                <li className='nav-item'>
                    <a className='nav-link' href='##'>
                        排行榜
                    </a>
                </li>
                <li className='nav-item'>
                    <a className='nav-link' href='##'>
                        歌单
                    </a>
                </li>
            </ul>
            <div className='flex-grow-1'>xxx</div>
        </div>
    )
}
```

`pages/FindMusic/index.scss`

```scss
.findmusic {
    & > .nav {
        width: 150px;
        .nav-link {
            color: #333;
            &.active {
                color: pink;
            }
        }
    }
}
```

### 代码实现

注意：配置嵌套路由的时候，必须要先匹配到父路由，才能匹配到子路由。

```js
import React from 'react'
import { NavLink, Route, Switch, Redirect } from 'react-router-dom'
import './index.scss'
import Recommend from '../Recommend'
import Ranking from '../Ranking'
import SongList from '../SongList'

export default function FindMusic() {
    return (
        <div className='findmusic d-flex'>
            <ul className='nav flex-column bg-light me-3'>
                <li className='nav-item'>
                    <NavLink className='nav-link' aria-current='page' to='/findmusic/recommend'>
                        推荐
                    </NavLink>
                </li>
                <li className='nav-item'>
                    <NavLink className='nav-link' to='/findmusic/ranking'>
                        排行榜
                    </NavLink>
                </li>
                <li className='nav-item'>
                    <NavLink className='nav-link' to='/findmusic/songlist'>
                        歌单
                    </NavLink>
                </li>
            </ul>
            <div className='flex-grow-1'>
                <Switch>
                    {/* 默认展示推荐 */}
                    <Redirect exact from='/findmusic' to='/findmusic/recommend' />
                    <Route path='/findmusic/recommend' component={Recommend} />
                    <Route path='/findmusic/ranking' component={Ranking} />
                    <Route path='/findmusic/songlist' component={SongList} />
                </Switch>
            </div>
        </div>
    )
}
```

## 编程式导航

📝 需求：在 SongList 组件，点击按钮跳转到 MyMusic 组件。

-   编程式导航：通过 JS 代码来实现页面跳转，可以处理相关逻辑，更加灵活。

-   第一种方式通过 props 拿到 history 进行跳转，`props.history.push('/comment')`。

-   第二种方式通过 react-router-dom 提供的 useHistory 勾子进行跳转。

```js
import React from 'react'
import { useHistory } from 'react-router-dom'

export default function SongList(props) {
    const history = useHistory()
    const handleClick = () => {
        history.push('/mymusic')
    }
    return (
        <div>
            <h3>SongList</h3>
            <button onClick={handleClick}>MyMusic</button>
        </div>
    )
}
```

## 动态路由传参

<img src="/resource/images/ifer_cc.png" class="highlight2"/>

### 语法要求

1. 入口。

```js
<NavLink className='nav-link' to='/findmusic/ranking/bsb'>
    飙升榜
</NavLink>
```

2. 出口。

```js
<Route path='/findmusic/ranking/:id' component={RankingList} />
```

3. 获取。

```js
// props.match.params.id 或者通过 useParams()
```

### 静态结构

`pages/Ranking/index.js`

```js
import React from 'react'

export default function Ranking(props) {
    return (
        <div>
            <nav className='nav'>
                <a className='nav-link active' aria-current='page' href='##'>
                    飙升榜
                </a>
                <a className='nav-link' href='##'>
                    新歌榜
                </a>
                <a className='nav-link' href='##'>
                    原创榜
                </a>
            </nav>
            <div className='alert alert-light' role='alert'>
                A simple light alert—check it out!
            </div>
        </div>
    )
}
```

### 代码展示

`pages/Ranking/index.js`

```jsx
import { NavLink, Redirect, Route, Switch } from 'react-router-dom'
import RankingList from '../RankingList'

export default function Ranking(props) {
    return (
        <div>
            <nav className='nav'>
                <NavLink className='nav-link' to='/findmusic/ranking/bsb'>
                    飙升榜
                </NavLink>
                <NavLink className='nav-link' to='/findmusic/ranking/xgb'>
                    新歌榜
                </NavLink>
                <NavLink className='nav-link' to='/findmusic/ranking/ycb'>
                    原创榜
                </NavLink>
            </nav>
            <div className='alert alert-success  mt-3' role='alert'>
                <Switch>
                    <Redirect exact from='/findmusic/ranking' to='/findmusic/ranking/bsb' />
                    <Route path='/findmusic/ranking/:id' component={RankingList} />
                </Switch>
            </div>
        </div>
    )
}
```

`pages/RankingList/index.js`

```js
import { useParams } from 'react-router-dom'

export default function RankingList(props) {
    const params = useParams()
    return (
        <>
            <span className='me-3'>props 获取参数: {props.match.params.id}</span>
            <span>useParams 获取参数: {params.id}</span>
        </>
    )
}
```

## Query 传参

1. 入口。

```js
<NavLink className='nav-link' to='/findmusic/ranking/?id=bsb'>
    飙升榜
</NavLink>
```

2. 出口。

```js
<Route path='/findmusic/ranking' component={RankingList} />
```

3. 获取。

```js
import qs from 'qs'
export default function RankingList(props) {
    const { id } = qs.parse(props.location.search.slice(1))
    return (
        <>
            <span className='me-3'>props 获取参数: {id}</span>
        </>
    )
}
```

4. 代码。

`src/pages/Ranking/index.js`

```js
import { NavLink, Route, Switch } from 'react-router-dom'
import RankingList from '../RankingList'

export default function Ranking(props) {
    return (
        <div>
            <nav className='nav'>
                <NavLink className='nav-link' to='/findmusic/ranking/?id=bsb'>
                    飙升榜
                </NavLink>
                <NavLink className='nav-link' to='/findmusic/ranking/?id=xgb'>
                    新歌榜
                </NavLink>
                <NavLink className='nav-link' to='/findmusic/ranking/?id=ycb'>
                    原创榜
                </NavLink>
            </nav>
            <div className='alert alert-success  mt-3' role='alert'>
                <Switch>
                    {/* 错误写法，因为 to='/findmusic/ranking?id=bsb'，会先匹配外部的 <Route path='/findmusic/ranking' component={Ranking} /> */}
                    {/* 如此，死循环 */}
                    {/* <Redirect
                        exact
                        from='/findmusic/ranking'
                        to='/findmusic/ranking?id=bsb'
                    /> */}
                    {/* 正确写法应该在外部指定：<NavLink className='nav-link' to='/findmusic/ranking?id=bsb'></NavLink> */}
                    <Route path='/findmusic/ranking' component={RankingList} />
                </Switch>
            </div>
        </div>
    )
}
```
