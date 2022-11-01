---
title: 16_极客园 H5
date: 2021-12-24 16:21:53
tags:
---

## 今日目标

✔ 掌握登录的完整流程。

<!-- more -->

## 登录界面

### 目标

能够手动搭建登录界面的效果。

### 步骤

1. 完成登录界面的导航栏，[NavBar 组件](https://mobile.ant.design/zh/components/nav-bar)。

```jsx
import styles from './index.module.scss'
import { NavBar } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
export default function Login() {
    const history = useHistory()
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
        </div>
    )
}
```

2. 添加登录表单，[Form 组件](https://mobile.ant.design/zh/components/form)。

```jsx
import styles from './index.module.scss'
import { NavBar, Form, Input, List, Button } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
export default function Login() {
    const history = useHistory()
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form>
                    {/* 用户名 */}
                    <Form.Item className='login-item'>
                        <Input placeholder='请输入用户名' />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item className='login-code-extra' extra={<span className='code-extra'>发送验证码</span>}>
                        <Form.Item className='login-item'>
                            <Input placeholder='请输入验证码' />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

`login/index.module.scss`

```scss
// 导入 1px 边框的样式文件
@import '@scss/hairline.scss';

.root {
    :global {
        .login-form {
            padding: 0 33px;
            .title {
                margin: 54px 0 13px 0;
            }

            .adm-list {
                --align-items: end !important;
            }
            .adm-list-default {
                border: none;
            }
            .adm-list-item {
                padding: 0;
            }
            .login-item {
                // 注意：因为 1px 边框样式使用了决定定位，所以，此处需要将设置为相对定位
                position: relative;
                // 为该元素设置 1px 边框
                @include hairline(bottom, #f0f0f0);

                > .adm-list-item-content {
                    height: 70px;
                }
            }
            .adm-list-item-content {
                position: relative;
                border-bottom: none;
            }
            // 验证码
            .login-code-extra {
                > .adm-list-item-content {
                    align-items: center;
                    padding: 0;
                }
                .adm-list-item-content-extra {
                    position: absolute;
                    right: 0;
                    bottom: 16px;
                }
            }
            .adm-input-wrapper {
                --placeholder-color: #a5a6ab;
            }
            .code-extra {
                color: #595769;
                font-size: 14px;

                &-disabled {
                    color: #a5a6ab;
                }
            }
            .adm-list-item-description {
                position: absolute;
                bottom: -25px;
            }

            .login-submit {
                height: 50px;
                margin-top: 38px;
                border: none;
                font-size: 16px;
                background: linear-gradient(315deg, #fe4f4f, #fc6627);
            }
        }
    }
}
```

## 表单校验

### 目标

能够为登录表单添加校验。

### 代码

`login/index.tsx`，给 Form.Item 组件添加 name 和 rules 属性。

```tsx
import styles from './index.module.scss'
import { NavBar, Form, Input, List, Button } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
export default function Login() {
    const history = useHistory()
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form>
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入用户名' />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item className='login-code-extra' extra={<span className='code-extra'>发送验证码</span>}>
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

## 获取登录表单数据

### 目标

能够拿到手机号和验证码数据。

### 步骤

1. 为 Form 表单添加 onFinish 事件。

2. 创建 onFinish 函数，作为 Form 属性 onFinish 事件的回调。

3. 指定函数 onFinish 的参数类型。

4. 通过参数获取到表单数据。

### 代码

`login/index.tsx`

```jsx
import styles from './index.module.scss'
import { NavBar, Form, Input, List, Button } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
// #3
type LoginForm = { mobile: string, code: string }
export default function Login() {
    const history = useHistory()
    // #2
    const onFinish = (values: LoginForm) => {
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                {/* #1 */}
                <Form onFinish={onFinish}>
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入用户名' />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item className='login-code-extra' extra={<span className='code-extra'>发送验证码</span>}>
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

## axios 封装

1. 安装 axios。

```bash
yarn add axios
```

2. `utils/request.ts`。

```ts
import axios from 'axios'

const instance = axios.create({
    baseURL: 'http://geek.itheima.net/v1_0/',
    timeout: 5000,
})

instance.interceptors.request.use(
    function (config) {
        return config
    },
    function (error) {
        return Promise.reject(error)
    }
)

instance.interceptors.response.use(
    function (response) {
        return response
    },
    function (error) {
        return Promise.reject(error)
    }
)

export default instance
```

## Redux 初始化

### 目标

能够初始化 Redux。

### 步骤

1. 安装 Redux 相关的包。

```bash
yarn add redux react-redux redux-thunk@2.3.0 redux-devtools-extension
```

2. 在 store 目录中分别创建：actions 和 reducers 文件夹、index.ts 文件。

3. 创建 `reducers/login.ts` 文件，创建基础 login reducer 并导出。

```ts
const initialState = {}
const login = (state = initialState, action: any) => {
    return state
}
export default login
```

4. 创建 `reducers/index.ts` 文件，创建 rootReducer 并导出。

```ts
import { combineReducers } from 'redux'
import login from './login'
export default combineReducers({ login })
```

5. 在 `store/index.ts` 中，创建 store 并导出。

```ts
import { applyMiddleware, createStore } from 'redux'
import { composeWithDevTools } from 'redux-devtools-extension'
import thunk from 'redux-thunk'
import reducers from './reducers'
export default createStore(reducers, composeWithDevTools(applyMiddleware(thunk)))
```

6. 在 `src/index.tsx` 中为 React 组件接入 Redux。

```jsx
import ReactDOM from 'react-dom'
import './index.scss'
import App from './App'
import { Provider } from 'react-redux'
import store from './store'

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
)
```

## 配置 Redux 的相关类型

### 目标

能够配置 Redux 的基础类型。

### 步骤

1. 在 types 目录中创建两个类型声明文件：`store.d.ts` 和 `data.d.ts`。

2. `store.d.ts`：用来存放跟 Redux 相关类型，比如，action 的类型等。

3. `data.d.ts`：用来存放跟数据接口相关类型。

### 代码

`src/types/store.d.ts`

```ts
// 存放和 Redux 相关的所有类型
// RootState
// RootAction
// RootThunkAction
// 各个模块的 Action

import store from '@/store'
import { ThunkAction } from 'redux-thunk'

// store 的 state 的类型
export type RootState = ReturnType<typeof store.getState>
// 所有的 action 的类型
export type RootAction = LoginAction
// thunkAction 类型
export type RootThunkAction = ThunkAction<void, RootState, unknown, RootAction>
// 各个默认的 action
export type LoginAction = {
    type: 'login/login'
}
```

`src/types/data.d.ts`

```ts
// 存放各种通用的数据
export type LoginForm = { mobile: string; code: string }
```

`src/store/actions/login.ts`

```jsx
import { LoginForm } from '@/types/data'
import { RootThunkAction } from '@/types/store'

export const login = (values: LoginForm): RootThunkAction => {
    return (dispatch) => {}
}
```

`src/pages/Login/index.tsx`

```jsx
import styles from './index.module.scss'
import { NavBar, Form, Input, List, Button } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
// #1
import { LoginForm } from '@/types/data'
// type LoginForm = { mobile: string; code: string }
export default function Login() {
    const history = useHistory()
    // #2
    const onFinish = (values: LoginForm) => {
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form onFinish={onFinish}>
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入用户名' />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item className='login-code-extra' extra={<span className='code-extra'>发送验证码</span>}>
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

## 发送登录请求

### 目标

能够在 Redux 中实现登录逻辑。

### 步骤

1. 在 `store/actions` 中创建 `login.ts` 文件。

2. 创建 login 函数并导出。

3. 在函数中根据接口发送请求实现登录功能。

### 代码

`store/actions/login.ts`

```ts
import { LoginForm } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post('/authorizations', values)
        console.log(res)
    }
}
```

`pages/Login/index.tsx`

```jsx
import styles from './index.module.scss'
import { NavBar, Form, Input, List, Button } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
// #1
import { useDispatch } from 'react-redux'
import { LoginForm } from '@/types/data'
// #2
import { login } from '@/store/actions/login'
export default function Login() {
    const history = useHistory()
    // #3
    const dispatch = useDispatch()
    const onFinish = (values: LoginForm) => {
        // #4
        dispatch(login(values))
    }
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form onFinish={onFinish}>
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入用户名' />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item className='login-code-extra' extra={<span className='code-extra'>发送验证码</span>}>
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

## 处理 Axios 的响应的类型

### 基本处理

`src/types/data.d.ts`

```ts
export type LoginForm = { mobile: string; code: string }
export type Token = {
    token: string
    refresh_token: string
}
```

`src/store/actions/login.ts`

```ts
import { LoginForm, Token } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

type LoginResponse = {
    message: string
    data: Token
}
export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post<LoginResponse>('/authorizations', values)
        console.log(res.data.data.refresh_token)
    }
}
```

### 继续封装

`types/data.d.ts`

```jsx
export type LoginForm = { mobile: string; code: string }

export type ApiResponse<T> = {
  message: string
  data: T
}
export type Token = {
  token: string
  refresh_token: string
}
```

`actions/login.ts`

```tsx
import { ApiResponse, LoginForm, Token } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post<ApiResponse<Token>>('/authorizations', values)
        console.log(res.data.data.refresh_token)
    }
}
```

## 处理 Action

-   `types/store.d.ts`

```ts
import store from '@/store'
import { ThunkAction } from 'redux-thunk'
import { Token } from './data'

// store 的 state 的类型
export type RootState = ReturnType<typeof store.getState>
// 所有的 action 的类型
export type RootAction = LoginAction
// thunkAction 类型
export type RootThunkAction = ThunkAction<void, RootState, unknown, RootAction>
// 各个默认的 action
export type LoginAction = {
    type: 'login/login'
    // #1
    payload: Token
}
```

-   `store/actions/login.ts`

```ts
import { ApiResponse, LoginForm, Token } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post<ApiResponse<Token>>('/authorizations', values)
        // #2
        dispatch({
            type: 'login/login',
            payload: res.data.data,
        })
    }
}
```

-   `store/reducers/login.ts`

```ts
import { Token } from '@/types/data'
import { LoginAction } from '@/types/store'
/* const initState: Token = {
    token: '',
    refresh_token: '',
} */
// #3
// 初始值多的情况下建议下面写法
const initState: Token = {} as Token
// #4
const login = (state = initState, action: LoginAction) => {
    // #5
    if (action.type === 'login/login') {
        return action.payload
    }
    return state
}
export default login
```

## 登录失败后处理

`pages/Login/index.tsx`

```ts
const onFinish = async (values: LoginForm) => {
    try {
        await dispatch(login(values))
    } catch (e) {
        // error: Object is of type 'unknown'
        console.log(e.response.data.message)
    }
}
```

解决报错。

```ts
const onFinish = async (values: LoginForm) => {
    try {
        await dispatch(login(values))
    } catch (e: any) {
        console.log(e.response.data.message)
    }
}
```

但是输入 `e.response.data.message` 的时候没有提示，可能会思考给 e 加上 axios 的错误类型，如下。

```ts
const onFinish = async (values: LoginForm) => {
    try {
        await dispatch(login(values))
    } catch (e: AxiosError) {
        // error: Catch clause variable type annotation must be 'any' or 'unknown' if specified
        console.log(e.response.data.message)
    }
}
```

类型断言。

```ts
const onFinish = async (values: LoginForm) => {
    try {
        await dispatch(login(values))
    } catch (e) {
        const error = e as AxiosError
        console.log(error.response?.data.message)
    }
}
```

问题：data.message 没有提示，解决如下。

```ts
const onFinish = async (values: LoginForm) => {
    try {
        await dispatch(login(values))
    } catch (e) {
        const error = e as AxiosError<{ message: string }>
        console.log(error.response?.data.message)
    }
}
```

## 统一错误处理

`src/utils/request.ts`

```ts
request.interceptors.response.use(
    function (response) {
        return response
    },
    function (error: AxiosError<{ message: string }>) {
        if (!error.response) {
            // Network Error
            Toast.show('网络繁忙，请稍后重试')
            return Promise.reject(error)
        }
        Toast.show(error.response.data.message)
        return Promise.reject(error)
    }
)
```

## 登录成功后处理

`pages/Login/index.tsx`

```ts
const onFinish = async (values: LoginForm) => {
    await dispatch(login(values))
    Toast.show({
        content: '登录成功',
        icon: 'success',
        duration: 600,
        afterClose() {
            history.push('/home')
        },
    })
}
```

## 存储 Token

### 目标

能够封装 Token 存储相关的操作。

### 内容

`src/utils/storage.ts`

```ts
import { Token } from '@/types/data'

