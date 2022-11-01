---
title: 10_极客园 PC
date: 2021-11-21 15:10:49
tags:
---

## 今日目标

✔ 掌握 CSS Modules 的使用。

✔ 掌握界面访问控制。

<!-- more -->

## CSS Modules

### 需求

📝 给 Layout 组件加背景颜色。

`pages/Layout/index.scss`

```scss
div {
    background-color: pink;
}
```

`pages/Layout/index.js`

```js
import React from 'react'
import './index.scss'

export default function Layout() {
    return <div>Layout</div>
}
```

### 问题

组件之间的样式相互影响了。

### 解决

-   增加 class 类名。

`pages/Layout/index.scss`

```scss
.layout {
    background-color: pink;
}
```

`pages/Layout/index.js`

```js
import React from 'react'
import './index.scss'

export default function Layout() {
    return <div className='layout'>Layout</div>
}
```

-   上面方案的问题。

    项目大了之后谁又能保证 layout 类名不会重复呢？

-   CSS IN JS。

    a，是什么：使用 JS 编写 CSS 的统称，用来解决 CSS 样式冲突、覆盖等问题，[CSS IN JS](https://github.com/MicheleBertoli/css-in-js) 的具体实现有 50 多种，比如：[CSS Modules](https://github.com/css-modules/css-modules)、[styled-components](https://www.styled-components.com/) 等。

    b，推荐使用：[CSS Modules](https://github.com/css-modules/css-modules) （React 脚手架已集成，可直接使用）。

-   使用步骤如下。

    1. 修改样式的文件名为 `index.module.scss`。

    2. `pages/Layout/index.js` 使用。

    ```js
    import React from 'react'
    import styles from './index.module.scss'

    export default function Layout() {
        return <div className={styles.layout}>Layout</div>
    }
    ```

    3. 观察编译好之后的样式类名。

### 命名

-   类名最好使用驼峰命名，因为最终类名会生成 styles 对象的一个属性。

```css
/* 定义 */
.tabBar {
}
```

```js
// 使用
styles.tabBar
```

-   如果没有使用驼峰命名，对于不合法的命名，需要使用 `[]` 语法。

```css
/* 定义 */
.tab-bar {
}
```

```js
// 使用
styles['tab-bar']
```

### 需求

📝 使用 AntD 的分页组件，并修改其激活时候的样式。

a，`pages/Layout/index.js`

```js
import React from 'react'
import { Pagination } from 'antd'
import styles from './index.module.scss'

export default function Layout() {
    return (
        <div className={styles.layout}>
            <Pagination defaultCurrent={1} total={50} />
        </div>
    )
}
```

b，`pages/Layout/index.module.scss`

```scss
.ant-pagination-item-active {
    border-color: red;
    a {
        color: red;
    }
}
```

c，问题：没有效果（没有命中子组件的样式，因为你写的类名被编译成了另外一个随机的类名）。

d，解决：使用 :global 包裹子组件的样式，css modules 就不会去修改这个类名了。

```scss
:global {
    .ant-pagination-item-active {
        border-color: red;
        a {
            color: red;
        }
    }
}
```

e，问题 1：被 `:global` 包裹的样式，相当于没有使用 CSS Modules（全局的），其他地方的使用都会受到影响。

f，问题 2：组件中类名多的话，都要 style.xxx，写起来也很麻烦。

### 最佳实践

-   每个组件的根节点使用 CSS Modules 形式的类名（根元素的类名：root）。

-   其他所有的子节点样式包裹在 root 内，并通过 :global 变成普通的 CSS 类名 。

`pages/Layout/index.module.scss`

```scss
.root {
    background-color: pink;
    // :global 内部的样式全部没有被修改，同时又被限制在了 root 下（root 是随机生成的唯一的）
    :global {
        .title {
            color: green;
        }
        .ant-pagination-item-active {
            border-color: red;
            a {
                color: red;
            }
        }
    }
}
```

`pages/Layout/index.js`

```js
import React from 'react'
import { Pagination } from 'antd'
import styles from './index.module.scss'

export default function Layout() {
    return (
        <div className={styles.root}>
            <h3 className='title'>Layout</h3>
            <Pagination defaultCurrent={1} total={50} />
        </div>
    )
}
```

📝 练习：改写 `pages/Login/index.scss` 为 `index.module.scss` 的形式。

## 粘贴基本结构和样式

<img src="/resource/images/ifer_geek.png"/>

`pages/Layout/index.js`，[基本结构](https://ant.design/components/layout-cn/#components-layout-demo-top-side-2)

```jsx
import React from 'react'
import { Layout, Menu, Breadcrumb } from 'antd'
import { UserOutlined, LaptopOutlined, NotificationOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
const { SubMenu } = Menu
const { Header, Content, Sider } = Layout
// 注意这里改成了 MyLayout，防止和 AntD 的冲突
export default function MyLayout() {
    return (
        <div className={styles.root}>
            <Layout>
                <Header className='header'>
                    <div className='logo' />
                    <Menu theme='dark' mode='horizontal' defaultSelectedKeys={['2']}>
                        <Menu.Item key='1'>nav 1</Menu.Item>
                        <Menu.Item key='2'>nav 2</Menu.Item>
                        <Menu.Item key='3'>nav 3</Menu.Item>
                    </Menu>
                </Header>
                <Layout>
                    <Sider width={200} className='site-layout-background'>
                        <Menu mode='inline' defaultSelectedKeys={['1']} defaultOpenKeys={['sub1']} style={{ height: '100%', borderRight: 0 }}>
                            <SubMenu key='sub1' icon={<UserOutlined />} title='subnav 1'>
                                <Menu.Item key='1'>option1</Menu.Item>
                                <Menu.Item key='2'>option2</Menu.Item>
                                <Menu.Item key='3'>option3</Menu.Item>
                                <Menu.Item key='4'>option4</Menu.Item>
                            </SubMenu>
                            <SubMenu key='sub2' icon={<LaptopOutlined />} title='subnav 2'>
                                <Menu.Item key='5'>option5</Menu.Item>
                                <Menu.Item key='6'>option6</Menu.Item>
                                <Menu.Item key='7'>option7</Menu.Item>
                                <Menu.Item key='8'>option8</Menu.Item>
                            </SubMenu>
                            <SubMenu key='sub3' icon={<NotificationOutlined />} title='subnav 3'>
                                <Menu.Item key='9'>option9</Menu.Item>
                                <Menu.Item key='10'>option10</Menu.Item>
                                <Menu.Item key='11'>option11</Menu.Item>
                                <Menu.Item key='12'>option12</Menu.Item>
                            </SubMenu>
                        </Menu>
                    </Sider>
                    <Layout style={{ padding: '0 24px 24px' }}>
                        <Breadcrumb style={{ margin: '16px 0' }}>
                            <Breadcrumb.Item>Home</Breadcrumb.Item>
                            <Breadcrumb.Item>List</Breadcrumb.Item>
                            <Breadcrumb.Item>App</Breadcrumb.Item>
                        </Breadcrumb>
                        <Content
                            className='site-layout-background'
                            style={{
                                padding: 24,
                                margin: 0,
                                minHeight: 280,
                            }}
                        >
                            Content
                        </Content>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

`pages/Layout/index.modules.scss`

```scss
.root {
    :global {
        .logo {
            float: left;
            width: 120px;
            height: 31px;
            margin: 16px 24px 16px 0;
            background: rgba(255, 255, 255, 0.3);
        }
    }
}
```

## 调整头部结构和样式

-   头部结构

```jsx
import React from 'react'
import { Layout, Menu, Breadcrumb } from 'antd'
import { UserOutlined, LaptopOutlined, NotificationOutlined, LogoutOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
const { SubMenu } = Menu
const { Header, Content, Sider } = Layout

export default function MyLayout() {
    return (
        <div className={styles.root}>
            <Layout>
                <Header className='header'>
                    <div className='logo' />
                    <div className='profile'>
                        <span>黑马先锋</span>
                        <span>
                            <LogoutOutlined></LogoutOutlined> 退出
                        </span>
                    </div>
                </Header>
                <Layout>
                    <Sider width={200} className='site-layout-background'>
                        <Menu mode='inline' defaultSelectedKeys={['1']} defaultOpenKeys={['sub1']} style={{ height: '100%', borderRight: 0 }}>
                            <SubMenu key='sub1' icon={<UserOutlined />} title='subnav 1'>
                                <Menu.Item key='1'>option1</Menu.Item>
                                <Menu.Item key='2'>option2</Menu.Item>
                                <Menu.Item key='3'>option3</Menu.Item>
                                <Menu.Item key='4'>option4</Menu.Item>
                            </SubMenu>
                            <SubMenu key='sub2' icon={<LaptopOutlined />} title='subnav 2'>
                                <Menu.Item key='5'>option5</Menu.Item>
                                <Menu.Item key='6'>option6</Menu.Item>
                                <Menu.Item key='7'>option7</Menu.Item>
                                <Menu.Item key='8'>option8</Menu.Item>
                            </SubMenu>
                            <SubMenu key='sub3' icon={<NotificationOutlined />} title='subnav 3'>
                                <Menu.Item key='9'>option9</Menu.Item>
                                <Menu.Item key='10'>option10</Menu.Item>
                                <Menu.Item key='11'>option11</Menu.Item>
                                <Menu.Item key='12'>option12</Menu.Item>
                            </SubMenu>
                        </Menu>
                    </Sider>
                    <Layout style={{ padding: '0 24px 24px' }}>
                        <Breadcrumb style={{ margin: '16px 0' }}>
                            <Breadcrumb.Item>Home</Breadcrumb.Item>
                            <Breadcrumb.Item>List</Breadcrumb.Item>
                            <Breadcrumb.Item>App</Breadcrumb.Item>
                        </Breadcrumb>
                        <Content
                            className='site-layout-background'
                            style={{
                                padding: 24,
                                margin: 0,
                                minHeight: 280,
                            }}
                        >
                            Content
                        </Content>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

-   头部样式

```scss
.root {
    :global {
        // :global 内部的样式全部没有被修改，同时又被限制在了 root 下（root 是随机生成的唯一的）
        .ant-layout-header {
            padding: 0;
        }
        // 头部
        .header {
            // 左侧
            .logo {
                float: left;
                width: 200px;
                height: 60px;
                margin: 2px 0px;
                background: url(~@/assets/logo.png) no-repeat 0 0;
                background-size: 200px 60px;
            }
            // 右侧
            .profile {
                position: absolute;
                right: 20px;
                color: #fff;
                font-weight: 700;
                span {
                    margin-left: 10px;
                    cursor: pointer;
                }
            }
        }
    }
}
```

## 左侧菜单样式与结构

1. 导入图标。

```jsx
import { HomeOutlined, HddOutlined, EditOutlined } from '@ant-design/icons'
```

2. 修改菜单的结构。

```jsx
<Menu mode='inline' theme='dark' defaultSelectedKeys={['1']} style={{ height: '100%', borderRight: 0 }}>
    <Menu.Item icon={<HomeOutlined />} key='1'>
        数据概览
    </Menu.Item>
    <Menu.Item icon={<HddOutlined />} key='2'>
        内容管理
    </Menu.Item>
    <Menu.Item icon={<EditOutlined />} key='3'>
        发布文章
    </Menu.Item>
</Menu>
```

3. 修改菜单的样式。

```scss
.root {
    // #1
    height: 100%;
    :global {
        .ant-layout-header {
            padding: 0;
        }
        // 头部
        .header {
            // 左侧
            .logo {
                float: left;
                width: 200px;
                height: 60px;
                margin: 2px 0px;
                // background: rgba(255, 255, 255, 0.3);
                background: url(~@/assets/logo.png) no-repeat 0 0;
                background-size: 200px 60px;
            }
            // 右侧
            .profile {
                position: absolute;
                right: 20px;
                color: #fff;
                font-weight: 700;
                span {
                    margin-left: 10px;
                    cursor: pointer;
                }
            }
        }
        // #2
        .ant-layout {
            height: 100%;
        }
        // #3
        .site-layout-background {
            background-color: #fff;
        }
    }
}
```

4. `App.js`，给 Switch 组件外部的 div 添加了 app class。

```js
import React from 'react'
import { BrowserRouter as Router, Route, Switch, Redirect } from 'react-router-dom'
import Login from '@/pages/Login'
import Layout from '@/pages/Layout'
import NotFound from '@/pages/NotFound'

export default function App() {
    return (
        <Router>
            <div className='app'>
                <Switch>
                    {/* from 默认模糊匹配，表示以 '/' 开头就匹配 */}
                    <Redirect exact from='/' to='/home' />
                    <Route path='/login' component={Login}></Route>
                    <Route path='/home' component={Layout}></Route>
                    <Route component={NotFound} />
                </Switch>
            </div>
        </Router>
    )
}
```

5. `index.scss`，把 app class 的高度也指定了 100%。

```scss
* {
    margin: 0;
    padding: 0;
    list-style: none;
}
html,
body,
#root,
.app {
    height: 100%;
}
```

## 嵌套路由配置

### 目标

能够在右侧内容区域展示左侧菜单对应的页面内容。

### 步骤

1. 在 pages 目录中，分别创建：`Home`（数据概览）/`Article`（内容管理）/`Publish`（发布文章）页面文件夹。

2. 分别在三个文件夹中创建 index.js 并创建基础组件后导出。

3. 在 Layout 页面组件中，配置子路由。

4. 使用 Link 修改左侧菜单内容，与子路由规则匹配实现路由切换。

### 代码

`pages/Home/index.js`

```js
import React from 'react'

export default function Home() {
    return <div>Home</div>
}
```

`pages/Layout/index.js`

```js
import React from 'react'
import { Layout, Menu } from 'antd'
import { Link, Route, Switch } from 'react-router-dom'
import { HomeOutlined, HddOutlined, EditOutlined, LogoutOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Home from '../Home'
import Article from '../Article'
import Publish from '../Publish'
const { Header, Sider } = Layout

export default function MyLayout() {
    return (
        <div className={styles.root}>
            <Layout>
                <Header className='header'>
                    <div className='logo' />
                    <div className='profile'>
                        <span>黑马先锋</span>
                        <span>
                            <LogoutOutlined></LogoutOutlined> 退出
                        </span>
                    </div>
                </Header>
                <Layout>
                    <Sider width={200} className='site-layout-background'>
                        {/* #1 入口 */}
                        <Menu mode='inline' theme='dark' defaultSelectedKeys={['1']} style={{ height: '100%', borderRight: 0 }}>
                            <Menu.Item icon={<HomeOutlined />} key='1'>
                                <Link to='/home'>数据概览</Link>
                            </Menu.Item>
                            <Menu.Item icon={<HddOutlined />} key='2'>
                                <Link to='/home/article'>内容管理</Link>
                            </Menu.Item>
                            <Menu.Item icon={<EditOutlined />} key='3'>
                                <Link to='/home/publish'>发布文章</Link>
                            </Menu.Item>
                        </Menu>
                    </Sider>
                    <Layout style={{ padding: '24px', backgroundColor: '#fff' }}>
                        {/* #2 出口 */}
                        <Switch>
                            <Route exact path='/home' component={Home} />
                            <Route path='/home/article' component={Article} />
                            <Route path='/home/publish' component={Publish} />
                        </Switch>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

注意：由于嵌套路由展示的内容是放在某个父级路由中的，所以，要展示嵌套路由的前提就是先展示父级路由内容，因此，嵌套路由的路径是基于父级路由路径的，比如当前项目功能中，`/home/article` 就是在父级路由 `/home` 的基础上，添加了 `/article`。

## 保持高亮状态

### 目标

能够在刷新页面时保持对应菜单高亮。

### 步骤

思路：将当前访问页面的路由地址作为 Menu 选中项的值（selectedKeys）即可。

1. 将 Menu 的 key 属性修改为与其对应的路由地址。

2. 获取到当前正在访问页面的路由地址。

3. 将当前路由地址设置为 selectedKeys 属性的值。

### 代码

`pages/Layout/index.js`

```js
import React from 'react'
import { Layout, Menu } from 'antd'
import { Link, Route, Switch, useLocation } from 'react-router-dom'
import { HomeOutlined, HddOutlined, EditOutlined, LogoutOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Home from '../Home'
import Article from '../Article'
import Publish from '../Publish'
const { Header, Sider } = Layout

export default function MyLayout() {
    // #1
    const location = useLocation()
    return (
        <div className={styles.root}>
            <Layout>
                <Header className='header'>
                    <div className='logo' />
                    <div className='profile'>
                        <span>黑马先锋</span>
                        <span>
                            <LogoutOutlined></LogoutOutlined> 退出
                        </span>
                    </div>
                </Header>
                <Layout>
                    <Sider width={200} className='site-layout-background'>
                        {/* #2 */}
                        <Menu mode='inline' theme='dark' defaultSelectedKeys={[location.pathname]} style={{ height: '100%', borderRight: 0 }}>
                            {/* #3 */}
                            <Menu.Item icon={<HomeOutlined />} key='/home'>
                                <Link to='/home'>数据概览</Link>
                            </Menu.Item>
                            <Menu.Item icon={<HddOutlined />} key='/home/article'>
                                <Link to='/home/article'>内容管理</Link>
                            </Menu.Item>
                            <Menu.Item icon={<EditOutlined />} key='/home/publish'>
                                <Link to='/home/publish'>发布文章</Link>
                            </Menu.Item>
                        </Menu>
                    </Sider>
                    <Layout style={{ padding: '24px', backgroundColor: '#fff' }}>
                        <Switch>
                            <Route exact path='/home' component={Home} />
                            <Route path='/home/article' component={Article} />
                            <Route path='/home/publish' component={Publish} />
                        </Switch>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

### 细节

🙁 问题：从 `pages/Publish/index.js` 跳转到首页，发现对应的侧边栏并没有高亮。

```js
import React from 'react'
import { Link } from 'react-router-dom'
export default function Publish() {
    return (
        <div>
            <h3>Publish</h3>
            <Link to='/home'>首页</Link>
        </div>
    )
}
```

🤔 原因：defaultSelectedKeys 属性只会在第一次进入的时候生效，为什么点击侧边栏的时候没有问题呢？因为侧边栏自带点击高亮的效果。

😀 解决：把 Menu 组件的 defaultSelectedKeys 属性换成 selectedKeys 即可。

### 总结

-   通过哪个属性指定 Menu 组件的选中项？

-   高亮状态是如何保持的？

## 展示个人信息

### 目标

能够在布局页面右上角展示登录用户名。

### 步骤

1. 在 Layout 组件中 dispatch 获取个人信息的异步 action。

2. 在 `actions/user.js` 中，创建异步 action 并获取个人信息。

3. 将接口返回的个人信息 dispatch 到 reducer 来存储该状态。

4. 在 `reducers/user.js` 中，处理个人信息的 action，将状态存储到 Redux 中。

5. 在 Layout 组件中获取个人信息并展示。

### 代码

1. `store/constants/index.js`

```jsx
export const LOGIN = 'LOGIN'
export const GET_USER_INFO = 'GET_USER_INFO'
```

2. `store/actions/user.js`

```jsx
import request from '@/utils/request'
import { GET_USER_INFO } from '../constants'

export const getUserInfoAc = (payload) => ({
    type: GET_USER_INFO,
    payload,
})

export const getUserInfo = () => {
    return async (dispatch) => {
        const res = await request.get('/user/profile')
        dispatch(getUserInfoAc(res.data.data))
    }
}
```

3. `store/reducers/user.js`

```jsx
import { GET_USER_INFO } from '../constants'

export default function user(state = {}, action) {
    switch (action.type) {
        case GET_USER_INFO:
            return action.payload
        default:
            return state
    }
}
```

4. `store/reducers/index.js`，合并 Reducer。

```jsx
import { combineReducers } from 'redux'
import login from './login'
import user from './user'
const rootReducer = combineReducers({
    login,
    user,
})
export default rootReducer
```

5. `pages/Layout/index.js`

```jsx
import { useEffect } from 'react'
import { Layout, Menu } from 'antd'
import { Link, Route, Switch, useLocation } from 'react-router-dom'
import { useDispatch, useSelector } from 'react-redux'
import { HomeOutlined, HddOutlined, EditOutlined, LogoutOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Home from '../Home'
import Article from '../Article'
import Publish from '../Publish'
import { getUserInfo } from '@/store/actions/user'
const { Header, Sider } = Layout

export default function MyLayout() {
    const location = useLocation()
    const dispatch = useDispatch()
    // #2
    const user = useSelector((state) => state.user)
    // #1
    useEffect(() => dispatch(getUserInfo()), [dispatch])
    return (
        <div className={styles.root}>
            <Layout>
                <Header className='header'>
                    <div className='logo' />
                    <div className='profile'>
                        {/* #3 */}
                        <span>{user.name}</span>
                        <span>
                            <LogoutOutlined></LogoutOutlined> 退出
                        </span>
                    </div>
                </Header>
                <Layout>
                    <Sider width={200} className='site-layout-background'>
                        <Menu mode='inline' theme='dark' selectedKeys={[location.pathname]} style={{ height: '100%', borderRight: 0 }}>
                            <Menu.Item icon={<HomeOutlined />} key='/home'>
                                <Link to='/home'>数据概览</Link>
                            </Menu.Item>
                            <Menu.Item icon={<HddOutlined />} key='/home/article'>
                                <Link to='/home/article'>内容管理</Link>
                            </Menu.Item>
                            <Menu.Item icon={<EditOutlined />} key='/home/publish'>
                                <Link to='/home/publish'>发布文章</Link>
                            </Menu.Item>
                        </Menu>
                    </Sider>
                    <Layout style={{ padding: '24px', backgroundColor: '#fff' }}>
                        <Switch>
                            <Route exact path='/home' component={Home} />
                            <Route path='/home/article' component={Article} />
                            <Route path='/home/publish' component={Publish} />
                        </Switch>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

6. `utils/request.js`，统一携带 Token。

```jsx
import axios from 'axios'
import { getToken } from '.'

const instance = axios.create({
    baseURL: 'http://geek.itheima.net/v1_0/',
    timeout: 5000,
})

instance.interceptors.request.use(
    (config) => {
        const token = getToken()
        if (token) {
            config.headers.Authorization = `Bearer ${token}`
        }
        return config
    },
    (error) => {
        return Promise.reject(error)
    }
)

instance.interceptors.response.use(
    (response) => {
        return response
    },
    (error) => {
        return Promise.reject(error)
    }
)

export default instance
```

## 退出登录功能

### 目标

能够理解退出功能的实现步骤。

### 步骤

1. 在 `store/constants/index.js` 文件中创建 actionType。

2. 在 `store/actions/login.js` 文件中创建退出的 action（清除本地和 Redux 中的 Token）。

3. 在 `store/reducers/login.js` 文件中处理 Redux 中的数据（Token）。

4. 在 `pages/Layout/index.js` 组件中进行退出的操作。

### 代码

1. `store/constants/index.js`

```js
export const LOGIN = 'LOGIN'
export const GET_USER_INFO = 'GET_USER_INFO'
export const LOGOUT = 'LOGOUT'
```

2. `store/actions/login.js`

```js
import request from '@/utils/request'
import { LOGIN, LOGOUT } from '../constants'
import { setToken, removeToken } from '@/utils'

// 登录
export const loginAc = (payload) => ({ type: LOGIN, payload })
export const login = (payload) => {
    return async (dispatch) => {
        const res = await request({
            method: 'post',
            url: '/authorizations',
            data: payload,
        })
        const token = res.data.data.token
        setToken(token)
        dispatch(loginAc(token))
    }
}

// 退出
export const logoutAc = () => ({ type: LOGOUT })
// 下面写法也 OK
/* export const logout = () => {
  removeToken()
  return logoutAc()
} */
// 更建议下面写法
export const logout = () => {
    return (dispatch) => {
        removeToken()
        dispatch(logoutAc())
    }
}
```

3. `store/reducers/login.js`

```js
import { LOGIN, LOGOUT } from '../constants'
const initValue = {
    token: '',
}

export default function login(state = initValue, action) {
    if (action.type === LOGIN) {
        return {
            ...state,
            token: action.payload,
        }
    }
    if (action.type === LOGOUT) {
        return {
            ...state,
            token: '',
        }
    }
    return state
}
```

4. `pages/Layout/index.js`

```jsx
import { useEffect } from 'react'
import { Layout, Menu, message, Popconfirm } from 'antd'
import { Link, Route, Switch, useLocation, useHistory } from 'react-router-dom'
import { useDispatch, useSelector } from 'react-redux'
import { HomeOutlined, HddOutlined, EditOutlined, LogoutOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Home from '../Home'
import Article from '../Article'
import Publish from '../Publish'
import { getUserInfo } from '@/store/actions/user'
import { logout } from '@/store/actions/login'
const { Header, Sider } = Layout

export default function MyLayout() {
    const location = useLocation()
    const dispatch = useDispatch()
    const history = useHistory()
    const user = useSelector((state) => state.user)
    useEffect(() => dispatch(getUserInfo()), [dispatch])

    const onConfirm = () => {
        // #1 清除 Token
        dispatch(logout())
        // #2 跳转到登录页
        history.push('/login')
        // #3 提示消息
        message.success('退出成功', 1)
    }
    return (
        <div className={styles.root}>
            <Layout>
                <Header className='header'>
                    <div className='logo' />
                    <div className='profile'>
                        <span>{user.name}</span>
                        <Popconfirm title='你确定要退出本系统吗?' okText='确定' cancelText='取消' placement='bottomRight' onConfirm={onConfirm}>
                            <LogoutOutlined></LogoutOutlined> 退出
                        </Popconfirm>
                    </div>
                </Header>
                <Layout>
                    <Sider width={200} className='site-layout-background'>
                        <Menu mode='inline' theme='dark' selectedKeys={[location.pathname]} style={{ height: '100%', borderRight: 0 }}>
                            <Menu.Item icon={<HomeOutlined />} key='/home'>
                                <Link to='/home'>数据概览</Link>
                            </Menu.Item>
                            <Menu.Item icon={<HddOutlined />} key='/home/article'>
                                <Link to='/home/article'>内容管理</Link>
                            </Menu.Item>
                            <Menu.Item icon={<EditOutlined />} key='/home/publish'>
                                <Link to='/home/publish'>发布文章</Link>
                            </Menu.Item>
                        </Menu>
                    </Sider>
                    <Layout style={{ padding: '24px', backgroundColor: '#fff' }}>
                        <Switch>
                            <Route exact path='/home' component={Home} />
                            <Route path='/home/article' component={Article} />
                            <Route path='/home/publish' component={Publish} />
                        </Switch>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

<font color=999>🧐 退出之后，Redux 中的用户信息最好也清空。</font>

## 处理 Token 失效

`utils/request.js`，在响应拦截器处处理 Token 失效的问题。

```js
import { message } from 'antd'
import axios from 'axios'
import { getToken } from '.'
import { logout } from '@/store/actions/login'
import store from '../store'

const instance = axios.create({
    baseURL: 'http://geek.itheima.net/v1_0/',
    timeout: 5000,
})

instance.interceptors.request.use(
    (config) => {
        const token = getToken()
        if (token) {
            config.headers.Authorization = `Bearer ${token}`
        }
        return config
    },
    (error) => {
        return Promise.reject(error)
    }
)

instance.interceptors.response.use(
    (response) => {
        return response
    },
    (err) => {
        if (err.response.status === 401) {
            // #1 提示消息
            message.error('登录信息过期', 1)
            // #2 清除 Token（本地和 Redux 的）
            store.dispatch(logout())
            // #3 用此跳转有问题：看不到提示消息了，因为此 API 会导致页面刷新
            window.location.href = '/login'
        }
        return Promise.reject(err)
    }
)

export default instance
```

## 处理 location.href 的问题

### 目标

非组件中如何使用 history。

### 步骤

说明：为了能够在非组件环境下拿到路由信息，需要我们自定义 Router 的 history。

1. 在 `utils/history.js` 文件中创建 hisotry 对象并导出。

2. 在 App.js 中导入 history 对象，并设置为 Router 的 history 属性对应的值。

3. 通过响应拦截器处理 Token 失效。

### 代码

`utils/history.js`

```js
import { createBrowserHistory } from 'history'
const history = createBrowserHistory()
export default history
```

`App.js`

```js
import { Route, Switch, Redirect, Router } from 'react-router-dom'
import Login from '@/pages/Login'
import Layout from '@/pages/Layout'
import NotFound from '@/pages/NotFound'
import history from '@/utils/history'
// Router + history = BrowserRouter
// Router + hash = HashRouter
export default function App() {
    return (
        <Router history={history}>
            <div className='app'>
                <Switch>
                    <Redirect exact from='/' to='/home' />
                    <Route path='/login' component={Login}></Route>
                    <Route path='/home' component={Layout}></Route>
                    <Route component={NotFound} />
                </Switch>
            </div>
        </Router>
    )
}
```

`utils/request.js`

```js
instance.interceptors.response.use(
    (response) => {
        return response
    },
    (err) => {
        if (err.response.status === 401) {
            // #1 提示消息
            message.error('登录信息过期', 1)
            // #2 清除 Token（本地和 Redux 的）
            store.dispatch(logout())
            // #3 使用 history 进行跳转
            history.push('/login')
        }
        return Promise.reject(err)
    }
)
```

### 优化

-   触发条件：把网络改到最慢并且超时时间改成 500（或者把网络禁用）。

-   问题表现：控制台会有错误，但没有提示（期望有错误提示）。

-   出现原因：这个时候可能连 err.response 都没有，所以 #1 处的判断会有问题。

-   解决办法：最好给一个错误提示告诉下用户。

```js
instance.interceptors.response.use(
    (response) => {
        return response
    },
    (err) => {
        // #mark
        if (!err.response) {
            message.error('网络繁忙，请稍后重试')
            return Promise.reject(err)
        }
        // #1
        if (err.response.status === 401) {
            message.error('登录信息过期', 1)
            store.dispatch(logout())
            history.push('/login')
        }
        return Promise.reject(err)
    }
)
```

`src/pages/Login/index.js`，解决点击登录时按钮的 loading 不关闭的问题。

```js
const onFinish = async (values) => {
    setLoading(true)
    try {
        await dispatch(login(values))
        message.success('登录成功', 1, () => {
            history.push('/login')
        })
    } catch (e) {
        // #mark
        const msg = e.response?.data?.message
        if (!msg) return setLoading(false)
        message.error(msg, 1, () => setLoading(false))
    }
}
```

### 总结

如何在非组件环境下实现路由跳转？

## 界面访问控制

### 概述

-   有些页面是需要登录后才能访问的（后台首页、内容管理等），否则，直接**拦截**到登录页。

-   不需要登录就可以访问的页面：登录页。

-   如何实现界面访问控制呢？

    a，分析：不管哪个页面都是通过路由来访问的，因此，需要从路由角度来进行控制。

    b，难点：React 中没有导航守卫，需要自己封装。

    c，思路：封装 `PrivateRoute` 组件代替默认的 Route 组件，里面进行判断是否登录，登录直接显示要访问的页面，没有登录跳转到登录页面。

### 分析

-   如何封装？参考 react-router-dom 文档中提供的鉴权[示例](https://v5.reactrouter.com/web/example/auth-workflow)。

-   PrivateRoute 组件实际上就是对原来的 Route 组件做了一次包装，来实现了一些额外的功能（判断是否登录来做相应操作）。

-   Route 组件 render 属性的使用。

`src/App.js`

```js
<Route
    path='/home'
    render={() => {
        const token = getToken()
        if (token) {
            return <Layout />
        } else {
            return <Redirect to='/login' />
        }
    }}
></Route>
```

### 封装

#### 基本写法

`App.js` 中期望如下使用。

```js
<PrivateRoute path='/home' component={Layout} />
```

`components/PrivateRoute/index.js`，下面代码实现了和 Route 组件完全一样的功能。

```js
import { Route } from 'react-router-dom'

export default function PrivateRoute(props) {
    return <Route {...props} />
}
```

进一步封装：内部做鉴权处理。

```js
import React from 'react'
import { isAuth } from '@/utils/storage'
import { Route, Redirect } from 'react-router-dom'
// 除了 component 属性，把所有的属性收集到 rest 中
export default function PrivateRoute({ component: Component, ...rest }) {
    return (
        <Route
            {...rest}
            render={() => {
                if (isAuth()) {
                    return <Component />
                } else {
                    return <Redirect to='/login' />
                }
            }}
        />
    )
}
```

#### 支持 children

`App.js` 中期望支持如下写法。

```jsx
<PrivateRoute path='/home'>
    <Layout />
</PrivateRoute>
```

```js
import React from 'react'
import { isAuth } from '@/utils/storage'
import { Route, Redirect } from 'react-router-dom'
export default function PrivateRoute({ children, ...rest }) {
    return (
        <Route
            {...rest}
            render={() => {
                if (isAuth()) {
                    // 接收到的 children 直接就表示一个组件，所以可以直接返回
                    return children
                } else {
                    return <Redirect to='/login' />
                }
            }}
        />
    )
}
```

#### 综合写法

期望 children 的写法和 components 属性的写法都支持，即。

```jsx
<PrivateRoute path='/home'>
    <Layout />
</PrivateRoute>
```

或

```jsx
<Route path='/login' component={Login}></Route>
```

`src/components/PrivateRoute/index.js`

```js
import React from 'react'
import { isAuth } from '@/utils/storage'
import { Route, Redirect } from 'react-router-dom'
export default function PrivateRoute({ children, component: Component, ...rest }) {
    return (
        <Route
            {...rest}
            render={() => {
                if (isAuth()) {
                    return children ? children : <Component />
                } else {
                    return <Redirect to='/login' />
                }
            }}
        />
    )
}
```

### 优化

场景：在发布文章页把 Token <font color=e32d40>**清除**</font>了，刷新，退出了登录，期望再次登录后还是回到原来的发布文章页。

`components/PrivateRoute/index.js`

```jsx
import { useLocation } from 'react-router-dom'
import { isAuth } from '@/utils/storage'
import { Route, Redirect } from 'react-router-dom'
export default function PrivateRoute({ children, component: Component, ...rest }) {
    const location = useLocation()
    return (
        <Route
            {...rest}
            render={() => {
                if (isAuth()) {
                    return children ? children : <Component />
                } else {
                    /* Redirect 跳转默认是 replace 的效果，也可以添加 push 属性变成 push 的效果 */
                    return (
                        <Redirect
                            to={{
                                // #1 指定跳转地址
                                pathname: '/login',
                                // #2 通过 state 来传递额外的参数
                                state: {
                                    // 把当前的地址作为 from 属性传递了过去
                                    from: location.pathname,
                                },
                            }}
                        />
                    )
                }
            }}
        />
    )
}
```

`pages/Login/index.js`，登录成功后对处理需要跳转的地址。

```jsx
const onFinish = async (values) => {
    setLoading(true)
    try {
        await dispatch(login(values))
        message.success('登录成功', 1, () => {
            // history.push('/home')
            // #mark
            // location.state?.from ? history.push(location.state.from) : history.push('/home')
            history.push(location.state?.from ? location.state.from : '/home')
        })
    } catch (e) {
        message.error(e.response.data.message, 1, () => {
            setLoading(false)
        })
    }
}
```

🤔 新的问题：返回的时候回退到了登录页，可以通过 replace 跳转进行修复。

```js
const onFinish = async (values) => {
    setLoading(true)
    try {
        await dispatch(login(values))
        message.success('登录成功', 1, () => {
            const from = location.state ? location.state.from : '/home'
            // 修复，这儿改成了 replace，表示不把当前页面保留到历史记录（或者用新的页面替换掉当前页面历史）
            history.replace(from)
        })
    } catch (e) {
        message.error(e.response.data.message, 1, () => {
            setLoading(false)
        })
    }
}
```

## 优化 Token 过期回跳处理

📝 由于 Token 过期造成的退出，登录成功后也期望跳转回过期时的那个页面。

`utils/request.js`

```jsx
instance.interceptors.response.use(
    (response) => {
        return response
    },
    (err) => {
        if (!err.response) {
            message.error('网络繁忙，请稍后重试')
            return Promise.reject(err)
        }
        if (err.response.status === 401) {
            message.error('登录信息过期', 1)
            store.dispatch(logout())
            // history.push('/login')
            // #mark
            history.replace({
                pathname: '/login',
                state: {
                    from: history.location.pathname,
                },
            })
        }
        return Promise.reject(err)
    }
)
```

## 首页展示

-   `pages/Home/index.module.scss`

```scss
.root {
    width: 100%;
    height: 100%;
    background: #f5f5f5 url(../../assets/chart.png) no-repeat center / contain;
}
```

-   `pages/Home/index.js`

```js
import styles from './index.module.scss'

export default function Home() {
    return <div className={styles.root}>Home</div>
}
```
