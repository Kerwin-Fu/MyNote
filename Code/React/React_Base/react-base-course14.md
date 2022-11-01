---
title: 14_TS 进阶
date: 2021-12-20 15:27:19
tags:
---

## 今日目标

✔ 掌握在项目中使用 TypeScript。

<!-- more -->

## useState

### 目标

掌握 useState Hook 配合 TypeScript 使用。

### 内容

-   `useState` 接收一个泛型参数，<font color=e32d40>**用于指定初始值的类型**</font>。

-   `useState` 的使用。

```ts
const [name, setName] = useState<string>('张三')
const [age, setAge] = useState<number>(28)
const [isProgrammer, setIsProgrammer] = useState<boolean>(true)

// 如果你在 setName 函数中的参数不符合声明的变量类型，程序会报错
<button onClick={() => setName(100)}>按钮</button>
```

-   `useState` 的类型推断，在使用 useState 的时候，只要提供了初始值，TypeScript 会自动根据初始值进行类型推断，因此 `useState` 的泛型参数可以省略。

```ts
export default function App() {
    const [name, setName] = useState('张三')
    const [age, setAge] = useState(28)
    const [isProgrammer, setIsProgrammer] = useState(true)
    return (
        <div>
            <button onClick={() => setName(100)}>按钮</button>
        </div>
    )
}
```

## useEffect

### 目标

掌握 useEffect Hook 在 TypeScript 中的使用。

### 内容

-   `useEffect` 是用于我们管理副作用（例如 API 调用）并在组件中使用 React 生命周期的。

-   `useEffect` 函数不涉及到任何泛型参数，在 TS 中的使用和 JS 中完全一致。

```ts
// 定时器
useEffect(() => {
    let timer = setInterval(() => {
        console.log('哈哈哈')
    })
    return () => {
        clearInterval(timer)
    }
}, [])
```

```ts
// 绑定事件
useEffect(() => {
    // 给 window 绑定点击事件
    const handleClick = () => {
        console.log('哈哈哈')
    }
    window.addEventListener('click', handleClick)

    return () => {
        // 给 window 移除点击事件
        window.addEventListener('click', handleClick)
    }
}, [])
```

## 📝 请求数据

### 目标

能够使用 useEffect 发送请求并掌握 useState 的进阶用法。

### 内容

-   频道列表接口：`http://geek.itheima.net/v1_0/channels`。

-   需求：发送请求获取频道列表数据，并且渲染。

-   注意：如果 useState 没有提供具体类型的初始值，是需要使用泛型参数指定类型的。

```jsx
// 存放频道列表数据
const [list, setList] = useState([])
```

![ifer_useState](/resource/images/ifer_useState.jpg)

-   关于 never 类型。

```ts
// 此时 Custom 就是 never 类型，不可能实现的类型
type Custom = number & string
```

-   如果 useState 的初始值是一个复杂的数据类型，需要给 useState 指定泛型参数。

```ts
import { useEffect, useState } from 'react'
import axios from 'axios'
// 定义类型别名 Res
type Res = { id: number; name: string }[]
export default function App() {
    // 解决1：给个初始值，不推荐
    // const [list, setList] = useState([{ name: 'ifer', id: 0 }])
    // 解决2：泛型参数
    // 一般复杂的类型，需要手动进行指定初始值类型，TS 没法进行推断
    const [list, setList] = useState<Res>([])
    useEffect(() => {
        const fetchData = async () => {
            const res = await axios.get('http://geek.itheima.net/v1_0/channels')
            setList(res.data.data.channels)
        }
        fetchData()
    }, [])
    return (
        <ul>
            {list.map((item) => {
                return <li key={item.id}>{item.name}</li>
            })}
        </ul>
    )
}
```

## useRef

### 目标

能够使用 useRef 配合 TS 操作 DOM。

### 内容

-   `useRef` 接收一个泛型参数，<font color=e32d40>**泛型参数用于指定 current 属性的值的类型**</font>。

-   如果使用 useRef 操作 DOM，需要明确指定所操作的 DOM 的具体的类型，否则 current 属性会是 null。

-   需求：获取 input 的 value 和获取 a 标签的 href。

```ts
import { useRef } from 'react'
export default function App() {
    // 不推荐 any
    // const inputRef = useRef<any>(null)
    const inputRef = useRef<HTMLInputElement>(null)
    const aRef = useRef<HTMLAnchorElement>(null)
    const get = () => {
        // inputRef.current 可能是 null，所以用了 ?.
        console.log(inputRef.current?.value)
        console.log(aRef.current?.href)
    }
    return (
        <div>
            <input type='text' ref={inputRef} />
            <a href='https://www.baidu.com' ref={aRef}>
                百度
            </a>
            <button onClick={get}>获取</button>
        </div>
    )
}
```

-   技巧：如何获取一个 DOM 对象的类型，鼠标直接移动到该元素上，就会显示出来该元素的类型。

![ifer_useRef2](/resource/images/ifer_useRef2.png)

## 可选链操作符

### 目标

掌握 JS 中提供的可选链操作符语法。

### 内容

可选链操作符（`?.`）允许读取位于连接对象链深处的属性值，而不必明确验证链中的每个引用是否有效，[参考文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Optional_chaining)。