const TOKEN_KEY = 'GEEK_H5'
/**
 * 存
 */
export function setToken(token: Token): void {
    localStorage.setItem(TOKEN_KEY, JSON.stringify(token))
}

/**
 * 取
 */
export function getToken(): Token {
    // localStorage.getItem(TOKEN_KEY) => string | null
    // 可以断言不为空，即便有可能为 null 时，JSON.parse(null) 也能正常处理
    // return JSON.parse(localStorage.getItem(TOKEN_KEY)!)
    // 保证返回的是一个对象
    // return JSON.parse(localStorage.getItem(TOKEN_KEY)!) || {}
    // 下面写法也 ok
    return JSON.parse(localStorage.getItem(TOKEN_KEY) || '{}')
}

/**
 * 删
 */
export function removeToken(): void {
    localStorage.removeItem(TOKEN_KEY)
}

/**
 * 判断是否有 Token
 */
export function hasToken(): boolean {
    // 注意 getToken() 至少返回的是 {}，而 !!{} 其实也是 true，所以...
    return !!getToken().token
}
```

## 持久化 Token

### 目标

能够实现刷新页面时在 Redux 状态中拿到 Token。

### 分析

问题：登录成功后，Redux 状态中有 Token 值，但是刷新页面后，Redux 中 Token 值没有了。

原因：只在登录时，将 Token 存储到 Redux 状态中，没有处理刷新的情况。

### 步骤

1. 登录成功时存储 Token 到本地。

2. 在 `store/reducers/login.ts` 中获取 Token。

`store/actions/login.ts`

```ts
import { ApiResponse, LoginForm, Token } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'
import { setToken } from '@/utils/storage'

