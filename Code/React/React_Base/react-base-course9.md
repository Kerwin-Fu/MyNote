---
title: 09_极客园 PC
date: 2021-11-21 00:48:17
tags:
---

## 今日目标

✔ 学会进行项目初始化。

✔ 掌握登录功能的实现。

<!-- more -->

## 项目介绍

### 目标

了解项目定位和功能。

### 内容

-   极客园 PC 端项目：个人自媒体管理端。

-   极客园对标 `CSDN`、`博客园` 等竞品，致力成为更加贴近年轻 IT 从业者（学员）的科技资讯类应用。

-   产品关键词：IT、极客、活力、科技、技术分享、前沿动态、内容社交。

-   用户特点：年轻有活力，对 IT 领域前言科技信息充满探索欲和学习热情。

-   项目功能和演示：包括登录、退出、首页、内容（文章）管理（文章列表、发布文章、修改文章）。

### 技术栈

-   项目搭建：React 官方脚手架 `create-react-app`。

-   React Hooks。

-   状态管理：Redux。

-   UI 组件库：`AntDv4`。

-   AJAX 请求库：`Axios`。

-   路由：`react-router-dom`。

-   富文本编辑器：`react-quill`。

-   CSS 预编译器：`sass`。

-   CSS Modules 避免组件之间的样式冲突。

## 项目搭建

### 目标

能够基于脚手架搭建项目基本结构。

### 步骤

1. 使用 React CLI 搭建项目：`npx create-react-app geek-pc`。

2. 进入项目根目录：`cd geek-pc`。

3. 启动项目：`yarn start`。

4. 调整项目目录结构如下。

```tree
src
|-- App.js          根组件
|-- assets          项目资源文件，比如，图片 等
|-- components      通用组件
|-- index.css       全局样式
|-- index.js        项目入口
|-- pages           页面
|-- store           Redux 状态仓库
`-- utils           工具，比如，token、axios 的封装等
```

<font color=#999>🧐 通过命令 `tree src` 可以输出上面结构，空文件夹下创建 `.gitkeep` 文件可以被 Git 追踪。</font>

### 核心代码

`src/index.js`

```js
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
ReactDOM.render(<App />, document.querySelector('#root'))
```

`src/App.js`

```js
export default function App() {
    return <div>根组件</div>
}
```

## 使用 Git 管理项目

### 目标

能够将项目推送到远程仓库。

### 步骤

1. 在项目根目录打开终端，并初始化 git 仓库（如果已经有了 git 仓库，无需重复该步），命令：`git init`。

2. 添加项目内容到暂存区：`git add .`。

3. 提交项目内容到仓库区：`git commit -m 项目初始化`。

4. 添加 remote 仓库地址：`git remote add origin [gitee 仓库地址]`。

5. 将项目内容推送到 gitee：`git push origin master -u`。

6. 以后只需要：`git push` 即可。

## 使用 CSS 预编译器

### 目标

能够在 CRA 中使用 Sass 写样式。

### 内容

`Sass` 是 CSS 预处理器，作用类似于 Less，由于 CRA 创建的项目中内置了 Sass 相关的配置，只需要 `yarn add sass` 后便可以直接使用 SASS 来写样式。[Sass 支持两种后缀](https://sass.bootcss.com/documentation/syntax)，分别是：`.sass` 和 `.scss`，区别如下。

a，`.sass` 是一种简化语法形式（例如用缩进代替 `{}`，用换行代替 `;`）

```sass
.box
    width: 200px
    height: 200px
    border: 1px solid #333
    &:hover
        background-color: pink
```

b，`.scss` 是传统的语法形式【推荐使用】

```scss
.box {
    width: 200px;
    height: 200px;
    border: 1px solid #333;
    &:hover {
        background-color: pink;
    }
}
```

### 步骤

1. 安装解析 sass 的包：`yarn add sass`。

2. 创建全局样式文件 `index.scss`。

```scss
* {
    margin: 0;
    padding: 0;
    list-style: none;
}
html,
body,
#root {
    height: 100%;
}
```

3. 在 `index.js` 中引入 `index.scss`。

```js
import ReactDOM from 'react-dom'
import './index.scss'
import App from './App'