```ts
// 能在保证有 obj.first 的情况下采取获取 second 属性，没有的话也不至于报错（会返回 undefined）
let nestedProp = obj.first?.second
// 等价于
let nestedProp = obj.first === null || obj.first === undefined ? undefined : obj.first.second
```

## 非空断言

### 目标

掌握 TS 中的非空断言的使用语法。

### 内容

-   如果我们明确的知道对象的属性一定不会为空，那么可以使用非空断言 `!`。

-   注意：非空断言一定要确保有该属性才能使用，不然使用非空断言会导致 Bug。

```ts
// 告诉 TS，明确的指定 obj 不可能为空
let nestedProp = obj!.second
```

```ts
import { useRef } from 'react'
export default function App() {
    const inputRef = useRef<HTMLInputElement>(null)
    const get = () => {
        // 断言 inputRef.current 不可能为空
        /* const current = inputRef.current!
        console.log(current.value) */
        console.log(inputRef.current!.value)
    }
    return (
        <div>
            <input type='text' ref={inputRef} />
            <button onClick={get}>获取</button>
        </div>
    )
}
```

## React 路由

### 目标

能够在 TypeScript 中使用 React 路由。

### 内容

<img src="/resource/images/ifer_router_ts.png"/>

-   安装：`yarn add react-router-dom@5.3.0 @types/react-router-dom`。

-   在 `src/pages` 目录，新建组件 `Home.tsx` 和 `Login.tsx`，在 `App.tsx` 中配置如下。

```jsx
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'
import Login from './pages/Login'
import Home from './pages/Home'
export default function App() {
    return (
        <Router>
            <div>
                <nav>
                    <Link to='/login'>登录</Link>
                    <Link to='/home'>首页</Link>
                </nav>
                {/* 有了 TS 的支持后，代码提示变得非常的精确 */}
                <Route path='/login' component={Login} />
                <Route path='/home' component={Home} />
            </div>
        </Router>
    )
}
```

## useHistory

### 目标

掌握 useHistory 在 TypeScript 中的使用。

### 内容

-   useHistory 可以实现路由之间的跳转，并且在跳转时可以指定跳转参数 state 的类型。

-   useHistory 如果仅仅实现跳转功能，和 JS 中使用语法一致，例如期望从`首页`跳转到`登录页`。

```jsx
// /pages/Home.tsx
import { useHistory } from 'react-router-dom'

export default function Home() {
    const history = useHistory()
    const login = () => {
        history.push('/login')
    }
    return (
        <div>
            <h2>Home</h2>
            <button onClick={login}>登录</button>
        </div>
    )
}
```

-   useHistory 可以通过泛型参数来指定 state 的类型。

```ts
const history = useHistory<{ from: string }>()
const login = () => {
    history.push({
        pathname: '/login',
        state: {
            from: 'ifer',
        },
    })
}
```

<font color=909090>🧐 分析如下，先了解即可。</font>

```ts
// 点击 useHistory 可以发现类型定义的源码
// 参数：HistoryLocationState
// 返回：H.History<HistoryLocationState>

// 点击 H：import * as H from 'history';

// 点进去 history

// 看到 HistoryLocationState 给了 LocationDescriptor<HistoryLocationState>，通过观察可以发现，也可以通过第二个参数进行 state 的参数传递，如下：
// push(location: Path | LocationDescriptor<HistoryLocationState>, state?: HistoryLocationState): void;

// 点击 LocationDescriptor，如下：
// export type LocationDescriptor<S = LocationState> = History.LocationDescriptor<S>;

// 点击 LocationDescriptor，如下：
// export type LocationDescriptor<S = LocationState> = Path | LocationDescriptorObject<S>;

// 点击 LocationDescriptorObject，如下：
/* export interface LocationDescriptorObject<S = LocationState> {
    pathname?: Pathname | undefined;
    search?: Search | undefined;
    state?: S | undefined;
    hash?: Hash | undefined;
    key?: LocationKey | undefined;
} */

// 发现泛型 S 确定对应了 state
export function useHistory<HistoryLocationState = H.LocationState>(): H.History<HistoryLocationState>
```

## useLocation

### 目标

掌握 useLocation 在 TypeScript 中的使用。

### 内容

-   useLocation 接收一个泛型参数，用于指定接收的 state 类型，与 useHistory 的泛型参数对应。

```jsx
import { useLocation } from 'react-router-dom'

export default function Login() {
    const location = useLocation<{ from: string }>()
    // 直接点击登录页，没有传参会报错，所以这里用了可选链操作符 ?.
    return <div>Login: {location.state?.from}</div>
}
```

-   注意：因为 useLocation 和 useHistory 都需要指定 Location 类型，因此可以将类型存放到通用的类型声明文件中，`src/types.d.ts`。

```ts
// Tip: 这里明确或了一个 null，当后面再书写 location.state.from 的时候，.from 的前面会自动加上 ? 号
export type LocationState = {
    from: string
} | null
```

-   `Home.tsx` 或 `Login.tsx` 中的类型定义直接换成 LocationState 即可。

```ts
import { useLocation } from 'react-router-dom'
import { LocationState } from '../types'

export default function Login() {
    const location = useLocation<LocationState>()
    return <div>Login: {location.state?.from}</div>
}
```

