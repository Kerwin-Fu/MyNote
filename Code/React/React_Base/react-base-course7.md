---
title: 07_Redux 基础
date: 2021-11-12 13:54:40
tags:
---

## 今日目标

✔ 掌握 Redux 核心概念和基本使用。

✔ 掌握 react-redux 的使用。

✔ 掌握 TODOLIST 的实现思路。

<!-- more -->

## 了解 Redux

### 目标

能够说出为什么需要使用 Redux。

### 内容

[Redux 中文官网](http://cn.redux.js.org/)，[中文文档](https://www.redux.org.cn/)

-   概念

Redux 是一个全局状态管理的 JS 库。

-   背景

    a，React 的定位只是一个用来构建用户界面的库，并不是 Web 应用的完整解决方案。因此 React 在涉及到复杂组件之间的通信时会比较棘手，而对于大型项目来说，这方面恰恰是最关键的，因此只用 React 写大型项目会比较吃力。

    b，2014 年 Facebook 提出了 [Flux](http://www.ruanyifeng.com/blog/2016/01/flux.html) 架构的概念，引发了很多的实现。

    c，2015 年，Redux 出现，将 Flux 与函数式编程（Reducer）结合一起，很短时间内就成为了最热门的前端状态管理库，类似的还有 Mobx、Saga 等状态管理工具。

### 为什么要用 Redux

<img src="/resource/images/with_redux.png" class="highlight2" width="400"/>

-   不使用 Redux（图左边）

    a，只能使用父子组件通讯、状态提升等 React 自带机制，远房亲戚（非父子）关系的组件通讯时乏力。

    b，组件之间的数据流混乱，出现 Bug 时难定位。

-   使用 Redux（图右边）

    a，<font color=e32d40>**集中式存储和管理应用的状态**</font>，处理组件通讯问题时，无视组件之间的层级关系。

    b，<font color=e32d40>**简化复杂应用中的数据传递问题**</font>，数据流清晰，易于定位 Bug。

### 小结

Redux 解决了什么问题？

## Redux 安装

### 目标

能够在 React 项目中准备 Redux 开发环境。

### 步骤

1. 创建 React 项目。

```bash
npx create-react-app redux-basic
```

2. 启动项目

```bash
yarn start
```

3. 安装 Redux 包

```bash
yarn add redux
```

## Redux 核心概念

### 目标

能够了解 Redux 三个核心概念的职责。

### 内容

[阮一峰](https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)

-   为了让代码各部分职责清晰、明确，Redux 提出三个核心概念，需要我们写代码的时候遵守。

    a，**action**（动作）：描述要做的事情（要干啥）。

    b，**reducer**（函数）：更新状态（怎么干）。

    c，**store**（仓库）：整合 action 和 reducer（谁来指挥）。

-   通过例子来理解三个核心概念。

    a，action：相当于公司里面要做的事情，比如打扫卫生这个事等。

    b，reducer：相当于公司的员工，负责执行。

    c，store：相当于公司的老板。

    d，流程：老板（store）分配（dispatch）要做的事情（action）给员工（reducer），员工干完活把结果交给老板。

    e，在视图当中，通过 store dispatch 一个 action，reducer 会自动收到通知来更新 state，state 一旦变化，说有使用 state 的视图自然就变了。

<img src="/resource/images/redux.png" width="400"/>

## Action

### 目标

定义一个最基本的 action。

### 内容

-   action 用来描述要做的事情，项目中的每一个功能都是一个 action，比如。

    a，计数器案例：计数器加 1、减 1。

    b，todomvc 案例：添加任务、删除任务等。

    c，项目：登录，退出等。

-   特点。

    a，只描述做什么。

    b，本质上就是一个 JS 对象，必须带有 `type` 属性，用于区分动作的类型。

    c，可以通过 payload 携带额外的数据。

```js
{ type： 'increment' }

// payload: 参数
{ type： 'decrement', payload： 2 }

{ type: 'addTodo', payload: '吃饭' }
{ type: 'addTodo', payload: '睡觉' }
{ type: 'removeTodo', payload: 3 }
```

`store/actions.js`

```js
export const incremen = {
    type: 'INCREMENT',
    payload: 5,
}

export const decrement = {
    type: 'DECREMENT',
    payload: 5,
}
```

## Action creator

### 目标

学会使用函数（actionCreator）去创建一个 action。

### 内容

1.  问题：直接使用对象来创建 action 不灵活，参数写死了。

2.  解决：可以使用函数来创建 action，通过传参把不一样的数据传递过去就好了，我们把这个创建 action 的函数叫做 actionCreator。

3.  返回值：返回一个 action 对象。

4.  好处：代码更加简洁，容易复用。

核心代码

`store/actions.js`

```js
export const increment = (payload) => ({
    type: 'INCREMENT',
    payload,
})

export const decrement = (payload) => ({
    type: 'DECREMENT',
    payload,
})
```

### 小结

actionCreator 的作用和返回值是什么？

## Reducer

### 目标

能够掌握 reducer 的基本写法。

### 内容

reducer：本质上是一个函数，作用是根据 action 来更新状态，有如下特点。

-   函数签名为：`(prevState, action) => newState`。

-   接收上一次的状态和 action，根据 action 的类型执行对应的操作，最终返回新的状态。

-   原则：不要在 reducer 函数内部直接修改 state。

store/reducers.js

```jsx
export default function counter(state = 10, action) {
    // 处理各种各样的 action
    switch (action.type) {
        case 'INCREMENT':
            return state + action.payload
        case 'DECREMENT':
            return state - action.payload
        default:
            // 记得要有默认返回的处理
            return state
    }
}
```

## 纯函数

### 目标

了解纯函数的特点。

### 内容

-   纯函数是[函数式编程](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)中的概念，对于纯函数来说有一个很重要的特点：<font color=e32d40>**相同的输入总是得到相同的输出**</font>。

-   纯函数常具有以下特点。

    a，不得改写参数，不能使用全局变量。

    b，不能调用 Date.now() 或者 Math.random() 等不纯的方法，因为每次会得到不一样的结果。

    c，不包含副作用的处理，副作用：AJAX 请求、操作本地数据、或者操作函数外部的变量等。

-   好处：代码简洁、方便测试、方便性能优化。

-   为什么说纯函数呢？因为 reducer 要求自身就必须是一个纯函数。

```js
const add = (a, b) => a + b
add(1, 2)
```

```js
const add = (a, b) => a + b + Math.random()
add(1, 2)
add(1, 2)
```

```js
const arr = [1, 2, 3, 4, 5]
arr.slice(1, 2)
arr.slice(1, 2)
arr.slice(1, 2)
```

```js
const arr = [1, 2, 3, 4, 5]
arr.splice(1, 2)
arr.splice(1, 2)
```

## Store

### 目标

掌握 Store 的创建和基本使用。

### 内容

-   Store：仓库，是 Redux 的核心，负责整合 Action 和 Reducer。

-   基本特点。

    a，一个应用只有一个 Store。

    b，创建：`const store = createStore(reducer)`。

    c，获取数据：`store.getState()`。

    d，更新数据：`store.dispatch(action)`。

-   其他 API。

    a，订阅（监听）状态变化：`const unSubscribe = store.subscribe(() => {})`，注意要<font color=e32d40>**先**</font>订阅，后续的更新才能被观测到。

    b，取消订阅状态变化：`unSubscribe()`。

`store/index.js`，注意演示上面 API 需要先在 `index.js` 中引入此文件。

```js
// store: 整个数据的仓库，负责关联 reducer 和 action，通过 store 对象可以给 reducer 分配 action
import { createStore } from 'redux'
import reducer from './reducers'
const store = createStore(reducer)
export default store
```

### 小结

通过哪个 API 来创建 Store？创建 Store 需要什么参数？

## 点击计数 📝

### 目标

掌握在视图中使用和更新数据。

### 读取数据

<img src="/resource/images/ifer_store.png" class="highlight2"/>

App.js

```js
import React from 'react'
import store from './store'

export default function App() {
    return (
        <div>
            <h3>count: {store.getState()}</h3>
        </div>
    )
}
```

### 更改数据

```js
import React from 'react'
import store from './store'
import { increment, decrement } from './store/actions'

export default function App() {
    return (
        <div>
            <p>count: {store.getState()}</p>
            <div>
                <button onClick={() => store.dispatch(increment(1))}>+1</button>
                <button onClick={() => store.dispatch(increment(5))}>+5</button>
                <button onClick={() => store.dispatch(decrement(1))}>-1</button>
                <button onClick={() => store.dispatch(decrement(5))}>-5</button>
            </div>
        </div>
    )
}
```

### 解决问题

```js
import ReactDOM from 'react-dom'
import App from './App'
import store from './store'

ReactDOM.render(<App />, document.querySelector('#root'))

store.subscribe(() => {
    ReactDOM.render(<App />, document.querySelector('#root'))
})
```

### 小结

store 中数据的变化，通过哪个方法可以被观测到？如何让视图更新？

## Redux 执行过程

### 目标

了解 Redux 的执行过程。

### 获取默认值的执行过程

-   只要创建 store，Redux 内部就会调用一次 reducer，打印试一下 `console.log(action.type)`。

-   类似：`reducer(undefined, {type: "@@redux/INITv.a.4.t.t.p"})`。

-   这一次调用 reducer 的目的：**获取状态的默认值**。

-   因为传入的状态值是 undefined ，并且是一个随机的 action type，所以!

    a，状态值因为 undefined，所以，我们设置的默认值就会生效，比如，此处的：10。

    b，因为是一个随机的 action type，所以，reducer 中 switch 一定无法命中，那就一定会走 default，也就是直接返回了状态的默认值，也就是：10。

-   Redux 内部拿到这个数据（比如此处的 10）以后，就用这个数据作为了 store 中的最新状态值。

-   因此，将来当我们调用 `store.getState()` 方法来获取 Redux 状态值的时候，拿到的就是 10 了。

```js
import { createStore } from 'redux'
import reducer from './reducer'
// 只要创建 store 传递了 reducer，Redux 内部就会自动的 dispatch 一次 action
// 目的：就是为了 store 能够有初始值，store.dispatch({ type: '@@xx699' })
const store = createStore(reducer)

// 所以可以拿到初始值
store.getState()

export default store
```

### 点击按钮后的执行过程

1. 点击按钮，派发动作 `store.dispatch(action)`。

2. 只要触发了 action，Redux 内部就会自动调用 reducer，根据上一次的状态和 action 计算出新的状态并返回。

3. reducer 执行完毕后，将最新的状态交给 store，store 用最新的状态替换旧状态，状态更新完毕。

### 小结

点击按钮视图更新的流程是什么？

## react-redux

### 基本介绍

-   问题：为什么要使用 react-redux 这个库?

-   回答：React 和 Redux 是两个独立的库，两者之间职责独立，因此，为了更好的实现在 React 中使用 Redux 进行状态管理，就需要一种机制，将这两个独立的库关联在一起，这就是 react-redux 出现的原因。

-   react-redux 是 Redux 官方提供的 React 绑定库。

### 基本使用

1. 安装

```bash
yarn add react-redux
```

2. 配置

`index.js`

```js
import ReactDOM from 'react-dom'
import App from './App.js'
import store from './store/store.js'
import { Provider } from 'react-redux'

// 通过 Provider 提供 store 供其他组件内部使用
ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.querySelector('#root')
)

// 用了 react-redux 下面手动触发更新的方式就没用了
/* store.subscribe(() => {
    ReactDOM.render(<App />, document.querySelector('#root'))
}) */
```

<span style="position:relative;top:-3px;">✍</span> 一旦使用了 react-redux，获取和更新数据的方式就变化了，要按照这个库的要求来。

### useSelector

-   `react-redux` 这个库提供了 useSelector，作用：从 store 中获取状态。

-   selector 函数应该是个纯函数。

`App.js`

```js
import React from 'react'
import { useSelector } from 'react-redux'
const App = () => {
    const count = useSelector((state) => state)
    return (
        <div>
            <h3>{count}</h3>
        </div>
    )
}
export default App
```

### useDispatch

作用：得到 dispatch 来触发 action（触发 action 会执行 reducer，reducer 负责数据的修改，react-redux 内部会监听数据的变化自动进行视图更新）。

`App.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { increment, decrement } from './store/actions'
const App = () => {
    const count = useSelector((state) => state)
    const dispatch = useDispatch()
    return (
        <div>
            <h3>{count}</h3>
            <div>
                <button onClick={() => dispatch(increment(1))}>+1</button>
                <button onClick={() => dispatch(increment(5))}>+5</button>
                <button onClick={() => dispatch(decrement(1))}>-1</button>
                <button onClick={() => dispatch(decrement(5))}>-5</button>
            </div>
        </div>
    )
}
export default App
```

如果 Test 组件想用，看看有多方便吧，无需传值，直接拽过来！

```js
import React from 'react'
import { useSelector } from 'react-redux'

export default function Test() {
    const money = useSelector((state) => state)
    return <div>{money}</div>
}
```

## Reducer 的分离与合并

### 概述

-   随着项目功能变得越来越复杂，需要 Redux 管理的状态也会越来越多，此时，有两种方式来处理状态的更新。

    a，使用一个 reducer，处理项目中所有状态。

    b，使用多个 reducer，按照项目功能划分，每一个 reducer 处理该功能的状态。

-   推荐：使用第二种方案，每个 reducer 处理单一功能的状态，职责更明确。

-   问题：此时项目中会有多个 reducer，但是 **store 只能接收一个 reducer**，因此需要将多个 reducer 合并为一个 reducer，才能传递给 store 使用。

-   解决：使用 Redux 中的 `combineReducers({ counter： counterReducer, user： userReducer })` 函数。

-   注意：组件中再想只使用 counter 的状态，需要 `const count = useSelector((state) => state.counter)`

-   每个 reducer 应该只关注自己的数据，例如：

    a，登录功能：`loginReducer` 处理的只应该是跟登录相关的状态。

    b，个人资料：`profileReducer` 处理的只应该是跟个人资料相关的状态。

    c，文章列表、文章详情、文章评论等。

### 步骤

1. `reducers.js` 中新建 userReducer。

2. 通过 combineReducers 合并 counter 和 user 并导出。

3. 修改 `App.js` 和 `Test.js` 获取数据的方式。

4. 新建 `User.js` 测试 userReducer 的使用。

### 代码

#### `reducers.js`

```js
import { combineReducers } from 'redux'

function counter(state = 10, action) {
    switch (action.type) {
        case 'INCREMENT':
            return state + action.payload
        case 'DECREMENT':
            return state - action.payload
        default:
            return state
    }
}

function user(state = { name: 'ifer', age: 18 }, action) {
    switch (action.type) {
        case 'UPDATENAME':
            return {
                ...state,
                name: action.payload,
            }
        default:
            return state
    }
}

export default combineReducers({
    counter,
    user,
})
```

#### `App.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { increment, decrement } from './store/actions'
import Test from './Test'
import User from './User'
const App = () => {
    const count = useSelector((state) => state.counter)
    const dispatch = useDispatch()
    return (
        <div>
            <h3>{count}</h3>
            <div>
                <button onClick={() => dispatch(increment(1))}>+1</button>
                <button onClick={() => dispatch(increment(5))}>+5</button>
                <button onClick={() => dispatch(decrement(1))}>-1</button>
                <button onClick={() => dispatch(decrement(5))}>-5</button>
            </div>
            <Test />
            <User />
        </div>
    )
}
export default App
```

#### `Test.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { increment } from './store/actions'

export default function Test() {
    const count = useSelector((state) => state.counter)
    const dispatch = useDispatch()
    return (
        <div>
            <p>Test {count}</p>
            <div>
                <button onClick={() => dispatch(increment(5))}>click</button>
            </div>
        </div>
    )
}
```

#### `User.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { updateName } from './store/actions'

export default function User() {
    const user = useSelector((state) => state.user)
    const dispatch = useDispatch()
    const handleClick = () => {
        dispatch(updateName('xxx'))
    }
    return (
        <div>
            <h3>{user.name}</h3>
            <button onClick={handleClick}>update name</button>
        </div>
    )
}
```

#### `actions.js`

```js
export const updateName = (payload) => ({
    type: 'UPDATENAME',
    payload,
})
```

## ActionTypes

### 概述

-   是什么：action 对象中的 type 属性。

-   Redux 项目中，同一个 type 会在不同文件中多次被用到，比如 actions.js、reducers.js 等。

-   目标：集中处理 action type，保持一致性，容易维护！

### 操作

1. 在 store 目录中创建 `actionTypes.js` 或 `constants.js` 文件。

2. 使用常量创建 ActionType 并导出。

3. 命名推荐：`模块_动作`，比如：

    - 计数器：`COUNTER_INCREMENT` 表示计数器模块中的 INCREMENT 动作。

    - TodoList：`TODOLIST_ADD` 表示 TodoList 案例中 ADD 动作。

    - 登录：`LOGIN_GETCODE` 表示登录模块中获取验证码的动作，`LOGIN_SUBMIT` 表示登录模块中的提交功能。

    - 个人信息：`PROFILE_GETINFO` 表示个人资料模块中的获取信息动作；`PROFILE_UPDATEINFO` 等。

4. 哪里需要用到就按需导入。

### 代码

`actionTypes.js`

```js
export const COUNTER_INCREMENT = 'COUNTER_INCREMENT'
export const COUNTER_DECREMENT = 'COUNTER_DECREMENT'
export const USER_UPDATENAME = 'USER_UPDATENAME'
```

`actions.js`

```js
import { COUNTER_INCREMENT, COUNTER_DECREMENT, USER_UPDATENAME } from './actionTypes'
export const increment = (payload) => ({
    type: COUNTER_INCREMENT,
    payload,
})

export const decrement = (payload) => ({
    type: COUNTER_DECREMENT,
    payload,
})

export const updateName = (payload) => ({
    type: USER_UPDATENAME,
    payload,
})
```

`reducers.js`

```js
import { combineReducers } from 'redux'
import { COUNTER_INCREMENT, COUNTER_DECREMENT, USER_UPDATENAME } from './actionTypes'

function counter(state = 10, action) {
    switch (action.type) {
        case COUNTER_INCREMENT:
            return state + action.payload
        case COUNTER_DECREMENT:
            return state - action.payload
        default:
            return state
    }
}

function user(state = { name: 'ifer', age: 18 }, action) {
    switch (action.type) {
        case USER_UPDATENAME:
            return {
                ...state,
                name: action.payload,
            }
        default:
            return state
    }
}

export default combineReducers({
    counter,
    user,
})
```

## TODOLIST

### 目标

[效果](https://todomvc.com/examples/react/#/)

<img src="/resource/images/todolist.png" width="400" class="highlight2"/>

### 拆分组件

<a target="_blank" href="/2021/11/09/react-base-course4/#more">静态结构</a>

`App.js`，引入拆分后的组件

```js
import React from 'react'
import TodoFooter from './components/TodoFooter'
import TodoHeader from './components/TodoHeader'
import TodoMain from './components/TodoMain'

export default function App() {
    return (
        <section className='todoapp'>
            {/* TodoHeader */}
            <TodoHeader />
            {/* TodoMain */}
            <TodoMain />
            {/* TodoFooter */}
            <TodoFooter />
        </section>
    )
}
```

`index.js`，引入相关的样式

```js
import ReactDOM from 'react-dom'
import './styles/base.css'
import './styles/index.css'
import App from './App'

ReactDOM.render(<App />, document.querySelector('#root'))
```

### Redux 基本结构

#### 目标

跑通 Redux 和配置 react-redux。

#### 步骤

1. 在 `store/reducers/todo.js` 文件中创建 todo reducer 并导出。

2. 在 `store/reducers/index.js` 文件中导入 todo reducer，并导出通过 combineReducers 合并后的 reducer。

3. 在 `store/index.js` 文件通过 createStore 创建 store。

4. 入口文件配置 react-redux。

#### 代码

1. `store/reducers/todo.js`

```js
export default function todo(state = [], action) {
    return state
}
```

2. `store/reducers/index.js`

```js
import { combineReducers } from 'redux'
import todo from './todo'
const rootReducer = combineReducers({
    todo,
})

export default rootReducer
```

3. `store/index.js`

```js
import { createStore } from 'redux'
import reducer from './reducers'
const store = createStore(reducer)
export default store
```

4. 入口文件，`index.js`

```js
import ReactDOM from 'react-dom'
import './styles/base.css'
import './styles/index.css'
import App from './App'
import store from './store'
import { Provider } from 'react-redux'

console.log(store.getState()) // 打印试一下

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.querySelector('#root')
)
```

### 列表渲染

#### 步骤

1. 在 `store/reducers/todo.js` 文件中准备初始数据。

2. 在 `components/TodoMain.js` 文件中通过 useSelector 获取数据并渲染。

#### 代码

1. `store/reducers/todo.js`

```js
const initState = [
    {
        id: 1,
        name: '吃饭',
        done: true,
    },
    {
        id: 2,
        name: '睡觉',
        done: false,
    },
]

export default function todo(state = initState, action) {
    return state
}
```

2. `components/TodoMain.js`

```js
import React from 'react'
import { useSelector } from 'react-redux'

export default function TodoMain() {
    const list = useSelector((state) => state.todo)
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <li key={item.id} className={item.done ? 'completed' : ''}>
                        <div className='view'>
                            <input className='toggle' type='checkbox' checked={item.done} onChange={() => {}} />
                            <label>{item.name}</label>
                            <button className='destroy'></button>
                        </div>
                        <input className='edit' value='Create a TodoMVC template' />
                    </li>
                ))}
            </ul>
        </section>
    )
}
```

### 删除功能

#### 步骤

1. 在 `store/constants/todo.js` 文件中新建删除的 ActionType。

2. 在 `store/actions/todo.js` 文件中创建删除的 actionCreator。

3. 在 `store/reducers/todo.js` 文件中编写删除的 reducer。

4. 在 `components/TodoMain.js` 组件中通过 dispatch action 进行删除的操作。

#### 代码

1. `store/constants/todo.js`

```js
export const TODO_DEL = 'TODO_DEL'
export const TODO_CHANGESTATUS = 'TODO_CHANGESTATUS'
```

2. `store/actions/todo.js`

```js
import { TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

export const delTodo = (id) => ({
    type: TODO_DEL,
    id,
})
export const changeStatus = (id) => ({
    type: TODO_CHANGESTATUS,
    id,
})
```

3. `store/reducers/todo.js`

```js
import { TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

const initState = [
    {
        id: 1,
        name: '吃饭',
        done: true,
    },
    {
        id: 2,
        name: '睡觉',
        done: false,
    },
]

export default function todo(state = initState, action) {
    if (action.type === TODO_DEL) {
        return state.filter((item) => item.id !== action.id)
    }
    if (action.type === TODO_CHANGESTATUS) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    done: !item.done,
                }
            } else {
                return item
            }
        })
    }
    return state
}
```

4. `components/TodoMain.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { changeStatus, delTodo } from '../store/actions/todo'