ReactDOM.render(<App />, document.querySelector('#root'))
```

## 配置基础路由

### 目标

能够配置登录和布局页面的路由。

<img src="/resource/images/ifer_geek_router.png"/>

### 步骤

1. 安装路由：`yarn add react-router-dom@5.3.0`。

2. 在 pages 目录中创建两个文件夹：Login、Layout、NotFound。

3. 分别在两个目录中创建 index.js 文件，并创建一个简单的组件后导出。

4. 在 App 组件中，导入路由组件以及两个页面组件。

5. 配置 Login 和 Layout 的路由规则。

`pages/Login/index.js`

```js
export default function Login() {
    return <div>登录界面</div>
}
```

`pages/Layout/index.js`

```js
export default function Layout() {
    return <div>布局界面</div>
}
```

`pages/NotFound/index.js`

```js
export default function NotFound() {
    return <div>NotFound</div>
}
```

`App.js`

```js
import { BrowserRouter as Router, Link, Route, Switch } from 'react-router-dom'
import Login from './pages/Login'
import Layout from './pages/Layout'
import NotFound from './pages/NotFound'

export default function App() {
    return (
        <Router>
            <div>
                <Link to='/login'>登录</Link>
                <Link to='/layout'>布局</Link>
                <Switch>
                    <Route path='/login' component={Login}></Route>
                    <Route path='/layout' component={Layout}></Route>
                    <Route component={NotFound} />
                </Switch>
            </div>
        </Router>
    )
}
```

### 优化

期望访问首页时 '/'，重定向到首页，[参考文档](https://v5.reactrouter.com/web/api/Redirect/exact-bool)。

```js
import { BrowserRouter as Router, Link, Route, Switch, Redirect } from 'react-router-dom'
import Login from './pages/Login'
import Layout from './pages/Layout'
import NotFound from './pages/NotFound'