<font color=909090>🧐 分析如下，了解即可</font>。

```ts
// 点击 useLocation，把传递过来的泛型参数 S 给了返回值 H.Location<S>
// export function useLocation<S = H.LocationState>(): H.Location<S>;

// 点击 Location，发现 S 给了 state，由此推断，泛型参数是用来约束 state 参数的，如下：
/* export interface Location<S = LocationState> {
    pathname: Pathname;
    search: Search;
    state: S;
    hash: Hash;
    key?: LocationKey | undefined;
} */
```

## useParams

### 目标

能够掌握 useParams 在 TypeScript 中的使用。

### 内容

<img src="/resource/images/ifer_useParams_ts.png"/>

useParams 接收一个泛型参数，用于指定 params 对象的类型。

`App.tsx`

```ts
import { BrowserRouter as Router, Link, Route } from 'react-router-dom'
import Article from './Article'

function App() {
    return (
        <div>
            <Router>
                <nav>
                    <Link to='/article/1'>文章1</Link>
                    <Link to='/article/2'>文章2</Link>
                </nav>
                <Route path='/article/:id' component={Article} />
            </Router>
        </div>
    )
}

export default App
```

`pages/article.tsx`

```ts
import { useParams } from 'react-router'
export default function Article() {
    const params = useParams<{ id: string }>()
    return <div>Article: {params.id}</div>
}
```

## unknow

-   never: 不可能实现的类型，例如下面的 Test 就是 never。

```ts
type Test = number & string
```

-   any: 任意类型，不对类型进行校验。

```ts
let num: any
num = 88
num = 'abc'
num() // 没有错误提示
console.log(num.length) // 没有错误提示
```

-   unknown: 任意类型，更安全的 any 类型。

```ts
let num: unknown
num = 88
num = 'abc'
console.log(num)
num() // error: 不能调用方法
console.log(num.length) // error: 不能访问属性
```

-   unknown 类型可以配合断言使用。

```ts
let len = (num as string).length
console.log(len)
```

-   或者类型收窄来处理 unknown 类型。

```ts
if (typeof num === 'string') {
    num.toUpperCase()
} else if (typeof num === 'function') {
    num()
}
```

## Redux

### 目标

掌握在 TS 项目中如何初始化 Redux。

### 内容

-   安装依赖包，`yarn add redux react-redux redux-devtools-extension`。

-   `store/reducers/todo.ts`

```ts
const initValue = [
    {
        id: 1,
        name: '吃饭',
        done: false,
    },
    {
        id: 2,
        name: '睡觉',
        done: true,
    },
    {
        id: 3,
        name: '打豆豆',
        done: false,
    },
]
// action 暂时给了 any
export default function todo(state = initValue, action: any) {
    return state
}
```

-   `store/reducers/index.ts`。

```ts
import { combineReducers } from 'redux'
import todo from './todo'
export default combineReducers({ todo })
```

-   `store/index.ts`。

```jsx
import { createStore } from 'redux'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
export default createStore(rootReducer, composeWithDevTools())
```

-   `src/index.tsx`。

```jsx
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import App from './App'
import store from './store'

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
)
```

## useSelector

### 目标

掌握 useSelector 在 TS 中的使用。

### 内容

-   useSelector 的基本使用，接收两个泛型参数。

```jsx
// 泛型参数1: 指定 state 的类型，默认是 {}
// 泛型参数2: 指定函数的返回值类型
const name = useSelector<{ name: string }, string>((state) => state.name)
```