export default function TodoMain() {
    const list = useSelector((state) => state.todo)
    const dispatch = useDispatch()
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <li key={item.id} className={item.done ? 'completed' : ''}>
                        <div className='view'>
                            <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                            <label>{item.name}</label>
                            <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
                        </div>
                        <input className='edit' value='Create a TodoMVC template' />
                    </li>
                ))}
            </ul>
        </section>
    )
}
```

### 修改任务状态

#### 步骤

1. 在 `store/constants/todo.js` 文件中创建修改任务状态的 ActionType。

2. 在 `store/actions/todo.js` 文件中创建修改任务状态的 actionCreator。

3. 在 `store/reducers/todo.js` 文件中编写修改任务状态的 reducer。

4. 在 `components/TodoMain.js` 组件中通过 dispatch action 进行修改任务状态的操作。

#### 代码

1. `store/constants/todo.js`

```js
export const TODO_CHANGESTATUS = 'TODO_CHANGESTATUS'
```

2. `store/actions/todo.js`

```js
export const changeStatus = (id) => ({
    type: TODO_CHANGESTATUS,
    id,
})
```

3. `store/reducers/todo.js`

```js
export default function todo(state = initState, action) {
    if (action.type === TODO_DEL) {
        return state.filter((item) => item.id !== action.id)
    }
    if (action.type === TODO_CHANGESTATUS) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    done: !item.done,
                }
            } else {
                return item
            }
        })
    }
    return state
}
```

4. `components/TodoMain.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { changeStatus, delTodo } from '../store/actions/todo'