export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post<ApiResponse<Token>>('/authorizations', values)
        dispatch({
            type: 'login/login',
            payload: res.data.data,
        })
        // !存到本地
        setToken(res.data.data)
    }
}
```

`store/reducers/login.ts`

```ts
import { Token } from '@/types/data'
import { LoginAction } from '@/types/store'
import { getToken } from '@/utils/storage'
// !mark
const initState: Token = getToken()
const login = (state = initState, action: LoginAction) => {
    if (action.type === 'login/login') {
        return action.payload
    }
    return state
}
export default login
```

## 发送验证码-校验手机号

### 目标

能够实现点击发送验证码时获取到手机号码，[参考文档](https://www.npmjs.com/package/rc-field-form)。

### 步骤

1. 给发送验证码绑定点击事件。

2. 在点击事件中获取到文本框的值。

3. 判断文本框的值是否为空。

4. 如果为空或手机号格式错误时，让文本框自动获得焦点。

### 代码

`pages/Login/index.tsx`

```ts
import styles from './index.module.scss'
import { useRef } from 'react'
import { NavBar, Form, Input, List, Button, Toast } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import { useDispatch } from 'react-redux'
import { LoginForm } from '@/types/data'
import { login } from '@/store/actions/login'
import { InputRef } from 'antd-mobile/es/components/input'
export default function Login() {
    const history = useHistory()
    const dispatch = useDispatch()
    const [form] = Form.useForm()
    const mobileRef = useRef<InputRef>(null)
    const onFinish = async (values: LoginForm) => {
        await dispatch(login(values))
        Toast.show({
            content: '登录成功',
            icon: 'success',
            duration: 600,
            afterClose() {
                history.push('/home')
            },
        })
    }
    const onGetCode = () => {
        // 获取手机号
        const mobile = form.getFieldValue('mobile')
        // 获取校验信息
        const error = form.getFieldError('mobile')
        if (!mobile || error.length > 0) {
            mobileRef.current?.focus()
            return
        }
    }
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form
                    form={form}
                    onFinish={onFinish}
                    initialValues={{
                        mobile: '13911111111',
                        code: '246810',
                    }}
                >
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入手机号' ref={mobileRef} maxLength={11} />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item
                        className='login-code-extra'
                        extra={
                            <span onClick={onGetCode} className='code-extra'>
                                发送验证码
                            </span>
                        }
                    >
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

## 发送验证码-发送请求

### 目标

能够使用 Redux 发送请求获取验证码。

### 步骤

1. 在 Login 组件中导入获取验证码的 action。

2. 在获取验证码事件中分发获取验证码的 action。

3. 在 login action 中创建获取验证码的 action 并导出。

4. 发送请求获取验证码。

### 代码

`store/actions/login.ts`

```tsx
import { ApiResponse, LoginForm, Token } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'
import { setToken } from '@/utils/storage'

export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post<ApiResponse<Token>>('/authorizations', values)
        dispatch({
            type: 'login/login',
            payload: res.data.data,
        })
        // !存到本地
        setToken(res.data.data)
    }
}

export function getCode(mobile: string) {
    return async () => {
        // 验证码是发送到手机上的，不必更新 Redux 状态
        await request.get(`/sms/codes/${mobile}`)
    }
}
```

`pages/Login/index.tsx`

```ts
const onGetCode = () => {
    // 获取手机号
    const mobile = form.getFieldValue('mobile')
    // 获取校验信息
    const error = form.getFieldError('mobile')
    if (!mobile || error.length > 0) {
        mobileRef.current?.focus()
        return
    }
    dispatch(getCode(mobile))
}
```

## 发送验证码-开启倒计时

### 目标

能够在点击获取验证码时显示倒计时。

### 步骤

1. 创建状态 timeLeft 倒计时数据。

2. 在点击获取验证码的事件处理程序中，更新倒计时时间并开启定时器。

3. 在定时器中，更新状态（需要使用回调函数形式的 setTimeLeft）。

4. 在开启定时器时，展示倒计时时间。

### 代码

`pages/Login/index.tsx`

```ts
import styles from './index.module.scss'
import { useRef, useState } from 'react'
import { NavBar, Form, Input, List, Button, Toast } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import { useDispatch } from 'react-redux'
import { LoginForm } from '@/types/data'
import { getCode, login } from '@/store/actions/login'
import { InputRef } from 'antd-mobile/es/components/input'
export default function Login() {
    const history = useHistory()
    const dispatch = useDispatch()
    const [form] = Form.useForm()
    // !#1: 初始化状态
    const [time, setTime] = useState(0)
    const mobileRef = useRef<InputRef>(null)
    const onFinish = async (values: LoginForm) => {
        await dispatch(login(values))
        Toast.show({
            content: '登录成功',
            icon: 'success',
            duration: 600,
            afterClose() {
                history.push('/home')
            },
        })
    }
    const onGetCode = () => {
        // 获取手机号
        const mobile = form.getFieldValue('mobile')
        // 获取校验信息
        const error = form.getFieldError('mobile')
        if (!mobile || error.length > 0) {
            mobileRef.current?.focus()
            return
        }
        dispatch(getCode(mobile))
        // !#3 设置状态
        setTime(5)
        // !#4 倒计时
        setInterval(() => {
            setTime(time - 1)
        }, 1000)
    }
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form
                    form={form}
                    onFinish={onFinish}
                    initialValues={{
                        mobile: '13911111111',
                        code: '246810',
                    }}
                >
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入手机号' ref={mobileRef} maxLength={11} />
                    </Form.Item>
                    {/* 验证码 */}
                    {/* //!#2: 视图判断 */}
                    <List.Item
                        className='login-code-extra'
                        extra={
                            <span onClick={onGetCode} className='code-extra'>
                                {time === 0 ? '发送验证码' : `${time}s后再次发送`}
                            </span>
                        }
                    >
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' maxLength={6} />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

## 发送验证码-获取最新 time 值

-   回调的写法。

```ts
setInterval(() => {
    setTime((time) => time - 1)
}, 1000)
```

-   通过 useRef。

```ts
export default function Login() {
    const history = useHistory()
    const dispatch = useDispatch()
    const [form] = Form.useForm()
    const [time, setTime] = useState(0)
    // !#1
    const timeRef = useRef(0)
    const mobileRef = useRef<InputRef>(null)
    const onFinish = async (values: LoginForm) => {
        await dispatch(login(values))
        Toast.show({
            content: '登录成功',
            icon: 'success',
            duration: 600,
            afterClose() {
                history.push('/home')
            },
        })
    }
    const onGetCode = () => {
        // 获取手机号
        const mobile = form.getFieldValue('mobile')
        // 获取校验信息
        const error = form.getFieldError('mobile')
        if (!mobile || error.length > 0) {
            mobileRef.current?.focus()
            return
        }
        dispatch(getCode(mobile))
        setTime(5)
        // !#2
        timeRef.current = 5
        setInterval(() => {
            // !#3
            timeRef.current--
            // !#4
            setTime(timeRef.current)
        }, 1000)
    }
}
```

## 发送验证码-清理定时器

### 倒计时结束清理定时器

1. 通过 useRef Hook 创建一个 ref 对象，用来存储定时器 id。

2. 在开启定时器时，将定时器 id 存储到 ref 对象中。

3. 通过 useEffect Hook 监听倒计时的变化。

4. 判断倒计时时间是否为 0 ，如果为 0 就清理定时器。

```ts
import styles from './index.module.scss'
import { useRef, useState, useEffect } from 'react'
import { NavBar, Form, Input, List, Button, Toast } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import { useDispatch } from 'react-redux'
import { LoginForm } from '@/types/data'
import { getCode, login } from '@/store/actions/login'
import { InputRef } from 'antd-mobile/es/components/input'
export default function Login() {
    const history = useHistory()
    const dispatch = useDispatch()
    const [form] = Form.useForm()
    const [time, setTime] = useState(0)
    const mobileRef = useRef<InputRef>(null)
    // !#1
    const timerRef = useRef(-1)
    const onFinish = async (values: LoginForm) => {
        await dispatch(login(values))
        Toast.show({
            content: '登录成功',
            icon: 'success',
            duration: 600,
            afterClose() {
                history.push('/home')
            },
        })
    }
    const onGetCode = () => {
        // 获取手机号
        const mobile = form.getFieldValue('mobile')
        // 获取校验信息
        const error = form.getFieldError('mobile')
        if (!mobile || error.length > 0) {
            mobileRef.current?.focus()
            return
        }
        dispatch(getCode(mobile))
        setTime(5)
        // !#2
        timerRef.current = window.setInterval(() => {
            setTime((time) => time - 1)
        }, 1000)
    }
    // !#3
    useEffect(() => {
        if (time === 0) {
            clearInterval(timerRef.current)
        }
    }, [time])
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)} />
            <div className='login-form'>
                <h2 className='title'>账号登录</h2>
                <Form
                    form={form}
                    onFinish={onFinish}
                    initialValues={{
                        mobile: '13911111111',
                        code: '246810',
                    }}
                >
                    {/* 用户名 */}
                    <Form.Item
                        className='login-item'
                        name='mobile'
                        rules={[
                            {
                                required: true,
                                message: '手机号不能为空',
                            },
                            {
                                pattern: /^1[3-9]\d{9}$/,
                                message: '手机号格式错误',
                            },
                        ]}
                    >
                        <Input placeholder='请输入手机号' ref={mobileRef} maxLength={11} />
                    </Form.Item>
                    {/* 验证码 */}
                    <List.Item
                        className='login-code-extra'
                        extra={
                            <span onClick={onGetCode} className='code-extra'>
                                {time === 0 ? '发送验证码' : `${time}s后再次发送`}
                            </span>
                        }
                    >
                        <Form.Item
                            className='login-item'
                            name='code'
                            rules={[
                                {
                                    required: true,
                                    message: '验证码不能为空',
                                },
                                {
                                    pattern: /^\d{6}$/,
                                    message: '验证码格式错误',
                                },
                            ]}
                        >
                            <Input placeholder='请输入验证码' maxLength={6} />
                        </Form.Item>
                    </List.Item>
                    {/* 登录按钮 */}
                    <Form.Item>
                        <Button color='primary' block className='login-submit' type='submit'>
                            登录
                        </Button>
                    </Form.Item>
                </Form>
            </div>
        </div>
    )
}
```

### 倒计时的时候再点击不要做处理

```js
const onGetCode = () => {
    // 倒计时的时候再点不做处理
    if (time > 0) return
}
```

### 组件销毁的时候清理定时器

问题：倒计时期间，登录成功后会报错。

```js
// 单独写一个 useEffect
useEffect(() => {
    return () => {
        clearInterval(timerRef.current)
    }
}, [])
```