-   也可以不使用泛型，通过指定 state 函数参数的类型，[参考文档](https://react-redux.js.org/using-react-redux/usage-with-typescript#typing-the-useselector-hook)。

```jsx
const name = useSelector((state: { name: string }) => state.name)
```

-   <font color=e32d40>**问题：如何准确的获取到 store 中 todo 的类型呢？**</font>[参考文档](https://react-redux.js.org/using-react-redux/usage-with-typescript#define-root-state-and-dispatch-types)。

-   方法 1：手动指定，`pages/Home/index.tsx`。

```ts
import { useSelector } from 'react-redux'
type RootState = {
    todo: {
        id: number
        name: string
        done: boolean
    }[]
}
export default function Home() {
    const todo = useSelector((state: RootState) => state.todo)
    console.log(todo)
    return <div>Home</div>
}
```

-   方法 2：`typeof` 配合 `ReturnType`。

`typeof` 可以获取某个数据的类型，`ReturnType` 是一个泛型工具类型，可以获取一个函数类型的返回值类型。

```ts
function fn(n1: number, n2: number): number {
    return n1 + n2
}
// 获取 fn 函数的类型
type Fn = typeof fn
// 获取 Fn 函数的返回值类型
type Res = ReturnType<Fn>
```

-   获取 RootState 的操作 `store/index.tx`。

```ts
import { createStore } from 'redux'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
const store = createStore(rootReducer, composeWithDevTools())
// mark
export type RootState = ReturnType<typeof store.getState>
export default store
```

-   `pages/Home/index.tsx` 中的写法如下。

```jsx
import { useSelector } from 'react-redux'
import { RootState } from '../../store'
export default function Home() {
    const todos = useSelector((state: RootState) => state.todo)
    return (
        <ul>
            {todos.map((item) => (
                <li className={item.done ? 'completed' : ''} key={item.id}>
                    <span>{item.name}</span>
                    <button>X</button>
                </li>
            ))}
        </ul>
    )
}
```

-   `pages/Home/index.css`。

```css
.completed {
    color: lightgray;
    text-decoration: line-through;
}
```

## useDispatch

```ts
import { Dispatch } from 'react'
import { useDispatch } from 'react-redux'
export default function Home() {
    // useDispatch 的泛型参数是 Dispatch<参数>，“参数” 表示 Action 的类型
    // 一般写的时候只需要和普通的 JS 写法一样即可，因为不会在组件中直接 dispatch 一个 action
    // 而是：dispatch 的是通过 actionCreator 生成的 action
    const dispatch = useDispatch<Dispatch<{ type: 'TODO_DEL'; id: number }>>()
    dispatch({ type: 'TODO_DEL', id: 1 })
    return null
}
```

## Action 和 Reducer

### 目标

掌握 Action 和 Reducer 在 TS 中的使用。

### 内容

-   `store/actions/todo.ts`。

```ts
export const todoAdd = (name: string) => ({
    type: 'TODO_ADD',
    name,
    id: Date.now(),
    done: false,
})

export const todoDel = (id: number) => ({
    type: 'TODO_DEL',
    id,
})
```

-   `store/reducers/todo.ts`。

```ts
type TodoType = {
    id: number
    name: string
    done: boolean
}[]
const initValue: TodoType = []
// action 暂时给了 any
export default function todo(state = initValue, action: any): TodoType {
    return state
}
```

<font color=e32d40>**问题：如何给 Action 定义类型？**</font>

-   `store/reducers/todo.ts`。

```ts
type TodoType = {
    id: number
    name: string
    done: boolean
}[]

// #1 想法
type TodoAction = {
    type: string
    [key: string]: any
}

const initValue: TodoType = []
export default function todo(state = initValue, action: TodoAction): TodoType {
    // #2 问题
    console.log(action.name) // 没有提示
    return state
}
```

-   解决：应该在 `store/actions/todo.ts` 中进行明确指定。

```ts
// #1
export type TodoAction =
    | {
          type: 'TODO_ADD' // #2 '就是 ADD_TODO' 类型
          name: string
          id: number
          done: boolean
      }
    | {
          type: 'TODO_DEL'
          id: number
      }

// #3 指定返回值
export const todoAdd = (name: string): TodoAction => ({
    type: 'TODO_ADD', // #4 这里写的时候会有提示
    name,
    id: Date.now(),
    done: false,
})

export const todoDel = (id: number): TodoAction => ({
    type: 'TODO_DEL',
    id,
})
```

-   `store/reducers/todo.ts` 中的 action 类型也可以指定为 TodoAction 啦。

```ts
import { TodoAction } from '../actions/todo'

type TodoType = {
    id: number
    name: string
    done: boolean
}[]

const initValue: TodoType = []
// #5 指定 action 的类型为 TodoAction
export default function todo(state = initValue, action: TodoAction): TodoType {
    switch (action.type) {
        case 'TODO_ADD':
            const { type, ...rest } = action
            return [rest, ...state]
        case 'TODO_DEL':
            return state.filter((item) => item.id !== action.id)
        default:
            return state
    }
}
```

## 完成 Todo

<img src="/resource/images/ifer_todo_ts.png"/>

### 渲染列表

`pages/Home/index.tsx`

```ts
import { useSelector } from 'react-redux'
import { RootState } from '../../store'
import './index.css'
export default function Home() {
    const todos = useSelector((state: RootState) => state.todo)
    return (
        <div>
            <input type='text' />
            <ul>
                {todos.map((item) => (
                    <li className={item.done ? 'completed' : ''} key={item.id}>
                        <span>{item.name}</span>
                        <button>X</button>
                    </li>
                ))}
            </ul>
        </div>
    )
}
```

### 添加功能

收集数据。

```ts
import { useState } from 'react'
import { useSelector } from 'react-redux'
import { RootState } from '../../store'
import './index.css'
export default function Home() {
    // #1
    const [name, setName] = useState('')
    const todos = useSelector((state: RootState) => state.todo)
    return (
        <div>
            {/* #2 */}
            <input type='text' value={name} onChange={(e) => setName(e.target.value)} />
            <ul>
                {todos.map((item) => (
                    <li className={item.done ? 'completed' : ''} key={item.id}>
                        <span>{item.name}</span>
                        <button>X</button>
                    </li>
                ))}
            </ul>
        </div>
    )
}
```

添加数据。

```ts
import { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { RootState } from '../../store'
import { todoAdd } from '../../store/actions/todo'
import './index.css'
export default function Home() {
    const dispatch = useDispatch()
    const [name, setName] = useState('')
    const todos = useSelector((state: RootState) => state.todo)
    // #2
    const handleAdd = (e: React.KeyboardEvent<HTMLInputElement>) => {
        if (e.code === 'Enter') {
            dispatch(todoAdd(name))
            setName('')
        }
    }
    return (
        <div>
            {/* #1 */}
            <input type='text' value={name} onChange={(e) => setName(e.target.value)} onKeyUp={handleAdd} />
            <ul>
                {todos.map((item) => (
                    <li className={item.done ? 'completed' : ''} key={item.id}>
                        <span>{item.name}</span>
                        <button>X</button>
                    </li>
                ))}
            </ul>
        </div>
    )
}
```

<font color=909090>🧐 技巧：在行内事件中，鼠标移动到 e 上面可以看到具体的事件对象类型。</font>

![image-20211123215525876](/resource/images/image-20211123215525876.png)

### 删除功能

```ts
import { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { RootState } from '../../store'
import { todoAdd, todoDel } from '../../store/actions/todo'
import './index.css'
export default function Home() {
    const dispatch = useDispatch()
    const [name, setName] = useState('')
    const todos = useSelector((state: RootState) => state.todo)
    const handleAdd = (e: React.KeyboardEvent<HTMLInputElement>) => {
        if (e.code === 'Enter') {
            dispatch(todoAdd(name))
            setName('')
        }
    }
    // #2
    const handleDel = (id: number) => {
        dispatch(todoDel(id))
    }
    return (
        <div>
            <input type='text' value={name} onChange={(e) => setName(e.target.value)} onKeyUp={handleAdd} />
            <ul>
                {todos.map((item) => (
                    <li className={item.done ? 'completed' : ''} key={item.id}>
                        <span>{item.name}</span>
                        {/* #1 */}
                        <button onClick={() => handleDel(item.id)}>X</button>
                    </li>
                ))}
            </ul>
        </div>
    )
}
```

### 改变状态

`store/actions/todo.ts`

```ts
export type TodoAction =
    | {
          type: 'TODO_ADD'
          name: string
          id: number
          done: boolean
      }
    | {
          type: 'TODO_DEL'
          id: number
      }
    | {
          type: 'TODO_CHANGE_DOEN'
          id: number
      }

export const todoAdd = (name: string): TodoAction => ({
    type: 'TODO_ADD',
    name,
    id: Date.now(),
    done: false,
})

export const todoDel = (id: number): TodoAction => ({
    type: 'TODO_DEL',
    id,
})

export const todoChangeDone = (id: number): TodoAction => ({
    type: 'TODO_CHANGE_DOEN',
    id,
})
```

`store/reducers/todo.ts`

```ts
import { TodoAction } from '../actions/todo'

type TodoType = {
    id: number
    name: string
    done: boolean
}[]

const initValue: TodoType = [
    {
        id: 1,
        name: 'React',
        done: false,
    },
    {
        id: 2,
        name: 'Vue',
        done: true,
    },
    {
        id: 3,
        name: 'Angular',
        done: false,
    },
]
export default function todo(state = initValue, action: TodoAction): TodoType {
    switch (action.type) {
        case 'TODO_ADD':
            const { type, ...rest } = action
            return [rest, ...state]
        case 'TODO_DEL':
            return state.filter((item) => item.id !== action.id)
        case 'TODO_CHANGE_DOEN':
            return state.map((item) => (item.id === action.id ? { ...item, done: !item.done } : item))
        default:
            return state
    }
}
```

`pages/Home.tsx`

```ts
import { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { RootState } from '../../store'
import { todoAdd, todoChangeDone, todoDel } from '../../store/actions/todo'
import './index.css'
export default function Home() {
    const dispatch = useDispatch()
    const [name, setName] = useState('')
    const todos = useSelector((state: RootState) => state.todo)
    const handleAdd = (e: React.KeyboardEvent<HTMLInputElement>) => {
        if (e.code === 'Enter') {
            dispatch(todoAdd(name))
            setName('')
        }
    }
    const handleDel = (id: number) => {
        dispatch(todoDel(id))
    }
    // #2
    const handleChangeDone = (id: number) => {
        dispatch(todoChangeDone(id))
    }
    return (
        <div>
            <input type='text' value={name} onChange={(e) => setName(e.target.value)} onKeyUp={handleAdd} />
            <ul>
                {todos.map((item) => (
                    <li className={item.done ? 'completed' : ''} key={item.id}>
                        {/* #1 */}
                        <span onClick={() => handleChangeDone(item.id)}>{item.name}</span>
                        <button onClick={() => handleDel(item.id)}>X</button>
                    </li>
                ))}
            </ul>
        </div>
    )
}
```

## redux-thunk

### 目标

掌握 redux-thunk 在 TypeScript 中的使用。

### 内容

📝 需求：期望点击删除按钮时，等待 2s 后删除。

-   `store/index.ts` 中配置 `redux-thunk`。

```ts
// #2
import { createStore, applyMiddleware } from 'redux'
// #1
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
const store = createStore(
    rootReducer,
    // #3
    composeWithDevTools(applyMiddleware(thunk))
)
export type RootState = ReturnType<typeof store.getState>
export default store
```

-   `actions/todo.ts`。

```ts
export const todoDelAsync = (id: number) => {
    return (dispatch: any) => {
        setTimeout(() => {
            dispatch(todoDel(id))
        }, 2000)
    }
}
```

-   `pages/Home/index.tsx`。

```ts
const handleDel = (id: number) => {
    dispatch(todoDelAsync(id))
}
```

### ThunkAction

<font color=e32d40>**问题：如何处理 todoDelAsync 返回值的类型？**</font>

-   ThunkAction 类型的使用，[参考文档](https://redux.js.org/usage/usage-with-typescript#type-checking-redux-thunks)。

```ts
// 泛型参数
// 1: 指定内部函数的返回值类型，一般是 void
// 2: 指定 RootState 的类型
// 3: 指定额外的参数类型，这里用不到，一般为 unknown 或 any
// 4: 指定 dispatch 的 action 的类型
export const todoDelAsync = (id: number): ThunkAction<void, RootState, unknown, TodoAction> => {
    return (dispatch, getState, extraData) => {
        // getState().todo // 因为，指定了 RootState 类型，这儿自动具有提示
        setTimeout(() => {
            dispatch(todoDel(id))
        }, 2000)
    }
}
```

可以把 RootThunkAction 抽取到 `store/index.ts` 文件中。

```ts
import { createStore, applyMiddleware } from 'redux'
import thunk, { ThunkAction } from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
// #mark1
import { TodoAction } from './actions/todo'
const store = createStore(rootReducer, composeWithDevTools(applyMiddleware(thunk)))
export type RootState = ReturnType<typeof store.getState>
// #mark2
export type RootThunkAction = ThunkAction<void, RootState, unknown, TodoAction>
export default store
```

### 一个 Bug

```ts
export const todoDelAsync = (id: number): RootThunkAction => {
    return (dispatch) => {
        setTimeout(() => {
            // dispatch(todoDel(id))
            // !下面写法并没有提示（虽然写错了会报出来）
            dispatch({
                type: 'TODO_DEL',
                id,
            })
        }, 2000)
    }
}
```

-   问题说明：在 redux-thunk@2.4.0 新版中，使用 dispatch 的时候，会丢失提示，需要降级到 2.3.0 版本，[issues](https://github.com/reduxjs/redux-thunk/issues/326)。

-   解决办法：`yarn add redux-thunk@2.3.0`。

## 黑马头条

### 项目搭建

#### styles/index.css

记得在 `src/index.tsx` 中引入 `import './styles/index.css'`。

```css
body {
    margin: 0;
    padding: 0;
}
*,
*:before,
*:after {
    box-sizing: inherit;
}

li {
    list-style: none;
}
dl,
dd,
dt,
ul,
li {
    margin: 0;
    padding: 0;
}

.no-padding {
    padding: 0px !important;
}

.padding-content {
    padding: 4px 0;
}

a:focus,
a:active {
    outline: none;
}

a,
a:focus,
a:hover {
    cursor: pointer;
    color: inherit;
    text-decoration: none;
}

b {
    font-weight: normal;
}

div:focus {
    outline: none;
}

.fr {
    float: right;
}

.fl {
    float: left;
}

.pr-5 {
    padding-right: 5px;
}

.pl-5 {
    padding-left: 5px;
}

.block {
    display: block;
}

.pointer {
    cursor: pointer;
}

.inlineBlock {
    display: block;
}
.catagtory {
    display: flex;
    overflow: hidden;
    overflow-x: scroll;
    background-color: #f4f5f6;
    width: 100%;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 999;
}
.catagtory li {
    padding: 0 15px;
    text-align: center;
    line-height: 40px;
    color: #505050;
    cursor: pointer;
    z-index: 99;
    white-space: nowrap;
}
.catagtory li.select {
    color: #f85959;
}
.list {
    margin-top: 60px;
}
.article_item {
    padding: 0 10px;
}
.article_item .img_box {
    display: flex;
    justify-content: space-between;
}
.article_item .img_box .w33 {
    width: 33%;
    height: 90px;
    display: inline-block;
}
.article_item .img_box .w100 {
    width: 100%;
    height: 180px;
    display: inline-block;
}
.article_item h3 {
    font-weight: normal;
    line-height: 2;
}
.article_item .info_box {
    color: #999;
    line-height: 2;
    position: relative;
    font-size: 12px;
}
.article_item .info_box span {
    padding-right: 10px;
}
.article_item .info_box span.close {
    border: 1px solid #ddd;
    border-radius: 2px;
    line-height: 15px;
    height: 12px;
    width: 16px;
    text-align: center;
    padding-right: 0;
    font-size: 8px;
    position: absolute;
    right: 0;
    top: 7px;
}
```

#### `components/Channel.tsx`

```jsx
import React from 'react'

export default function Channel() {
    return (
        <ul className='catagtory'>
            <li className='select'>开发者资讯</li>
            <li>ios</li>
            <li>c++</li>
            <li>android</li>
            <li>css</li>
            <li>数据库</li>
            <li>区块链</li>
            <li>go</li>
            <li>产品</li>
            <li>后端</li>
            <li>linux</li>
            <li>人工智能</li>
            <li>php</li>
            <li>javascript</li>
            <li>架构</li>
            <li>前端</li>
            <li>python</li>
            <li>java</li>
            <li>算法</li>
            <li>面试</li>
            <li>科技动态</li>
            <li>js</li>
            <li>设计</li>
            <li>数码产品</li>
            <li>html</li>
            <li>软件测试</li>
            <li>测试开发</li>
        </ul>
    )
}
```

#### `components/NewsList.tsx`

```jsx
import React from 'react'
import avatar from '../assets/back.jpg'
export default function NewsList() {
    return (
        <div className='list'>
            <div className='article_item'>
                <h3 className='van-ellipsis'>python数据预处理 ：数据标准化</h3>
                <div className='img_box'>
                    <img src={avatar} className='w100' alt='' />
                </div>
                <div className='info_box'>
                    <span>13552285417</span>
                    <span>0评论</span>
                    <span>2018-11-29T17:02:09</span>
                </div>
            </div>
        </div>
    )
}
```

#### `App.tsx`

```jsx
import Channel from './components/Channel'
import NewsList from './components/NewsList'

export default function App() {
    return (
        <div className='app'>
            <Channel />
            <NewsList />
        </div>
    )
}
```

### 接口说明

-   获取频道列表，http://geek.itheima.net/v1_0/channels。

-   获取频道新闻，http://geek.itheima.net/v1_0/articles?channel_id=频道id&timestamp=时间戳

### 配置 Redux

`store/reducers/channel.ts`

```ts
const initValue = {
    channelList: [],
    active: 0,
}

export default function channel(state = initValue, action: any) {
    return state
}
```

`store/reducers/index.ts`

```ts
import { combineReducers } from 'redux'
import channel from './channel'
export default combineReducers({ channel })
```

`store/index.ts`

```ts
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
export default createStore(rootReducer, composeWithDevTools(applyMiddleware(thunk)))
```

`src/index.tsx`

```ts
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import './styles/index.css'
import App from './App'
import store from './store'

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
)
```

### 请求频道数据

`store/actions/channel.ts`

```ts
import axios from 'axios'
export function getChannelList() {
    return async (dispatch: any) => {
        const res = await axios.get('http://geek.itheima.net/v1_0/channels')
        console.log(res)
    }
}
```

`components/Channel.tsx`

```ts
import { useEffect } from 'react'
import { useDispatch } from 'react-redux'
import { getChannelList } from '../store/actions/channel'

export default function Channel() {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    return (
        <ul className='catagtory'>
            <li className='select'>开发者资讯</li>
            <li>ios</li>
        </ul>
    )
}
```

### 定义 RootThunkAction

```ts
import { createStore, applyMiddleware } from 'redux'
import thunk, { ThunkAction } from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
const store = createStore(rootReducer, composeWithDevTools(applyMiddleware(thunk)))
export default store

// #0
export type ChannelItem = {
    id: number
    name: string
}

// #1
export type ChannelAction = {
    type: 'CHANNEL_LIST'
    payload: ChannelItem[]
}

// #2
export type RootState = ReturnType<typeof store.getState>

// #3
export type RootAction = ChannelAction

// #4
export type RootThunkAction = ThunkAction<void, RootState, unknown, RootAction>
```

### 把频道数据存储到 Redux

`store/actions/channel.ts`

```ts
import axios from 'axios'
import { RootThunkAction } from '..'
export function getChannelList(): RootThunkAction {
    return async (dispatch) => {
        const res = await axios.get('http://geek.itheima.net/v1_0/channels')
        dispatch({
            type: 'CHANNEL_LIST',
            payload: res.data.data.channels,
        })
    }
}
```

`store/reducers/channel.ts`

```ts
import { ChannelAction } from '..'

const initValue = {
    channelList: [],
    active: 0,
}
export default function channel(state = initValue, action: ChannelAction) {
    switch (action.type) {
        case 'CHANNEL_LIST':
            return {
                ...state,
                channelList: action.payload,
            }
        default:
            return state
    }
}
```

### 渲染频道数据

`components/Channel.ts`

```ts
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { RootState } from '../store'
import { getChannelList } from '../store/actions/channel'

export default function Channel() {
    const dispatch = useDispatch()
    const channel = useSelector((state: RootState) => state.channel)
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    return (
        <ul className='catagtory'>
            {channel.channelList.map((item) => {
                return (
                    <li key={item.id} className={channel.active === item.id ? 'select' : ''}>
                        {item.name}
                    </li>
                )
            })}
        </ul>
    )
}
```

`reducers/channel.ts`

```ts
import { ChannelAction, ChannelItem } from '..'

// #1
type ChannelType = {
    channelList: ChannelItem[]
    active: number
}

// #2 指定初始数据为 ChannelType
const initValue: ChannelType = {
    channelList: [],
    active: 0,
}

// #3 指定返回值为 ChannelType
export default function channel(state = initValue, action: ChannelAction): ChannelType {
    switch (action.type) {
        case 'CHANNEL_LIST':
            return {
                ...state,
                channelList: action.payload,
            }
        default:
            return state
    }
}
```

### 处理频道高亮

`store/index.ts`

```ts
// #mark
export type ChannelAction =
    | {
          type: 'CHANNEL_LIST'
          payload: ChannelItem[]
      }
    | {
          type: 'CHANNEL_ACTIVE'
          payload: number
      }
```

`store/actions/channel.ts`

```ts
export function changeActive(id: number): RootThunkAction {
    return (dispatch) => {
        dispatch({
            type: 'CHANNEL_ACTIVE',
            payload: id,
        })
    }
}
```

`store/reducers/channel.ts`

```ts
import { ChannelAction, ChannelItem } from '..'

type ChannelType = {
    channelList: ChannelItem[]
    active: number
}

const initValue: ChannelType = {
    channelList: [],
    active: 0,
}

export default function channel(state = initValue, action: ChannelAction): ChannelType {
    switch (action.type) {
        case 'CHANNEL_LIST':
            return {
                ...state,
                channelList: action.payload,
            }
        // #mark
        case 'CHANNEL_ACTIVE':
            return {
                ...state,
                active: action.payload,
            }
        default:
            return state
    }
}
```

`components/Channel.tsx`

```ts
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { RootState } from '../store'
import { changeActive, getChannelList } from '../store/actions/channel'

export default function Channel() {
    const dispatch = useDispatch()
    const channel = useSelector((state: RootState) => state.channel)
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    return (
        <ul className='catagtory'>
            {channel.channelList.map((item) => {
                return (
                    <li key={item.id} className={channel.active === item.id ? 'select' : ''} onClick={() => dispatch(changeActive(item.id))}>
                        {item.name}
                    </li>
                )
            })}
        </ul>
    )
}
```

### 请求文章数据

`store/actions/article.ts`

```ts
import axios from 'axios'
import { RootThunkAction } from '..'

export function getArticleList(id: number): RootThunkAction {
    return async (dispatch) => {
        const res = await axios.get(`http://geek.itheima.net/v1_0/articles?channel_id=${id}&timestamp=${Date.now()}`)
        console.log(res)
    }
}
```

`components/NewsList.tsx`

```ts
import { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import avatar from '../assets/back.jpg'
import { RootState } from '../store'
import { getArticleList } from '../store/actions/article'
export default function NewsList() {
    const dispatch = useDispatch()
    const active = useSelector((state: RootState) => state.channel.active)
    useEffect(() => {
        dispatch(getArticleList(active))
    }, [dispatch, active])
    return (
        <div className='list'>
            <div className='article_item'>
                <h3 className='van-ellipsis'>python数据预处理 ：数据标准化</h3>
                <div className='img_box'>
                    <img src={avatar} className='w100' alt='' />
                </div>
                <div className='info_box'>
                    <span>13552285417</span>
                    <span>0评论</span>
                    <span>2018-11-29T17:02:09</span>
                </div>
            </div>
        </div>
    )
}
```

### 渲染文章数据

`store/index.ts`

定义 ArticleAction 和 RootAction

```ts
import { createStore, applyMiddleware } from 'redux'
import thunk, { ThunkAction } from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
const store = createStore(rootReducer, composeWithDevTools(applyMiddleware(thunk)))
export default store

export type ChannelItem = {
    id: number
    name: string
}

// #1
export type ArticleItem = {
    art_id: string
    title: string
    aut_id: string
    comm_count: number
    pubdate: string
    aut_name: string
    is_top: number
    cover: {
        type: number
        images: string[]
    }
}

export type ChannelAction =
    | {
          type: 'CHANNEL_LIST'
          payload: ChannelItem[]
      }
    | {
          type: 'CHANNEL_ACTIVE'
          payload: number
      }

// #2
export type ArticleAction = {
    type: 'ARTICLE_LIST'
    payload: ArticleItem[]
}
export type RootState = ReturnType<typeof store.getState>

// #3
export type RootAction = ChannelAction | ArticleAction

export type RootThunkAction = ThunkAction<void, RootState, unknown, RootAction>
```

`store/actions/article.ts`

```ts
import axios from 'axios'
import { RootThunkAction } from '..'

export function getArticleList(id: number): RootThunkAction {
    return async (dispatch) => {
        const res = await axios.get(`http://geek.itheima.net/v1_0/articles?channel_id=${id}&timestamp=${Date.now()}`)
        // #mark
        dispatch({
            type: 'ARTICLE_LIST',
            payload: res.data.data.results,
        })
    }
}
```

`store/reducers/article.ts`

```ts
import { ArticleAction, ArticleItem } from '..'
type AritcleType = ArticleItem[]

const initValue: AritcleType = []

export default function article(state = initValue, action: ArticleAction): AritcleType {
    switch (action.type) {
        case 'ARTICLE_LIST':
            return action.payload
        default:
            return state
    }
}
```

`store/reducers/index.ts`

```ts
import { combineReducers } from 'redux'
import channel from './channel'
import article from './article'
export default combineReducers({ channel, article })
```

`components/NewsList.tsx`

```ts
import { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import avatar from '../assets/back.jpg'
import { RootState } from '../store'
import { getArticleList } from '../store/actions/article'
export default function NewsList() {
    const dispatch = useDispatch()
    const active = useSelector((state: RootState) => state.channel.active)
    const article = useSelector((state: RootState) => state.article)
    useEffect(() => {
        dispatch(getArticleList(active))
    }, [dispatch, active])
    return (
        <div className='list'>
            {article.map((item) => {
                return (
                    <div className='article_item' key={item.art_id}>
                        <h3 className='van-ellipsis'>{item.title}</h3>
                        <div className='img_box'>
                            <img src={item.cover.type === 0 ? avatar : item.cover.images[0]} className='w100' alt='' />
                        </div>
                        <div className='info_box'>
                            <span>{item.aut_name}</span>
                            <span>{item.comm_count}评论</span>
                            <span>{item.pubdate}</span>
                        </div>
                    </div>
                )
            })}
        </div>
    )
}
```