export default function TodoMain() {
    const list = useSelector((state) => state.todo)
    const dispatch = useDispatch()
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <li key={item.id} className={item.done ? 'completed' : ''}>
                        <div className='view'>
                            <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                            <label>{item.name}</label>
                            <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
                        </div>
                        <input className='edit' value='Create a TodoMVC template' />
                    </li>
                ))}
            </ul>
        </section>
    )
}
```

### 添加任务

#### 步骤

1. 在 `store/constants/todo.js` 文件中创建添加任务的 ActionType。

2. 在 `store/actions/todo.js` 文件中创建添加任务的 actionCreator。

3. 在 `store/reducers/todo.js` 文件中编写添加任务的 reducer。

4. 在 `components/TodoHeader.js` 组件中通过 dispatch action 进行添加任务的操作，如果敲了回车，并且 name 不为空，才进行 dispatch，最后清空输入的内容。

#### 代码

1. `store/constants/todo.js`

```js
export const TODO_DEL = 'TODO_DEL'
export const TODO_CHANGESTATUS = 'TODO_CHANGESTATUS'
export const TODO_ADD = 'TODO_ADD'
```

2. `store/actions/todo.js`

```js
import { TODO_ADD, TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

export const delTodo = (id) => ({
    type: TODO_DEL,
    id,
})
export const changeStatus = (id) => ({
    type: TODO_CHANGESTATUS,
    id,
})
export const addTodo = (name) => ({
    type: TODO_ADD,
    name,
    id: Date.now(),
})
```

3. `store/reducers/todo.js`

```js
import { TODO_ADD, TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

const initState = [
    {
        id: 1,
        name: '吃饭',
        done: true,
    },
    {
        id: 2,
        name: '睡觉',
        done: false,
    },
]

export default function todo(state = initState, action) {
    if (action.type === TODO_DEL) {
        return state.filter((item) => item.id !== action.id)
    }
    if (action.type === TODO_CHANGESTATUS) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    done: !item.done,
                }
            } else {
                return item
            }
        })
    }
    if (action.type === TODO_ADD) {
        const todo = {
            id: action.id,
            name: action.name,
            done: false,
        }
        return [todo, ...state]
    }
    return state
}
```

4. `components/TodoHeader.js`

```js
import React, { useState } from 'react'
import { useDispatch } from 'react-redux'
import { addTodo } from '../store/actions/todo'