export default function App() {
    return (
        <Router>
            <div>
                <Link to='/login'>登录</Link>
                {/* 为了地址栏的语义化，这里把 to 属性对应的值改成了 /home，同时出口 Route 的 path 属性也要改成 /home */}
                <Link to='/home'>布局</Link>
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

## 组件库 AntD

### 目标

能够使用 AntD 的 Button 组件渲染按钮。

### 步骤

[Ant Design](https://ant.design/index-cn) 是一套设计体系，基于这套体系有一个很成熟的 React UI 组件库，主要用于研发企业级中后台产品。

1. 安装 AntD 组件库：`yarn add antd`。

2. 在项目的入口文件全局导入 AntD 组件库的样式。

3. 导入 Button 组件。

4. 在 Login 页面渲染 Button 组件。

`src/index.js`

```js
import 'antd/dist/antd.css' // 先导入 antd 样式文件
import './index.scss' // 再导入全局样式文件，防止样式覆盖！
```

`pages/Login/index.js`

```js
import { Button } from 'antd'
export default function Login() {
    return (
        <div>
            <Button type='primary'>登录界面</Button>
        </div>
    )
}
```

<font color=#999>🤔 以上操作做完之后，控制台会看到警告，[解决](https://github.com/ant-design/ant-design/issues/33327)。</font>

```diff
-import 'antd/dist/antd.css'
+import 'antd/dist/antd.min.css'
```

### 总结

-   在哪个文件中导入 AntD 的样式文件？

-   AntD 的样式文件和我们自己的全局样式文件的导入顺序是什么？

## 配置路径别名

### 目标

能够配置 @ 路径别名简化路径处理。

### 内容

-   注意：CRA 将所有工程化配置，都隐藏在了 `react-scripts` 包中，所以，项目中看不到任何配置信息。

-   如果要修改 CRA 的默认配置，常见的有以下两种方案。

    a，根据[文档](https://ant.design/docs/react/use-with-create-react-app-cn#%E9%AB%98%E7%BA%A7%E9%85%8D%E7%BD%AE)通过第三方库来修改，比如，`@craco/craco`，<font color=e32d40>**推荐**</font>。

    b，通过执行 `yarn eject` 命令，释放 `react-scripts` 中的所有配置到项目中（注意：该操作不可逆）。

### 步骤

1. 安装修改 CRA 配置的包：`yarn add -D @craco/craco`。

2. 在项目根目录中创建 craco 的配置文件：`craco.config.js`，并在配置文件中配置路径别名。

```js
const path = require('path')
module.exports = {
    webpack: {
        alias: {
            '@': path.join(__dirname, 'src'),
        },
    },
}
```

3. 把 `package.json` 中的 `react-scripts` 替换为 `craco` 命令。

```json
{
    "scripts": {
        "start": "craco start",
        "build": "craco build",
        "test": "craco test",
        "eject": "react-scripts eject"
    }
}
```

4. 在代码中，就可以通过 `@` 来表示 src 目录的绝对路径。

5. 重启项目，让配置生效。

## @ 别名路径提示

### 目标

能够让 VSCode 识别 @ 路径并给出提示。

### 步骤

1. 在项目根目录创建 `jsconfig.json` 配置文件。

2. 在配置文件中添加以下配置。

```json
{
    "compilerOptions": {
        "baseUrl": "./",
        "paths": {
            "@/*": ["src/*"]
        }
    }
}
```

<font color=#999>🧐 如果 `/jsconfig.json` 文件报错，可以在 VSCode 的配置里面搜索 `jscon`，对 Check JS 选项进行打钩即可。</font>

### 总结

VSCode 会自动读取 `jsconfig.json` 中的配置，让 VSCode 知道 @ 就是 src 目录。

## 登录界面

<img src="/resource/images/ifer_login_box.png"/>

### 目标

能够搭建出登录页面的基础布局。

### 步骤

1. 在 `Login/index.js` 中创建登录页面基本结构。

2. 在 Login 目录中创建 `index.scss` 文件，指定组件样式。

3. 将 logo.png 和 login.png 拷贝到 assets 目录中。

### 代码

`pages/Login/index.js`

```js
import { Card } from 'antd'
import logo from '@/assets/logo.png'
import './index.scss'
export default function Login() {
    return (
        <div className='login'>
            <Card className='login-container'>
                <img className='login-logo' src={logo} alt='极客园' />
            </Card>
        </div>
    )
}
```

`pages/Login/index.scss`

```scss
.login {
    width: 100%;
    height: 100%;
    // 若父级没有定位，这里会相对于浏览器窗口
    // 学习资料：https://github.com/qianguyihao/Web/blob/master/02-CSS%E5%9F%BA%E7%A1%80/08-CSS%E5%B1%9E%E6%80%A7%EF%BC%9A%E5%AE%9A%E4%BD%8D%E5%B1%9E%E6%80%A7.md
    position: absolute;
    left: 0;
    top: 0;
    background: center/cover url(~@/assets/login.png);

    .login-logo {
        width: 200px;
        height: 60px;
        display: block;
        margin: 0 auto 20px;
    }

    .login-container {
        width: 440px;
        height: 360px;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        box-shadow: 0 0 50px rgb(0 0 0 / 10%);
    }

    .login-checkbox-label {
        color: #1890ff;
    }
}
```

### 总结

使用 Card 组件，可以用来实现对登录框内容的包裹。

## 表单结构

### 目标

能够使用 AntD 的 `Form` 组件创建登录表单。

### 步骤

1. 打开 AntD [Form 组件文档](https://ant.design/components/form-cn/)。

2. 找到代码演示的第一个示例（基本使用），点击`< >`（显示代码），并拷贝代码到组件中。

3. 分析 Form 组件基本结构。

4. 调整 Form 组件结构和样式。

### 代码

`pages/Login/index.js`

```js
import { Card, Form, Input, Button, Checkbox } from 'antd'
import logo from '@/assets/logo.png'
import './index.scss'
export default function Login() {
    return (
        <div className='login'>
            <Card className='login-container'>
                {/* Logo */}
                <img className='login-logo' src={logo} alt='极客园' />
                {/* 登录表单，注意 autoComplete 要想生效下面不要出现 password 框 */}
                <Form autoComplete='off' size='large'>
                    <Form.Item>
                        <Input placeholder='请输入手机号' />
                    </Form.Item>

                    <Form.Item>
                        <Input placeholder='请输入验证码' />
                    </Form.Item>

                    <Form.Item>
                        <Checkbox>我已阅读并同意[隐私条款]和[用户协议]</Checkbox>
                    </Form.Item>

                    <Form.Item>
                        <Button type='primary' htmlType='submit' block>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

### 总结

-   通过哪个属性调整 Input/Button 的大小？

-   Button 组件通过哪个属性设置为 submit 按钮？

## 表单校验

### 目标

能够为手机号和密码添加表单校验。

### 步骤

1. 为 Form 组件添加 `validateTrigger` 属性，指定校验触发时机的集合。

2. 为 Form.Item 组件添加 name 属性，这样表单校验才会生效，name 属性最好和[接口文档](http://geek.itheima.net/api-pc.html#)保持一致。

3. 为 Form.Item 组件添加 `rules` 属性，用来添加表单校验。

### 代码

`pages/Login/index.js`

```js
import { Card, Form, Input, Button, Checkbox } from 'antd'
import logo from '@/assets/logo.png'
import './index.scss'
export default function Login() {
    return (
        <div className='login'>
            <Card className='login-container'>
                {/* Logo */}
                <img className='login-logo' src={logo} alt='极客园' />
                {/* 默认 onChange 或者提交表单的时候会校验，也可以通过 validateTrigger 来指定其他触发校验的时机 */}
                <Form autoComplete='off' size='large' validateTrigger={['onBlur', 'onChange']}>
                    <Form.Item
                        name='mobile'
                        rules={[
                            { required: true, message: '请输入手机号' },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号码格式不对',
                            },
                        ]}
                    >
                        <Input placeholder='请输入手机号' />
                    </Form.Item>
                    {/* len: 当输入的内容为 string 类型时为字符串长度；number 类型时为确定数字； array 类型时为数组长度 */}
                    <Form.Item
                        name='code'
                        rules={[
                            { len: 6, message: '验证码6个字符' },
                            { required: true, message: '请输入验证码' },
                        ]}
                    >
                        <Input placeholder='请输入验证码' maxLength={6} />
                    </Form.Item>

                    <Form.Item>
                        <Checkbox>我已阅读并同意[隐私条款]和[用户协议]</Checkbox>
                    </Form.Item>

                    <Form.Item>
                        <Button type='primary' htmlType='submit' block>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

## 自定义校验

valuePropName，告诉需要拿的属性是 checked，而不是默认的 value，[参考文档](https://ant.design/components/form-cn/#components-form-demo-register)。

```js
<Form.Item
    name='agree'
    valuePropName='checked'
    rules={[
        {
            // 一上来是没有值的，提交会报错，ok
            // 勾选后，错误提示消失，提交也不报错 ok，因为有值 true
            // 取消勾选，错误提示没有再次出现（应该出现），提交也竟不报错了，有问题
            // 原因：因为这一次取消后 checked 属性也是有值的，只不过是 false
            required: true,
            message: '请阅读并同意协议',
        },
    ]}
>
    <Checkbox>我已阅读并同意[隐私条款]和[用户协议]</Checkbox>
</Form.Item>
```

所以，不是填不填写的问题，而是 true 和 false 的问题。

```jsx
<Form.Item
    name='agree'
    valuePropName='checked'
    rules={[
        {
            validator: (rule, value) => {
                if (value === true) {
                    return Promise.resolve()
                } else {
                    return Promise.reject(new Error('请阅读并同意条款和协议'))
                }
            },
        },
    ]}
>
    <Checkbox>我已阅读并同意[隐私条款]和[用户协议]</Checkbox>
</Form.Item>
```

## 获取和设置表单默认值

### 目标

能够拿到登录表单中的手机号码和验证码。

### 步骤

1. 为 Form 组件添加 `onFinish` 属性，该事件会在点击登录按钮时触发。

2. 创建 onFinish 函数，通过参数 values 拿到表单值。

3. 为了方便，为 Form 组件添加 `initialValues` 属性，来初始化表单值。

### 代码

`pages/Login/index.js`

```js
import { Card, Form, Input, Button, Checkbox } from 'antd'
import logo from '@/assets/logo.png'
import './index.scss'
export default function Login() {
    const onFinish = (values) => {
        console.log(values)
    }
    return (
        <div className='login'>
            <Card className='login-container'>
                {/* Logo */}
                <img className='login-logo' src={logo} alt='极客园' />
                {/* 默认 onChange 或者提交表单的时候会校验，也可以通过 validateTrigger 来指定其他触发校验的时机 */}
                <Form
                    autoComplete='off'
                    size='large'
                    validateTrigger={['onBlur', 'onChange']}
                    onFinish={onFinish}
                    initialValues={{
                        mobile: '13911111111',
                        code: '246810',
                        agree: true,
                    }}
                >
                    <Form.Item
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '请输入手机号',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号码格式不对',
                            },
                        ]}
                    >
                        <Input placeholder='请输入手机号' />
                    </Form.Item>

                    <Form.Item
                        name='code'
                        rules={[
                            { len: 6, message: '验证码6个字符' },
                            { required: true, message: '请输入验证码' },
                        ]}
                    >
                        <Input placeholder='请输入验证码' maxLength={6} />
                    </Form.Item>

                    <Form.Item
                        name='agree'
                        valuePropName='checked'
                        rules={[
                            {
                                validator: (rule, value) => {
                                    if (value === true) {
                                        return Promise.resolve()
                                    } else {
                                        return Promise.reject(new Error('请阅读并同意条款和协议'))
                                    }
                                },
                            },
                        ]}
                    >
                        <Checkbox>我已阅读并同意[隐私条款]和[用户协议]</Checkbox>
                    </Form.Item>

                    <Form.Item>
                        <Button type='primary' htmlType='submit' block>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

### 总结

-   如何获取到 Form 表单中的值？

-   通过哪个属性可以为 Form 表单初始化值？

## 配置 Redux

### 目标

能够完成 Redux 的基础配置。

### 步骤

1. 安装 Redux 相关的包：`yarn add redux react-redux redux-thunk redux-devtools-extension axios`。

2. 在 store 目录中分别创建：actions 和 reducers 文件夹、index.js 文件。

3. 在 `store/index.js` 中，创建 store 并导出。

4. 在 `reducers/index.js` 文件，创建 rootReducer 并导出。

5. 在 `reducers/login.js` 文件，创建基础 login reducer 并导出。

6. 在 `src/index.js` 中为 React 组件接入 Redux。

### 代码

store

```tree
store
|-- actions
|-- constants
|-- index.js
`-- reducers
    |-- index.js
    `-- login.js
```

1. `store/reducers/login.js`

```js
const initValue = {
    token: '',
}
export default function login(state = initValue, action) {
    return state
}
```

2. `store/reducers/index.js`

```js
import { combineReducers } from 'redux'

import login from './login'

const rootReducer = combineReducers({
    login,
})

export default rootReducer
```

3. `store/index.js`

```js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'

const middlewares = composeWithDevTools(applyMiddleware(thunk))
const store = createStore(rootReducer, middlewares)

export default store
```

4. `src/index.js`

```js
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import 'antd/dist/antd.css'
import './index.scss'
import store from './store'
import App from './App'

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
)
```

## 封装 Axios

1. 安装 axios

```bash
yarn add axios
```

2. `utils/request.js`

```jsx
import axios from 'axios'

const instance = axios.create({
    baseURL: 'http://geek.itheima.net/v1_0/',
    timeout: 5000,
})

// 添加请求拦截器
instance.interceptors.request.use(
    (config) => {
        // 在发送请求之前做些什么
        return config
    },
    (error) => {
        // 对请求错误做些什么
        return Promise.reject(error)
    }
)

// 添加响应拦截器
instance.interceptors.response.use(
    (response) => {
        // 对响应数据做点什么
        return response
    },
    (error) => {
        // 对响应错误做点什么
        return Promise.reject(error)
    }
)

export default instance
```

## 发起登录请求

### 目标

能够通过 Redux 实现登录功能。

### 步骤

1. 在 `store/constants/index.js` 文件中创建登录的 actionType。

2. 在 `store/actions/login.js` 文件中创建 actionCreator，并实现登录逻辑。

3. 在 `store/reducers/login.js` 文件中处理登录状态。

4. 在 `pages/Login/index.js` 组件中 dispatch 登录的异步 action。

### 代码

1. `store/constants/index.js`

```jsx
export const LOGIN = 'LOGIN'
```

2. `store/actions/login.js`

```js
import request from '@/utils/request'
import { LOGIN } from '../constants'

export const loginAc = (payload) => ({
    type: LOGIN,
    payload,
})

export const login = (payload) => {
    return async (dispatch) => {
        const res = await request({
            method: 'post',
            url: '/authorizations',
            data: payload,
        })
        dispatch(loginAc(res.data.data.token))
    }
}
```

3. `store/reducers/login.js`

```js
import { LOGIN } from '../constants'

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
    return state
}
```

4. `pages/Login/index.js`

```js
const onFinish = (values) => dispatch(login(values))
```

### 总结

登录业务在 Redux 中的执行流程是什么？

## 持久化 Token

### 目标

能够对 Token 进行持久化。

### 步骤

1. 在 `utils/token.js` 文件提供 `getToken`、`setToken`、`removeToken`、`isAuth` 四个工具函数并导出。

2. 在 `utils/index.js` 文件，统一导出 `token.js` 中的所有内容，来简化工具函数的导入。

3. 在 `store/actions/login.js` 文件，请求成功之后存储 Token。

4. 在 `store/index.js` 获取初始 Token。

### 代码

1. `utils/storage.js`

```js
const TOKEN_KEY = 'ITCAST_GEEK_PC'
export const getToken = () => localStorage.getItem(TOKEN_KEY)
export const setToken = (token) => localStorage.setItem(TOKEN_KEY, token)
export const removeToken = () => localStorage.removeItem(TOKEN_KEY)
export const isAuth = () => !!getToken()
```

2. `utils/index.js`

```js
export { getToken, setToken, removeToken, isAuth } from './storage'
```

3. `store/actions/login.js`

```js
import request from '@/utils/request'
import { LOGIN } from '../constants'
import { setToken } from '@/utils'

export const loginAc = (payload) => ({
    type: LOGIN,
    payload,
})

export const login = (payload) => {
    return async (dispatch) => {
        const res = await request({
            method: 'post',
            url: '/authorizations',
            data: payload,
        })
        const token = res.data.data.token
        // mark
        setToken(token)
        dispatch(loginAc(token))
    }
}
```

4. `store/index.js`

```js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
import { getToken } from '@/utils'
// 3 个参数：reducer、可选的对象，可以用于提供初始值、提供中间件
const store = createStore(
    rootReducer,
    {
        login: {
            token: getToken(),
        },
    },
    composeWithDevTools(applyMiddleware(thunk))
)

export default store
```

## 登录状态提示

登录成功后，提示消息，并且跳转到首页。

`pages/Login/index.js`

```jsx
const onFinish = async (values) => {
    try {
        await dispatch(login(values))
        message.success('登录成功', 1, () => {
            history.push('/home')
        })
    } catch (e) {
        message.error(e.response.data.message, 1)
    }
}
```

🤔 问题：把网络改成 Offline，点击登录按钮，却没有报错提示（或者网络很慢的情况下，可以改成 Slow 3G 并把超时时间改的很短来模拟这种情况），解决如下。

```js
message.error(e.response?.data?.message || '登录失败', 1)
```

## 给按钮增加 loading

1. 提供一个状态 loading。

```jsx
const [loading, setLoading] = useState(false)
```

2. 发送请求，修改 loading 状态。

```jsx
const onFinish = async (values) => {
    setLoading(true)
    try {
        await dispatch(login(values))
        message.success('登录成功', 1, () => {
            // setLoading(false) // 跳走了，所以这一行不加也没关系
            history.push('/home')
        })
    } catch (e) {
        message.error(e.response.data.message, 1, () => {
            setLoading(false)
        })
    }
}
```

3. 使用 loading 状态。

```jsx
<Button type='primary' htmlType='submit' block loading={loading}>
    登录
</Button>
```