export default function TodoHeader() {
    const [name, setName] = useState('')
    const dispatch = useDispatch()
    const add = (e) => {
        if (e.code === 'Enter') {
            if (!name) return alert('内容不能为空')
            dispatch(addTodo(name))
            setName('')
        }
    }
    return (
        <header className='header'>
            <h1>todo</h1>
            <input className='new-todo' placeholder='What needs to be done?' autoFocus value={name} onChange={(e) => setName(e.target.value)} onKeyUp={add} />
        </header>
    )
}
```

### 全选功能

#### 步骤

1. 在 `store/constants/todo.js` 文件中创建全选的 ActionType。

2. 在 `store/actions/todo.js` 文件中创建全选的 actionCreator。

3. 在 `store/reducers/todo.js` 文件中编写全选的 reducer。

4. 在 `components/TodoMain.js` 组件中通过 dispatch action 进行全选的操作（关键点：先拿到全选的状态，点击的时候对当前全选状态进行取反的操作）。

#### 代码

1. `store/constants/todo.js`

```js
export const TODO_DEL = 'TODO_DEL'
export const TODO_CHANGESTATUS = 'TODO_CHANGESTATUS'
export const TODO_ADD = 'TODO_ADD'
export const TODO_CHANGEALL = 'TODO_CHANGEALL'
```

2. `store/actions/todo.js`

```js
import { TODO_ADD, TODO_CHANGEALL, TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

export const delTodo = (id) => ({
    type: TODO_DEL,
    id,
})
export const changeStatus = (id) => ({
    type: TODO_CHANGESTATUS,
    id,
})
export const addTodo = (name) => ({
    type: TODO_ADD,
    name,
    id: Date.now(),
})
export const changeAll = (done) => ({
    type: TODO_CHANGEALL,
    done,
})
```

3. `store/reducers/todo.js`

```js
import { TODO_ADD, TODO_CHANGEALL, TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

const initState = [
    {
        id: 1,
        name: '吃饭',
        done: true,
    },
    {
        id: 2,
        name: '睡觉',
        done: false,
    },
]

export default function todo(state = initState, action) {
    if (action.type === TODO_DEL) {
        return state.filter((item) => item.id !== action.id)
    }
    if (action.type === TODO_CHANGESTATUS) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    done: !item.done,
                }
            } else {
                return item
            }
        })
    }
    if (action.type === TODO_ADD) {
        const todo = {
            id: action.id,
            name: action.name,
            done: false,
        }
        return [todo, ...state]
    }
    if (action.type === TODO_CHANGEALL) {
        return state.map((item) => {
            return {
                ...item,
                done: action.done,
            }
        })
    }
    return state
}
```

4. `components/TodoMain.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { changeAll, changeStatus, delTodo } from '../store/actions/todo'

export default function TodoMain() {
    const list = useSelector((state) => state.todo)
    const dispatch = useDispatch()
    const isCheckAll = list.every((item) => item.done)
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' checked={isCheckAll} onChange={() => dispatch(changeAll(!isCheckAll))} />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <li key={item.id} className={item.done ? 'completed' : ''}>
                        <div className='view'>
                            <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                            <label>{item.name}</label>
                            <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
                        </div>
                        <input className='edit' value='Create a TodoMVC template' onChange={() => {}} />
                    </li>
                ))}
            </ul>
        </section>
    )
}
```

### 双击显示编辑框

#### 思路

1. 明确：是否显示编辑框取决于 li 上是否使用 editing class。

2. 创建控制是否应用 editing class 的 state，currentId，后续打算用此状态和每一行的 id 进行比较。

3. 判断，`editing: item.id === currentId`。

#### 代码

`components/TodoMain.js`

```js
import React, { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import classNames from 'classnames'
import { changeAll, changeStatus, delTodo } from '../store/actions/todo'

export default function TodoMain() {
    const list = useSelector((state) => state.todo)
    const dispatch = useDispatch()
    const isCheckAll = list.every((item) => item.done)

    const [currentId, setCurrentId] = useState('')
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' checked={isCheckAll} onChange={() => dispatch(changeAll(!isCheckAll))} />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <li
                        key={item.id}
                        className={classNames({
                            completed: item.done,
                            editing: item.id === currentId,
                        })}
                    >
                        <div className='view'>
                            <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                            <label onDoubleClick={() => setCurrentId(item.id)}>{item.name}</label>
                            <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
                        </div>
                        <input className='edit' value='Create a TodoMVC template' onChange={() => {}} />
                    </li>
                ))}
            </ul>
        </section>
    )
}
```

### 自动获取焦点

#### 思路

1. 通过 ref 绑定编辑框，但是能直接在 TodoMain 组件里面写吗？

2. 抽离 TodoItem 组件，目的：为了每一个组件都有一个自己的 ref，不会相互影响。

3. 当 currentId 变化的时候进行 `ref.current.focus()`。

4. 离开编辑框的时候把 currentId 置空（为什么会出现这个问题呢，原来 currentId 放哪里，只会有一个，现在呢？）。

#### 代码

1. `TodoItem.js`

```js
import React, { useState, useRef, useEffect } from 'react'
import { useDispatch } from 'react-redux'
import classNames from 'classnames'
import { changeStatus, delTodo } from '../store/actions/todo'

export default function TodoItem({ item }) {
    const [currentId, setCurrentId] = useState('')
    const ref = useRef(null)
    const dispatch = useDispatch()
    const showEdit = (id) => {
        setCurrentId(id)
        // ref.current.focus()
    }
    useEffect(() => {
        ref.current.focus()
    }, [currentId])
    return (
        <li
            className={classNames({
                completed: item.done,
                editing: item.id === currentId,
            })}
        >
            <div className='view'>
                <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                <label onDoubleClick={() => showEdit(item.id)}>{item.name}</label>
                <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
            </div>
            <input className='edit' value='Create a TodoMVC template' onChange={() => {}} ref={ref} onBlur={() => setCurrentId('')} />
        </li>
    )
}
```

2. `TodoMain.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { changeAll } from '../store/actions/todo'
import TodoItem from './TodoItem'

export default function TodoMain() {
    const list = useSelector((state) => state.todo)
    const dispatch = useDispatch()
    const isCheckAll = list.every((item) => item.done)
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' checked={isCheckAll} onChange={() => dispatch(changeAll(!isCheckAll))} />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <TodoItem key={item.id} item={item} />
                ))}
            </ul>
        </section>
    )
}
```

### 回填数据

需求：双击回填数据，在编辑框中输入时并记住内容。

1. 准备一个自己组件的变量来和输入框中的 value 进行绑定。

2. 输入框 onChange 的时候记住内容。

3. 双击的时候把 item.name 给 input 框的 value。

`components/TodoItem.js`

```js
import React, { useState, useRef, useEffect } from 'react'
import { useDispatch } from 'react-redux'
import classNames from 'classnames'
import { changeStatus, delTodo } from '../store/actions/todo'

export default function TodoItem({ item }) {
    const [currentId, setCurrentId] = useState('')
    // #1
    const [currentName, setCurrentName] = useState('')
    const ref = useRef(null)
    const dispatch = useDispatch()
    const showEdit = (id, name) => {
        setCurrentId(id)
        // #3
        setCurrentName(name)
    }
    useEffect(() => {
        ref.current.focus()
    }, [currentId])
    return (
        <li
            className={classNames({
                completed: item.done,
                editing: item.id === currentId,
            })}
        >
            <div className='view'>
                <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                <label onDoubleClick={() => showEdit(item.id, item.name)}>{item.name}</label>
                <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
            </div>
            {/* #2 */}
            <input className='edit' value={currentName} onChange={(e) => setCurrentName(e.target.value)} ref={ref} onBlur={() => setCurrentId('')} />
        </li>
    )
}
```

### 完成修改

#### 步骤

1. 在 `store/constants/todo.js` 文件中创建修改 name 的 ActionType。

2. 在 `store/actions/todo.js` 文件中创建修改 name 的 actionCreator。

3. 在 `store/reducers/todo.js` 文件中编写修改 name 的 reducer。

4. 在 `components/TodoItem.js` 组件中通过 dispatch action 进行修改 name 的操作。

#### 代码

1. `store/constants/todo.js`

```js
export const TODO_DEL = 'TODO_DEL'
export const TODO_CHANGESTATUS = 'TODO_CHANGESTATUS'
export const TODO_ADD = 'TODO_ADD'
export const TODO_CHANGEALL = 'TODO_CHANGEALL'
export const TODO_CHANGENAME = 'TODO_CHANGENAME'
```

2. `store/actions/todo.js`

```js
import { TODO_ADD, TODO_CHANGEALL, TODO_CHANGENAME, TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

export const delTodo = (id) => ({
    type: TODO_DEL,
    id,
})
export const changeStatus = (id) => ({
    type: TODO_CHANGESTATUS,
    id,
})
export const addTodo = (name) => ({
    type: TODO_ADD,
    name,
    id: Date.now(),
})
export const changeAll = (done) => ({
    type: TODO_CHANGEALL,
    done,
})
export const changeName = (id, name) => ({
    type: TODO_CHANGENAME,
    id,
    name,
})
```

3. `store/reducers/todo.js`

```js
import { TODO_CHANGENAME, TODO_ADD, TODO_CHANGEALL, TODO_CHANGESTATUS, TODO_DEL } from '../constants/todo'

const initState = [
    {
        id: 1,
        name: '吃饭',
        done: true,
    },
    {
        id: 2,
        name: '睡觉',
        done: false,
    },
]

export default function todo(state = initState, action) {
    if (action.type === TODO_DEL) {
        return state.filter((item) => item.id !== action.id)
    }
    if (action.type === TODO_CHANGESTATUS) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    done: !item.done,
                }
            } else {
                return item
            }
        })
    }
    if (action.type === TODO_ADD) {
        const todo = {
            id: action.id,
            name: action.name,
            done: false,
        }
        return [todo, ...state]
    }
    if (action.type === TODO_CHANGEALL) {
        return state.map((item) => {
            return {
                ...item,
                done: action.done,
            }
        })
    }
    if (action.type === TODO_CHANGENAME) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    name: action.name,
                }
            } else {
                return item
            }
        })
    }
    return state
}
```

4. `components/TodoItem.js`

```js
import React, { useState, useRef, useEffect } from 'react'
import { useDispatch } from 'react-redux'
import classNames from 'classnames'
import { changeStatus, delTodo, changeName } from '../store/actions/todo'

export default function TodoItem({ item }) {
    const [currentId, setCurrentId] = useState('')
    const [currentName, setCurrentName] = useState('')
    const ref = useRef(null)
    const dispatch = useDispatch()
    const showEdit = (id, name) => {
        setCurrentId(id)
        setCurrentName(name)
    }
    const edit = (e) => {
        // ESC
        if (e.keyCode === 27) {
            setCurrentId('')
        }
        // 回车
        if (e.keyCode === 13) {
            dispatch(changeName(currentId, currentName))
            setCurrentId('') // 隐藏编辑框
            setCurrentName('') // 置空输入的内容
        }
    }
    useEffect(() => {
        ref.current.focus()
    }, [currentId])
    return (
        <li
            className={classNames({
                completed: item.done,
                editing: item.id === currentId,
            })}
        >
            <div className='view'>
                <input className='toggle' type='checkbox' checked={item.done} onChange={() => dispatch(changeStatus(item.id))} />
                <label onDoubleClick={() => showEdit(item.id, item.name)}>{item.name}</label>
                <button className='destroy' onClick={() => dispatch(delTodo(item.id))}></button>
            </div>
            <input className='edit' value={currentName} onChange={(e) => setCurrentName(e.target.value)} ref={ref} onBlur={() => setCurrentId('')} onKeyUp={edit} />
        </li>
    )
}
```

### 清空已完成

1. `constants/todo.js`

```js
export const TODO_DEL = 'TODO_DEL'
export const TODO_CHANGESTATUS = 'TODO_CHANGESTATUS'
export const TODO_ADD = 'TODO_ADD'
export const TODO_CHANGEALL = 'TODO_CHANGEALL'
export const TODO_CHANGENAME = 'TODO_CHANGENAME'
export const TODO_CLEARDONED = 'TODO_CLEARDONED'
```

2. `actions/todo.js`

```js
import { TODO_ADD, TODO_CHANGEALL, TODO_CHANGENAME, TODO_CHANGESTATUS, TODO_CLEARDONED, TODO_DEL } from '../constants/todo'

export const delTodo = (id) => ({
    type: TODO_DEL,
    id,
})
export const changeStatus = (id) => ({
    type: TODO_CHANGESTATUS,
    id,
})
export const addTodo = (name) => ({
    type: TODO_ADD,
    name,
    id: Date.now(),
})
export const changeAll = (done) => ({
    type: TODO_CHANGEALL,
    done,
})
export const changeName = (id, name) => ({
    type: TODO_CHANGENAME,
    id,
    name,
})
export const clearTodo = () => ({
    type: TODO_CLEARDONED,
})
```

3. `reducers/todo.js`

```js
import { TODO_CHANGENAME, TODO_ADD, TODO_CHANGEALL, TODO_CHANGESTATUS, TODO_DEL, TODO_CLEARDONED } from '../constants/todo'

const initState = [
    {
        id: 1,
        name: '吃饭',
        done: true,
    },
    {
        id: 2,
        name: '睡觉',
        done: false,
    },
]

export default function todo(state = initState, action) {
    if (action.type === TODO_DEL) {
        return state.filter((item) => item.id !== action.id)
    }
    if (action.type === TODO_CHANGESTATUS) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    done: !item.done,
                }
            } else {
                return item
            }
        })
    }
    if (action.type === TODO_ADD) {
        const todo = {
            id: action.id,
            name: action.name,
            done: false,
        }
        return [todo, ...state]
    }
    if (action.type === TODO_CHANGEALL) {
        return state.map((item) => {
            return {
                ...item,
                done: action.done,
            }
        })
    }
    if (action.type === TODO_CHANGENAME) {
        return state.map((item) => {
            if (item.id === action.id) {
                return {
                    ...item,
                    name: action.name,
                }
            } else {
                return item
            }
        })
    }
    if (action.type === TODO_CLEARDONED) {
        // 保留没完成，清空已完成
        return state.filter((item) => !item.done)
    }
    return state
}
```

4. `components/TodoFooter.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { clearTodo } from '../store/actions/todo'

export default function TodoFooter() {
    const list = useSelector((state) => state.todo)
    const leftCount = list.filter((item) => !item.done).length
    const dispatch = useDispatch()
    return (
        <footer className='footer'>
            <span className='todo-count'>
                <strong>{leftCount}</strong> item left
            </span>
            <ul className='filters'>
                <li>
                    <a className='selected' href='#/'>
                        All
                    </a>
                </li>
                <li>
                    <a href='#/active'>Active</a>
                </li>
                <li>
                    <a href='#/completed'>Completed</a>
                </li>
            </ul>
            <button className='clear-completed' onClick={() => dispatch(clearTodo())}>
                Clear completed
            </button>
        </footer>
    )
}
```

### 统计剩余未完成数量

`components/TodoFooter.js`

```js
import React from 'react'
import { useSelector } from 'react-redux'

export default function TodoFooter() {
    const list = useSelector((state) => state.todo)
    const leftCount = list.filter((item) => !item.done).length
    return (
        <footer className='footer'>
            <span className='todo-count'>
                <strong>{leftCount}</strong> item left
            </span>
            <ul className='filters'>
                <li>
                    <a className='selected' href='#/'>
                        All
                    </a>
                </li>
                <li>
                    <a href='#/active'>Active</a>
                </li>
                <li>
                    <a href='#/completed'>Completed</a>
                </li>
            </ul>
            <button className='clear-completed'>Clear completed</button>
        </footer>
    )
}
```

### 点击高亮

#### 步骤

1. 在 store/constants/filter.js 文件中创建过滤的 ActionType。

2. 在 store/actions/filter.js 文件中创建过滤的 actionCreator。

3. 在 store/reducers/filter.js 文件中编写过滤的 reducer。

4. 在 store/reducers/index.js 引入 filter reducer。

5. 在 components/TodoFooter.js 组件中通过 dispatch action 进行过滤的操作。

#### 代码

1. `constants/filter.js`

```js
export const FILTER_SELECTED = 'FILTER_SELECTED'
```

2. `actions/filter.js`

```js
import { FILTER_SELECTED } from '../constants/filter'

export const changeFilter = (filter) => ({
    type: FILTER_SELECTED,
    filter,
})
```

3. `reducers/filter.js`

```js
import { FILTER_SELECTED } from '../constants/filter'

export default function filter(state = 'all', action) {
    if (action.type === FILTER_SELECTED) {
        return action.filter
    }
    return state
}
```

4. `reducers/index.js`

```js
import { combineReducers } from 'redux'
import todo from './todo'
import filter from './filter'
const rootReducer = combineReducers({
    todo,
    filter,
})

export default rootReducer
```

5. `components/TodoFooter.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { clearTodo } from '../store/actions/todo'
import { changeFilter } from '../store/actions/filter'

export default function TodoFooter() {
    const list = useSelector((state) => state.todo)
    const leftCount = list.filter((item) => !item.done).length
    const dispatch = useDispatch()
    const selected = useSelector((state) => state.filter)
    const arr = ['all', 'active', 'completed']
    return (
        <footer className='footer'>
            <span className='todo-count'>
                <strong>{leftCount}</strong> item left
            </span>
            <ul className='filters'>
                {arr.map((item) => (
                    <li key={item}>
                        <a className={item === selected ? 'selected' : ''} href='#/' onClick={() => dispatch(changeFilter(item))}>
                            {item}
                        </a>
                    </li>
                ))}
            </ul>
            <button className='clear-completed' onClick={() => dispatch(clearTodo())}>
                Clear completed
            </button>
        </footer>
    )
}
```

### 切换数据

`components/TodoMain.js`

```js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { changeAll } from '../store/actions/todo'
import TodoItem from './TodoItem'

export default function TodoMain() {
    const list = useSelector((state) => {
        if (state.filter === 'active') {
            return state.todo.filter((item) => !item.done)
        } else if (state.filter === 'completed') {
            return state.todo.filter((item) => item.done)
        } else {
            return state.todo
        }
    })
    const dispatch = useDispatch()
    const isCheckAll = list.every((item) => item.done)
    return (
        <section className='main'>
            <input id='toggle-all' className='toggle-all' type='checkbox' checked={isCheckAll} onChange={() => dispatch(changeAll(!isCheckAll))} />
            <label htmlFor='toggle-all'>Mark all as complete</label>
            <ul className='todo-list'>
                {list.map((item) => (
                    <TodoItem key={item.id} item={item} />
                ))}
            </ul>
        </section>
    )
}
```

## 中间件概述

### 目标

能够理解为什么需要 Redux 中间件。

### 内容

默认情况下，Redux 自身只能处理同步数据流，但是在实际项目开发中，状态的更新、获取，通常是使用异步操作来实现。

-   问题：如何在 Redux 中进行异步操作呢?

-   回答：通过 Redux 中间件机制来实现。

-   中间件，可以理解为处理一个功能的中间环节，对于 Redux 中间件来说就是在数据到达 reducer 之前进行一系列的处理操作。

### 触发时机

-   Redux 中间件执行时机：**在 dispatching action 和 到达 reducer 之间**。

    a，没有中间件：`dispatch(action) => reducer`。

    b，使用中间件：`dispatch(action) => 执行中间件代码 => reducer`。

-   原理：封装了 Redux 的 dispatch 方法。

    a，没有中间件：`store.dispatch()` 使用的是 Redux 库自己提供的 dispatch 方法，用来发起状态更新。

    b，使用中间件：`store.dispatch()` 使用的是中间件封装处理后的 dispatch，但是最终还是会调用 Redux 库自己提供的 dispatch 方法。

没有中间件

<img src="/resource/images/redux中间件-触发时机1.jpg"/>

有中间件

<img src="/resource/images/redux中间件-触发时机2.jpg"/>

## logger 中间件

1. 安装：`yarn add redux-logger`。

2. 导入 redux-logger。

3. 从 redux 中导入 applyMiddleware 函数。

4. 将 applyMiddleware() 调用作为 createStore 函数的第二个参数。

5. 调用 applyMiddleware 函数时，将 logger 作为参数传入。

6. 后续调用 store.dispatch() 时，控制台就会有日志信息输出。

`store/index.js`

```js
import { createStore, applyMiddleware } from 'redux'
import logger from 'redux-logger'
import rootReducer from './reducers'
export default createStore(rootReducer, applyMiddleware(logger))
```

## redux-thunk 使用

`redux-thunk` 中间件可以处理函数形式的 action，而在函数形式的 action 中就可以执行异步操作代码，完成异步操作。

1. 安装：`yarn add redux-thunk`。

2. 导入 redux-thunk。

3. 将 thunk 添加到中间件列表中。

4. 修改 action creator，返回一个函数。

5. 在函数形式的 action 中执行异步操作，在异步操作成功后，分发 action 更新状态。

`store/index.js`

```js
import thunk from 'redux-thunk'
import logger from 'redux-logger'
import rootReducer from './reducers'
export default createStore(rootReducer, applyMiddleware(thunk, logger))
```

`store/actions/todo.js`

```js
export const clearTodo = () => {
    return (dispatch) => {
        setTimeout(() => {
            dispatch({
                type: CLEAR_TODO,
            })
        }, 1000)
    }
}
```

## redux-devtools-extension

### 目标

开发 React 项目时，能够通过 Chrome 开发者工具调试跟踪 Redux 状态。

### 步骤

1. 保证浏览器安装了 Redux 的开发者工具。

2. 通过包管理器在项目中安装 `yarn add redux-devtools-extension`。

3. 在 store/index.js 中进行配置。

4. 启动 react 项目，打开 chrome 开发者工具，测试

`文档` [redux-devtools-exension](https://www.npmjs.com/package/redux-devtools-extension)

```jsx
import { createStore, applyMiddleware } from 'redux'
import reducer from './reducers'
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
export default createStore(reducer, composeWithDevTools(applyMiddleware(thunk)))
```

## 黑马头条 📝

<img src="/resource/images/ifer_toutiao.png" class="highlight2" width="300"/>

### 项目初始化

1. 通过 `npx create-react-app toutiao` 初始化项目，并清理无关文件。

2. 引入结构和样式。

#### `src/index.css`

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

#### `src/components/Channel.js`

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

#### `src/components/NewsList.js`

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

#### `src/App.js`

```jsx
import React from 'react'
import Channel from './components/Channel'
import NewsList from './components/NewsList'
export default function App() {
    return (
        <div className='app'>
            <Channel></Channel>
            <NewsList></NewsList>
        </div>
    )
}
```

#### `src/index.js`

```js
import ReactDOM from 'react-dom'
import App from './App'
import './index.css'

ReactDOM.render(<App />, document.querySelector('#root'))
```

### 接口说明

-   获取频道列表，`http://geek.itheima.net/v1_0/channels`、`toutiao`。

-   获取频道新闻，`http://geek.itheima.net/v1_0/articles?channel_id=频道id&timestamp=时间戳`。

### Redux 初始化

-   安装

```bash
yarn add redux react-redux redux-thunk axios redux-devtools-extension
```

-   创建目录结构，src 目录里面的文件如下

```bash
|-- App.js
|-- assets
|   `-- back.jpg
|-- components
|   |-- Channel.js
|   `-- NewsList.js
|-- index.css
|-- index.js
`-- store
    |-- actions
    |-- constants
    |-- index.js
    `-- reducers
```

-   store/index.js

```jsx
import { createStore, applyMiddleware } from 'redux'
import { composeWithDevTools } from 'redux-devtools-extension'
import thunk from 'redux-thunk'

import reducer from './reducers'

export default createStore(reducer, composeWithDevTools(applyMiddleware(thunk)))
```

-   store/reducers/index.js

```jsx
import { combineReducers } from 'redux'
import channel from './channel'
import news from './news'
export default combineReducers({
    channel,
    news,
})
```

-   store/reducers/channel.js

```js
export default function channel(state = [], action) {
    return state
}
```

-   store/reducers/news.js

```js
export default function news(state = [], action) {
    return state
}
```

-   入口文件，index.js

```jsx
import ReactDOM from 'react-dom'
import App from './App'
import './index.css'
import store from './store'
import { Provider } from 'react-redux'

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
)
```

-   通过开发者工具查看效果

### 加载频道数据

1. `store/constants/channel.js`

```js
export const CHANNEL_GET = 'CHANNEL_GET'
```

2. `store/actions/channel.js`

```js
import axios from 'axios'
import { CHANNEL_GET } from '../constants/channel'

export const getChannelListAc = (payload) => ({
    type: CHANNEL_GET,
    payload,
})

export const getChannelList = () => {
    return async (dispatch) => {
        const res = await axios.get('http://geek.itheima.net/v1_0/channels')
        dispatch(getChannelListAc(res.data.data.channels))
    }
}
```

3. `store/reducers/channel.js`

```js
import { CHANNEL_GET } from '../constants/channel'

export default function channel(state = [], action) {
    switch (action.type) {
        case CHANNEL_GET:
            return action.payload
        default:
            return state
    }
}
```

4. `components/Channel.js`

```js
import React, { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { getChannelList } from '../store/actions/channel'

export default function Channel() {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    const list = useSelector((state) => state.channel)
    return (
        <ul className='catagtory'>
            {list.map((item) => (
                <li key={item.id} className='select'>
                    {item.name}
                </li>
            ))}
        </ul>
    )
}
```

### 处理初始高亮

`store/reducers/channel.js`

```js
import { CHANNEL_GET } from '../constants/channel'

const initState = {
    list: [],
    active: 0,
}

export default function channel(state = initState, action) {
    switch (action.type) {
        case CHANNEL_GET:
            return {
                ...state,
                list: action.payload,
            }
        default:
            return state
    }
}
```

`components/Channel.js`

```js
import React, { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { getChannelList } from '../store/actions/channel'

export default function Channel() {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    const list = useSelector((state) => state.channel.list)
    const active = useSelector((state) => state.channel.active)
    return (
        <ul className='catagtory'>
            {list.map((item) => (
                <li key={item.id} className={item.id === active ? 'select' : ''}>
                    {item.name}
                </li>
            ))}
        </ul>
    )
}
```

### 点击高亮

1. `store/constants/channel.js`

```js
export const CHANNEL_GET = 'CHANNEL_GET'
export const CHANNEL_ACTIVE = 'CHANNEL_ACTIVE'
```

2. `store/actions/channel.js`

```js
import axios from 'axios'
import { CHANNEL_ACTIVE, CHANNEL_GET } from '../constants/channel'

export const getChannelListAc = (payload) => ({
    type: CHANNEL_GET,
    payload,
})

export const getChannelList = () => {
    return async (dispatch) => {
        const res = await axios.get('http://geek.itheima.net/v1_0/channels')
        dispatch(getChannelListAc(res.data.data.channels))
    }
}
export const changeActive = (id) => ({
    type: CHANNEL_ACTIVE,
    id,
})
```

3. `store/reducers/channel.js`

```js
import { CHANNEL_ACTIVE, CHANNEL_GET } from '../constants/channel'

const initState = {
    list: [],
    active: 0,
}

export default function channel(state = initState, action) {
    switch (action.type) {
        case CHANNEL_GET:
            return {
                ...state,
                list: action.payload,
            }
        case CHANNEL_ACTIVE:
            return {
                ...state,
                active: action.id,
            }
        default:
            return state
    }
}
```

4. `Components/Channel.js`

```js
import React, { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { changeActive, getChannelList } from '../store/actions/channel'

export default function Channel() {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    const list = useSelector((state) => state.channel.list)
    const active = useSelector((state) => state.channel.active)
    return (
        <ul className='catagtory'>
            {list.map((item) => (
                <li key={item.id} className={item.id === active ? 'select' : ''} onClick={() => dispatch(changeActive(item.id))}>
                    {item.name}
                </li>
            ))}
        </ul>
    )
}
```

### 请求数据

1. `store/constants/channel.js`

```js
export const CHANNEL_GET = 'CHANNEL_GET'
export const CHANNEL_ACTIVE = 'CHANNEL_ACTIVE'
// 这个可以放在单独的 news.js 中，或者只保留一个 constants/index.js 文件即可
export const NEWS_GET = 'NEWS_GET'
```

2. `store/actions/news.js`

```js
import axios from 'axios'
import { NEWS_GET } from '../constants/channel'

export const getNewsListAc = (payload) => ({
    type: NEWS_GET,
    payload,
})

export const getNewsList = (id) => {
    return async (dispatch) => {
        const res = await axios.get(`http://geek.itheima.net/v1_0/articles?channel_id=${id}&timestamp=${Date.now()}`)
        dispatch(getNewsListAc(res.data.data.results))
    }
}
```

3. `store/reducers/news.js`

```js
import { NEWS_GET } from '../constants/channel'

export default function news(state = [], action) {
    switch (action.type) {
        case NEWS_GET:
            return action.payload
        default:
            return state
    }
}
```

4. `components/NewsList.js`

```js
import React, { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import avatar from '../assets/back.jpg'
import { getNewsList } from '../store/actions/news'

export default function NewsList() {
    const active = useSelector((state) => state.channel.active)
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getNewsList(active))
    }, [active, dispatch])
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

### 渲染列表

```js
import React, { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import avatar from '../assets/back.jpg'
import { getNewsList } from '../store/actions/news'

export default function NewsList() {
    const active = useSelector((state) => state.channel.active)
    const list = useSelector((state) => state.news)
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getNewsList(active))
    }, [active, dispatch])
    return (
        <div className='list'>
            {list.map((item) => (
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
            ))}
        </div>
    )
}
```
