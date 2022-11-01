---
title: 20_极客园 H5
date: 2022-01-08 12:47:26
tags:
---

## 今日目标

✔ 搜索功能。

<!-- more -->

## 文章搜索页的静态结构

### 目标

实现文章搜索页面的主要静态结构和样式。

### 步骤

1. 为首页 Tab 栏右边的 ”放大镜“ 按钮添加点击事件，点击后跳转到搜索页。

`Home/index.tsx`

```ts
<Icon type='iconbtn_search' onClick={() => history.push('/search')} />
```

2. 把 Search 模板粘贴到 pages 目录并配置路由。

`App.jsx`

```ts
<Route path='/search' component={Search} />
```

## 搜索关键字的输入与防抖处理

### 目标

从文本输入框获取输入的关键字内容，且运用防抖机制降低获取频繁。

实现思路：1. 清理之前的定时器，2. 新建定时器执行任务。

### 步骤

`pages/Search/index.tsx`

```ts
import { useState, useRef, useEffect } from 'react'
import classnames from 'classnames'
import { useHistory } from 'react-router'
import { NavBar, Search } from 'antd-mobile'
import Icon from '@/components/Icon'
import styles from './index.module.scss'

const SearchPage = () => {
    const timerRef = useRef(-1)
    const history = useHistory()
    // #1
    const [keyword, setKeyword] = useState('')
    // #2
    const handleChange = (v: string) => {
        setKeyword(v)
        // #3
        clearTimeout(timerRef.current)
        // #4
        timerRef.current = window.setTimeout(() => {
            // 防抖
            console.log(v)
        }, 500)
    }
    // #5
    useEffect(() => () => clearTimeout(timerRef.current), [])
    return (
        <div className={styles.root}>
            <NavBar className='navbar' onBack={() => history.go(-1)} right={<span className='search-text'>搜索</span>}>
                <Search placeholder='请输入关键字搜索' value={keyword} onChange={handleChange} />
            </NavBar>

            {true && (
                <div
                    className='history'
                    style={{
                        display: true ? 'none' : 'block',
                    }}
                >
                    <div className='history-header'>
                        <span>搜索历史</span>
                        <span>
                            <Icon type='iconbtn_del' />
                            清除全部
                        </span>
                    </div>

                    <div className='history-list'>
                        <span className='history-item'>
                            <span className='text-overflow'>黑马程序员</span>
                            <Icon type='iconbtn_essay_close' />
                        </span>
                    </div>
                </div>
            )}

            <div className={classnames('search-result', true ? 'show' : '')}>
                <div className='result-item'>
                    <Icon className='icon-search' type='iconbtn_search' />
                    <div className='result-value text-overflow'>
                        <span>黑马</span>
                        程序员
                    </div>
                </div>
            </div>
        </div>
    )
}

export default SearchPage
```

## aHooks 的使用

### 步骤

1. 安装 ahooks 包：`yarn add ahooks`。

2. 导入 `useDebounceFn` hook。

3. 创建防抖函数。

4. 搜索框中输入内容时，调用防抖函数。

### 代码

```ts
const SearchPage = () => {
    const history = useHistory()
    const [keyword, setKeyword] = useState('')
    const { run: getSuggest } = useDebounceFn(
        function (v) {
            console.log(v)
        },
        {
            wait: 500,
        }
    )
    const handleChange = (v: string) => {
        setKeyword(v)
        getSuggest(v)
    }
}
```

## 发送请求获取搜索建议数据

### 目标

将输入的关键发送到服务端，获取和该关键字匹配的建议数据。

实现思路：通过 Redux Action 来发送请求，获取结果数据后保存在 Redux Store 中。

### 代码

`types/data.d.ts`

```ts
export type Suggestion = string[]
```

`types/store.d.ts`

```ts
export type SearchAction = {
    type: 'search/suggestion'
    payload: Suggestion
}
export type RootAction = LoginAction | ProfileAction | HomeAction | SearchAction
```

`actions/search.ts`

```ts
import { ApiResponse, Suggestion } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export const getSuggestion = (value: string): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<{ options: Suggestion }>>('/suggestion', {
            params: {
                q: value,
            },
        })
        dispatch({ type: 'search/suggestion', payload: res.data.data.options })
    }
}
```

`reducers/search.ts`

```ts
import { Suggestion } from '@/types/data'
import { SearchAction } from '@/types/store'
type SearchState = {
    suggestion: Suggestion
}
const initState: SearchState = {
    suggestion: [],
}
export default function search(state = initState, action: SearchAction): SearchState {
    switch (action.type) {
        case 'search/suggestion':
            return {
                ...state,
                suggestion: action.payload,
            }
        default:
            return state
    }
}
```

`reducers/index.ts`

```ts
import { combineReducers } from 'redux'
import login from './login'
import profile from './profile'
import home from './home'
import search from './search'
export default combineReducers({ login, profile, home, search })
```

`Search/index.tsx`

```ts
const { run: getSuggest } = useDebounceFn(
    function (v) {
        dispatch(getSuggestion(v))
    },
    {
        wait: 500,
    }
)
```

## 渲染推荐数据

```ts
const { suggestion } = useSelector((state: RootState) => state.search)

<div className={classnames('search-result', true ? 'show' : '')}>
    {suggestion.map((item, index) => {
        return (
            <div className='result-item' key={index}>
                <Icon className='icon-search' type='iconbtn_search' />
                <div className='result-value text-overflow'>{item}</div>
            </div>
        )
    })}
</div>
```

## 高亮处理

-   基本操作。

```js
const keyword = 'a'
let str = 'abcabc'
str = str.replace(new RegExp(keyword, 'gi'), (match) => `<span>${match}</span>`)
console.log(str)
```

-   项目中使用，[参考文档](https://zh-hans.reactjs.org/docs/dom-elements.html)。

```ts
const highLight = (str: string) => {
    return str.replace(new RegExp(keyword, 'gi'), (match) => `<span>${match}</span>`)
}
```

```ts
<div
    className='result-value text-overflow'
    dangerouslySetInnerHTML={{
        __html: highLight(item),
    }}
/>
```

## 按需展示历史记录和联想建议

### 目标

实现在做搜索操作时只显示搜索建议列表；其他情况只显示搜索历史。

<img src="/resource/images/image-20210908185453822.png" alt="image-20210908185453822" style="zoom:40%;" />

实现思路：利用之前创建的 `isSearching` 状态，控制建议列表和搜索历史的显示、隐藏。

### 步骤

-   提供 isSearching 状态控制联想建议的显示/隐藏。

```jsx
// 是否显示搜索
const [isSearching, setIsSearching] = useState(false)
```

-   修改 isSearching 状态。

```jsx
const onChange = (e: string) => {
    setKeyword(e)
    // 没有输入内容就不要发请求啦，并隐藏之前的联想建议。
    if (e) {
        run(e)
        setIsSearching(true)
    } else {
        setIsSearching(false)
    }
}
```

-   根据 isSearching 状态控制显示和隐藏。

```jsx
{
    /* 历史记录 */
}
;<div className='history' style={{ display: isSearching ? 'none' : 'block' }} />

{
    /* 联想建议 */
}
;<div
    className={classnames('search-result', {
        show: isSearching,
    })}
/>
```

## 内容为空时也清除联想建议

Bug: 输入 a => 清除 => 输入 b，发现先出来之前的旧内容再出来 b 的结果。

`types/store.d.ts`

```ts
export type SearchAction =
    | {
          type: 'search/suggestion'
          payload: Suggestion
      }
    | {
          type: 'search/clearSuggestion'
      }
```

`actions/search.ts`

```ts
export function clearSuggestion(): SearchAction {
    return {
        type: 'search/clearSuggestion',
    }
}
```

`reducers/search.ts`

```ts
import { Suggestion } from '@/types/data'
import { SearchAction } from '@/types/store'

type SearchState = {
    suggestion: Suggestion
}
const initState: SearchState = {
    suggestion: [],
}

export default function search(state = initState, action: SearchAction): SearchState {
    switch (action.type) {
        case 'search/suggestion':
            return {
                ...state,
                suggestion: action.payload,
            }
        case 'search/clearSuggestion':
            return {
                ...state,
                suggestion: [],
            }
        default:
            return state
    }
}
```

`pages/Search/index.tsx`

```ts
const handleChange = (v: string) => {
    setKeyword(v)
    if (v) {
        getSuggest(v)
        setIsSearching(true)
    } else {
        setIsSearching(false)
        // !清空联想建议的内容
        dispatch(clearSuggestion())
    }
}
```

## 点击搜索添加历史记录

### 目标

点击顶部 ”搜索“ 按钮，或点击搜索建议列表中的一项，跳转到搜索详情页。

<img src="/resource/images/image-20210909103450023.png" alt="image-20210909103450023" style="zoom:40%;" />

### 步骤

-   注册事件进行搜索

`pages/Search/index.tsx`，给搜索按钮和联想建议绑定 onSearch 事件。

-   提供 onSearch 方法

```ts
const onSearch = (key: string) => {
    if (!key) return
    dispatch(addSearchList(key))
}
```

-   定义 action 类型

`types/data.d.ts`

```ts
export type Histories = string[]
```

`types/store.d.ts`

```ts
export type SearchAction =
    | {
          type: 'search/suggestion'
          payload: Suggestion
      }
    | {
          type: 'search/clearSuggestion'
      }
    | {
          type: 'search/saveHistories'
          payload: string
      }
```

-   在 reducers/search.ts 中处理 history

```ts
import { Histories, Suggestion } from '@/types/data'
import { SearchAction } from '@/types/store'

type SearchState = {
    suggestion: Suggestion
    histories: Histories
}
const initState: SearchState = {
    suggestion: [],
    histories: [],
}

export default function search(state = initState, action: SearchAction): SearchState {
    switch (action.type) {
        case 'search/suggestion':
            return {
                ...state,
                suggestion: action.payload,
            }
        case 'search/clearSuggestion':
            return {
                ...state,
                suggestion: [],
            }
        case 'search/saveHistories': {
            return {
                ...state,
                histories: [action.payload, ...state.histories],
            }
        }
        default:
            return state
    }
}
```

-   actions 中处理添加历史记录的 action

`actions/search.ts`

```ts
export function addSearchList(keyword: string): SearchAction {
    return {
        type: 'search/saveHistories',
        payload: keyword,
    }
}
```

## 准备搜索的 action 和 reducer

`types/store.d.ts`

```ts
export type SearchAction =
    | {
          type: 'search/suggestion'
          payload: Suggestion
      }
    | {
          type: 'search/clearSuggestion'
      }
    | {
          type: 'search/saveHistories'
          payload: string[]
      }
```

`reducers/search.ts`

```ts
case 'search/saveHistories': {
    return {
        ...state,
        // histories: [action.payload, ...state.histories],
        histories: action.payload,
    }
}
```

`actions/search.ts`

```ts
export function addSearchList(keyword: string): RootThunkAction {
    return (dispatch, getState) => {
        let { histories } = getState().search
        // 判断 keyword 在 hisrories 中是否存在，如果存在，删除
        histories = histories.filter((item) => item !== keyword)
        if (histories.length >= 10) {
            histories.pop()
        }
        histories.unshift(keyword)
        dispatch({
            type: 'search/saveHistories',
            payload: histories,
        })
    }
}

export function removeSearchList(keyword: string): RootThunkAction {
    return (dispatch, getState) => {
        let { histories } = getState().search
        // 判断 keyword 在 hisrories 中是否存在，如果存在，删除
        histories = histories.filter((item) => item !== keyword)
        dispatch({
            type: 'search/saveHistories',
            payload: histories,
        })
    }
}

export function clearHistory(): SearchAction {
    return {
        type: 'search/saveHistories',
        payload: [],
    }
}
```

## 渲染历史记录

`pages/Search/index.tsx`，渲染、删除和清空。

```ts
<div
    className='history'
    style={{
        display: isSearching ? 'none' : 'block',
    }}
>
    <div className='history-header'>
        <span>搜索历史</span>
        <span onClick={() => dispatch(clearHistory())}>
            <Icon type='iconbtn_del' />
            清除全部
        </span>
    </div>
    <div className='history-list'>
        {histories.map((item, index) => (
            <span className='history-item' key={index} onClick={() => onSearch(item)}>
                <span className='text-overflow'>{item}</span>
                <Icon type='iconbtn_essay_close' onClick={() => dispatch(removeSearchList(item))} />
            </span>
        ))}
    </div>
</div>
```

## 历史记录的持久化

### 目标

将每次输入的搜索关键字记录下来，再动态渲染到界面上。

实现思路：

-   在成功搜索后，将关键字存入 Redux 和 LocalStorage 中。

-   从 Redux 中获取所有关键字，并渲染到界面。

### 步骤

-   封装本地存储

`utils/storage.ts`

```ts
const SEARCH_HIS_KEY = 'SEARCH_HIS_KEY'
// 取
export const getLocalHistories = (): Histories => {
    return JSON.parse(localStorage.getItem(SEARCH_HIS_KEY) || '[]')
}
// 存
export const setLocalHisories = (histories: Histories): void => {
    localStorage.setItem(SEARCH_HIS_KEY, JSON.stringify(histories))
}
// 删
export const removeLocalHistories = () => {
    localStorage.removeItem(SEARCH_HIS_KEY)
}
```

-   `reducers/search.ts`

```ts
import { Histories, Suggestion } from '@/types/data'
import { SearchAction } from '@/types/store'
import { getLocalHistories, setLocalHisories } from '@/utils/storage'

type SearchState = {
    suggestion: Suggestion
    histories: Histories
}
const initState: SearchState = {
    suggestion: [],
    // #2
    histories: getLocalHistories(),
}

export default function search(state = initState, action: SearchAction): SearchState {
    switch (action.type) {
        case 'search/suggestion':
            return {
                ...state,
                suggestion: action.payload,
            }
        case 'search/clearSuggestion':
            return {
                ...state,
                suggestion: [],
            }
        case 'search/saveHistories': {
            // #1
            setLocalHisories(action.payload)
            return {
                ...state,
                histories: action.payload,
            }
        }
        default:
            return state
    }
}
```

## 搜索的 Bug

输入 aaaaa，快速删除所有，查看 Network 会发现最后的 q 还是 a。

`pages/Search/index.tsx`

```ts
const { run: getSuggest } = useDebounceFn(
    function () {
        // 曾经这里的 v 永远不可能为空
        if (!keyword) return
        dispatch(getSuggestion(keyword))
    },
    {
        wait: 500,
    }
)
```

## 清空搜索历史记录

TODO: 本地的也要清除。

### 目标

点击”清除全部“按钮后，删除全部的搜索历史记录。

<img src="/resource/images/image-20210909102100740.png" alt="image-20210909102100740" style="zoom:40%;" />

实现思路：删除 Redux 和 LocalStorage 中存储的历史记录。

### 步骤

-   给清空按钮注册点击事件

```jsx
<div className='history-header'>
    <span>搜索历史</span>
    <span onClick={onClearHistory}>
        <Icon type='iconbtn_del' />
        清除全部
    </span>
</div>
```

-   显示弹窗

```jsx
const onClearHistory = () => {
    // 清空历史记录
    Dialog.confirm({
        title: '温馨提示',
        content: '你确定要清空记录吗？',
        onConfirm: function () {
            dispatch(clearHistories())
        },
    })
}
```

-   在 actions 中准备 action

```jsx
/**
 * 清空历史记录
 * @returns
 */
export function clearHistories(): RootThunkAction {
    return async (dispatch) => {
        // 清空本地历史记录
        removeLocalHistories()
        // 清空redux数据
        dispatch({
            type: 'search/clearHistories',
        })
    }
}
```

-   在 reducers 中处理 action

```jsx
export type SearchAction = {
    type: 'search/clearHistories',
}

export default function reducer(state = initValue, action: SearchAction) {
    if (action.type === 'search/clearHistories') {
        return {
            ...state,
            histories: [],
        }
    }
    return state
}
```

## 搜索详情页的静态结构

### 目标

实现搜索详情页的静态结构和样式。

<img src="/resource/images/image-20210909105543672.png" alt="image-20210909105543672" style="zoom:40%;" />

### 步骤

1. 将资源包中对应的样式文件，拷贝到 `pages/Search/Result/`目录下。

2. 配置路由。

App.tsx

```jsx
import SearchResult from './pages/Search/Result'
<Route exact path='/search' component={Search} />
<Route path='/search/result' component={SearchResult} />
```

3. 搜索的时候需要跳转

```ts
const onSearch = (key: string) => {
    if (!key) return
    dispatch(addSearchList(key))
    history.push('/search/result?key=' + key)
}
```

## 获取查询参数

```ts
import { useHistory, useLocation } from 'react-router-dom'
import { NavBar } from 'antd-mobile'

import styles from './index.module.scss'

const Result = () => {
    const history = useHistory()
    const location = useLocation()
    // location.search => ?key=a
    const search = new URLSearchParams(location.search)
    const value = search.get('key')!
    console.log(value)
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)}>搜索结果</NavBar>
            <div className='article-list'>
                <div className='article-item'>文章列表</div>
            </div>
        </div>
    )
}

export default Result
```

URLSearchParams 的另一种用法。

```ts
const search = new URLSearchParams()
search.append('name', 'ifer')
search.append('age', 18)
console.log(search)
```

处理参数：当数据带有 + 号或空格的时候会有问题。

```ts
const search = decodeURI(location.search.replace('?keyword=', ''))
```

## 请求搜索详情页数据

### 目标

获取从搜索页面传入的参数后，调用后端接口获取搜索详情。

### 步骤

1. 获取通过 URL 地址传入到搜索详情页的查询字符串参数 `q`

`types/store.d.ts`

```ts
export type SearchAction =
    | {
          type: 'search/suggestion'
          payload: Suggestion
      }
    | {
          type: 'search/clearSuggestion'
      }
    | {
          type: 'search/saveHistories'
          payload: string[]
      }
    | {
          type: 'search/saveHistoriesResult'
          payload: SearchResult
      }
```

`types/data.d.ts`

```ts
export type SearchResult = {
    page: number
    per_page: number
    total_count: number
    results: Article[]
}
```

2. 在 `store/reducers/search.ts` 中添加保存搜索详情数据的 Reducer 逻辑

```ts
import { Histories, SearchResult, Suggestion } from '@/types/data'
import { SearchAction } from '@/types/store'
import { getLocalHistories, setLocalHisories } from '@/utils/storage'

type SearchState = {
    suggestion: Suggestion
    histories: Histories
    // #1
    searchResult: SearchResult
}
const initState: SearchState = {
    suggestion: [],
    histories: getLocalHistories(),
    // #2
    searchResult: {} as SearchResult,
}

export default function search(state = initState, action: SearchAction): SearchState {
    switch (action.type) {
        case 'search/suggestion':
            return {
                ...state,
                suggestion: action.payload,
            }
        case 'search/clearSuggestion':
            return {
                ...state,
                suggestion: [],
            }
        case 'search/saveHistories': {
            // #1
            setLocalHisories(action.payload)
            return {
                ...state,
                histories: action.payload,
            }
        }
        // #3
        case 'search/saveHistoriesResult':
            return {
                ...state,
                searchResult: action.payload,
            }
        default:
            return state
    }
}
```

3. 在 `store/actions/search.ts` 中编写 Action Creator。

```ts
export function getSearchResults(keyword: string, page: number = 1, per_page = 10): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<SearchResult>>('/search', {
            params: {
                q: keyword,
                page,
                per_page,
            },
        })
        dispatch({
            type: 'search/saveHistoriesResult',
            payload: res.data.data,
        })
    }
}
```

4. 请求结果并展示。

`pages/Search/Result/index.tsx`

```ts
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { useHistory, useLocation } from 'react-router-dom'
import { NavBar } from 'antd-mobile'
import styles from './index.module.scss'
import { getSearchResults } from '@/store/actions/search'
import { RootState } from '@/types/store'
import ArticleItem from '@/pages/Home/components/ArticleItem'

const Result = () => {
    const history = useHistory()
    const location = useLocation()
    const dispatch = useDispatch()
    const search = new URLSearchParams(location.search)
    const keyword = search.get('key')!
    // #2
    const {
        searchResult: { results = [] },
    } = useSelector((state: RootState) => state.search)
    // #1
    useEffect(() => {
        dispatch(getSearchResults(keyword))
    }, [dispatch, keyword])
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)}>搜索结果</NavBar>
            <div className='article-list'>
                {/* #3 */}
                {results.map((item) => (
                    <div className='article-item' key={item.art_id}>
                        <ArticleItem article={item} />
                    </div>
                ))}
            </div>
        </div>
    )
}

export default Result
```

## 图片 403 问题

```html
<meta name="referrer" content="no-referrer" />
```

## 加载更多数据

`pages/Search/Result/index.tsx`

```ts
import { useEffect, useRef, useState } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { useHistory, useLocation } from 'react-router-dom'
// #1
import { NavBar, InfiniteScroll } from 'antd-mobile'
import styles from './index.module.scss'
import { getSearchResults } from '@/store/actions/search'
import { RootState } from '@/types/store'
import ArticleItem from '@/pages/Home/components/ArticleItem'

const Result = () => {
    const history = useHistory()
    const location = useLocation()
    const dispatch = useDispatch()
    const search = new URLSearchParams(location.search)
    const keyword = search.get('key')!
    const {
        searchResult: { results = [] },
    } = useSelector((state: RootState) => state.search)
    // #3
    const hasMore = results.length < 100
    // #4
    const pageRef = useRef(1)
    const bBarRef = useRef(true)
    // #5
    const loadMore = async () => {
        // #6 节流
        if (bBarRef.current) {
            bBarRef.current = false
            await dispatch(getSearchResults(keyword, pageRef.current))
            pageRef.current++
            bBarRef.current = true
        }
    }
    // !务必注释
    /* useEffect(() => {
        dispatch(getSearchResults(keyword))
    }, [dispatch, keyword]) */
    return (
        <div className={styles.root}>
            <NavBar onBack={() => history.go(-1)}>搜索结果</NavBar>
            <div className='article-list'>
                {results.map((item) => (
                    <div className='article-item' key={item.art_id}>
                        <ArticleItem article={item} />
                    </div>
                ))}
                {/* #2: 注意放的位置，丢外面会导致不会被内容撑下去，永远在可视区 */}
                <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
            </div>
        </div>
    )
}

export default Result
```

reducers/history.ts

```ts
import { Histories, SearchResult, Suggestion } from '@/types/data'
import { SearchAction } from '@/types/store'
import { getLocalHistories, setLocalHisories } from '@/utils/storage'

type SearchState = {
    suggestion: Suggestion
    histories: Histories
    searchResult: SearchResult
}
const initState: SearchState = {
    suggestion: [],
    histories: getLocalHistories(),
    searchResult: {} as SearchResult,
}

export default function search(state = initState, action: SearchAction): SearchState {
    switch (action.type) {
        case 'search/suggestion':
            return {
                ...state,
                suggestion: action.payload,
            }
        case 'search/clearSuggestion':
            return {
                ...state,
                suggestion: [],
            }
        case 'search/saveHistories': {
            setLocalHisories(action.payload)
            return {
                ...state,
                histories: action.payload,
            }
        }
        // #mark
        case 'search/saveHistoriesResult':
            const old = state.searchResult.results || []
            return {
                ...state,
                searchResult: {
                    ...state.searchResult,
                    results: [...old, ...action.payload.results],
                },
            }
        default:
            return state
    }
}
```

## 跳转到详情

### 目标

将请求到的搜索详情数据渲染到界面上。

### 步骤

`pages/Home/components/ArticleList/index.tsx`

```ts
// 注释掉 .article-item 的点击
{
    articles.map((item) => (
        <div key={item.art_id} className='article-item'>
            <ArticleItem article={item} />
        </div>
    ))
}
```

把点击事件绑定到 `pages/Home/components/ArticleItem/index.tsx`

```ts
<div className={styles.root} onClick={() => history.push(`/article/${art_id}`)}></div>
```

## 获取详情数据

-   `types/data.d.ts`

```ts
export type ArticleDetail = {
    art_id: string
    attitude: number
    aut_id: string
    aut_name: string
    aut_photo: string
    comm_count: number
    content: string
    is_collected: boolean
    is_followed: boolean
    like_count: number
    pubdate: string
    read_count: number
    title: string
}
```

-   `actions/article.ts`

```ts
import { ApiResponse, ArticleDetail } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export function getArticleInfo(id: string): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<ArticleDetail>>(`/articles/${id}`)
        console.log(res.data.data)
    }
}
```

-   `pages/Article/index.tsx`

```ts
const Article = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const params = useParams<{ id: string }>()
    const articleId = params.id
    useEffect(() => {
        dispatch(getArticleInfo(articleId))
    }, [dispatch, articleId])
}
```

## 将详情保存到 Redux 中

-   store.d.ts 中增加文章详情的类型。

```ts
export type ArticleAction = {
    type: 'article/setArticleInfo'
    payload: ArticleDetail
}
export type RootAction = LoginAction | ProfileAction | HomeAction | SearchAction | ArticleAction
```

-   修改 `actions/article.ts`，分发 Action。

```ts
import { ApiResponse, ArticleDetail } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export function getArticleInfo(id: string): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<ArticleDetail>>(`/articles/${id}`)
        dispatch({
            type: 'article/setArticleInfo',
            payload: res.data.data,
        })
    }
}
```

-   在 reducers/article.ts 中存储数据。

```ts
import { ArticleDetail } from '@/types/data'
import { ArticleAction } from '@/types/store'
type ArticleType = {
    info: ArticleDetail
}
const initState: ArticleType = {
    info: {},
} as ArticleType

export default function article(state = initState, action: ArticleAction): ArticleType {
    switch (action.type) {
        case 'article/setArticleInfo':
            return {
                ...state,
                info: action.payload,
            }
        default:
            return state
    }
}
```

-   在 reducers/index.ts 中。

```ts
import article from './article'
export default combineReducers({ article })
```

## 文章详情-基本渲染

-   获取文章详情数据

```ts
const { info } = useSelector((state: RootState) => state.article)
```

-   渲染文章详情

```jsx
<div className='article-wrapper'>
    <div className='header'>
        <h1 className='title'>{info.title}</h1>
        <div className='info'>
            <span>{dayjs(info.pubdate).format('YYYY-MM-DD')}</span>
            <span>{info.read_count} 阅读</span>
            <span>{info.comm_count} 评论</span>
        </div>

        <div className='author'>
            <img src={info.aut_photo} alt='' />
            <span className='name'>{info.aut_name}</span>
            <span className={classNames('follow', info.is_followed ? 'followed' : '')}>{info.is_followed ? '已关注' : '关注'}</span>
        </div>
    </div>

    <div className='content'>
        <div
            className='content-html dg-html'
            dangerouslySetInnerHTML={{
                __html: info.content,
            }}
        />
        <div className='date'>发布文章时间：{dayjs(info.pubdate).format('YYYY-MM-DD')}</div>
    </div>
</div>
```

## 文章详情 useInitState 优化获取

`pages/Article/index.tsx`

-   使用 useInitState 来优化请求的获取，但是会遇到传参的问题，，因此需要包裹一个函数。

```ts
import { NavBar, InfiniteScroll } from 'antd-mobile'
import { useHistory, useParams } from 'react-router-dom'
import classNames from 'classnames'
import styles from './index.module.scss'
import Icon from '@/components/Icon'
import CommentItem from './components/CommentItem'
import CommentFooter from './components/CommentFooter'
import { getArticleInfo } from '@/store/actions/article'
import dayjs from 'dayjs'
import { useInitState } from '@/utils/hooks'

const Article = () => {
    const history = useHistory()
    const params = useParams<{ id: string }>()
    const articleId = params.id
    /* const { info } = useSelector((state: RootState) => state.article)
    useEffect(() => {
        dispatch(getArticleInfo(articleId))
    }, [dispatch, articleId]) */
    const { info } = useInitState(() => getArticleInfo(articleId), 'article')
    const renderArticle = () => {
        // 文章详情
        return (
            <div className='wrapper'>
                {/* //!文章基本信息 */}
                <div className='article-wrapper'>
                    <div className='header'>
                        <h1 className='title'>{info.title}</h1>
                        <div className='info'>
                            <span>{dayjs(info.pubdate).format('YYYY-MM-DD')}</span>
                            <span>{info.read_count} 阅读</span>
                            <span>{info.comm_count} 评论</span>
                        </div>

                        <div className='author'>
                            <img src={info.aut_photo} alt='' />
                            <span className='name'>{info.aut_name}</span>
                            <span className={classNames('follow', info.is_followed ? 'followed' : '')}>{info.is_followed ? '已关注' : '关注'}</span>
                        </div>
                    </div>

                    <div className='content'>
                        <div
                            className='content-html dg-html'
                            dangerouslySetInnerHTML={{
                                __html: info.content,
                            }}
                        />
                        <div className='date'>发布文章时间：{dayjs(info.pubdate).format('YYYY-MM-DD')}</div>
                    </div>
                </div>
                {/* //!评论信息 */}
                <div className='comment'>
                    <div className='comment-header'>
                        <span>全部评论（101）</span>
                        <span>20 点赞</span>
                    </div>

                    <div className='comment-list'>
                        <CommentItem />
                        <InfiniteScroll
                            hasMore={false}
                            loadMore={async () => {
                                console.log(1)
                            }}
                        />
                    </div>
                </div>
            </div>
        )
    }

    return (
        <div className={styles.root}>
            <div className='root-wrapper'>
                <NavBar
                    onBack={() => history.go(-1)}
                    right={
                        <span>
                            <Icon type='icongengduo' />
                        </span>
                    }
                >
                    {true && (
                        <div className='nav-author'>
                            <img src='http://geek.itheima.net/images/user_head.jpg' alt='' />
                            <span className='name'>黑马先锋</span>
                            <span className={classNames('follow', true ? 'followed' : '')}>{true ? '已关注' : '关注'}</span>
                        </div>
                    )}
                </NavBar>
                {/* 文章详情和评论 */}
                {renderArticle()}
                {/* //!底部评论栏 */}
                <CommentFooter />
            </div>
        </div>
    )
}

export default Article
```

-   使用 `useInitialState` 自定义 hook 会重复发送请求

```
针对该问题的分析过程：

1. 定位出问题的代码位置

  - 既然造成了重复请求，说明 dispatch 分发 action 的代码重复执行了。可以通过 console.log 来确认，是否会重复执行

2. 分析原因

  - dispatch 是在 useEffect hook 中执行的，说明 effect 重复执行。而 effect 重复执行的原因只有一个，就是：依赖项发生改变
  - 第一个依赖项 `dispatch` 函数是不变的
  - 只能是第二个依赖项 action 函数改变了

3. 确认分析是否正确
  - Reesult 组件中 `useInitialState` 重复执行，也就是重复更新了状态，每次更新状态都会导致组件重新渲染。
  - 组件重新渲染时，会重新执行组件中的所有代码。
  - 而我们传递给 `useInitialState` hook 的第一个回调函数，每次都会重新创建
```

-   解决思路：减少 action 的依赖

解决方法 1，`src/utils/hooks.ts`，去掉 action 依赖。

解决方法 2

```ts
import { RootState } from '@/types/store'
import { useEffect, useRef } from 'react'
import { useDispatch, useSelector } from 'react-redux'
export function useInitState<T extends keyof RootState>(action: () => void, stateName: T) {
    const dispatch = useDispatch()
    const actionRef = useRef(action)
    useEffect(() => {
        const actionFn = actionRef.current
        dispatch(actionFn())
    }, [dispatch])
    const state = useSelector((state: RootState) => state[stateName])
    return state
}
```

## 文章详情-防 XSS 攻击

### 目标

清理正文中的不安全元素，防止 XSS 安全漏洞。

实现思路：使用 `dompurify` 对 HTML 内容进行净化处理

### 演示

```ts
import { useState } from 'react'
const Question = () => {
    const [v, setV] = useState('')
    const onChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
        setV(e.target.value)
    }
    return (
        <div className={styles.root}>
            <textarea value={v} onChange={onChange} />
            <div
                dangerouslySetInnerHTML={{
                    __html: v,
                }}
            ></div>
            {/* <img src='http://geek.itheima.net/resources/images/19.jpg' onload='document.body.innerHTML = "铁子";'/> */}
        </div>
    )
}

export default Question
```

```js
console.log(dompurify.sanitize(`<img src='http://geek.itheima.net/resources/images/19.jpg' onload='document.body.innerHTML = "铁子";'/>`))
```

### 步骤

1. 安装包

```bash
yarn add dompurify @types/dompurify
```

2. 在页面中调用 `dompurify` 来对文章正文内容做净化。

```ts
import DOMPurify from 'dompurify'
```

```ts
<div className='content-html dg-html' dangerouslySetInnerHTML={{ __html: dompurify.sanitize(info.content || '') }} />
```

## 文章详情-代码高亮

### 目标

实现嵌入文章中的代码带有语法高亮效果。

实现思路：

-   通过 `highlight.js` 库实现对文章正文 HTML 中的代码元素自动添加语法高亮

### 步骤

-   安装包

```bash
yarn add highlight.js
```

-   在页面中引入 `highlight.js`

`pages/Article/index.tsx`

```ts
import hljs from 'highlight.js'
import 'highlight.js/styles/vs2015.css'
```

-   在文章加载后，对文章内容中的代码进行语法高亮

```ts
useEffect(() => {
    // !其实后端返回的内容已经自带了高亮的类名，只需要引入 CSS 即可
    // 配置 highlight.js
    hljs.configure({
        // 忽略未经转义的 HTML 字符
        ignoreUnescapedHTML: true,
    })
    // 获取到内容中所有的code标签
    const codes = document.querySelectorAll('.dg-html pre code')
    codes.forEach((el) => {
        // 让 code 进行高亮
        hljs.highlightElement(el as HTMLElement)
    })
}, [articleId])
```

## 文章详情-控制头部的显示和隐藏

### 思路

-   为顶部导航栏组件 NavBar 设置中间部分的内容

-   监听页面的 `scroll` 事件，在页面滚动时判断描述信息区域的 `top` 是否小于等于 0；如果是，则将 NavBar 中间内容设置为显示；否则设置为隐藏

### 步骤

-   为顶部导航栏添加作者信息

```ts
<div className='nav-author'>
    <img src={info.aut_photo} alt='' />
    <span className='name'>{info.aut_name}</span>
    <span className={classNames('follow', info.is_followed ? 'followed' : '')}>{info.is_followed ? '已关注' : '关注'}</span>
</div>
```

-   声明状态和对界面元素的引用

```ts
const [isShowAuthor, setIsShowAuthor] = useState(false)
const authorRef = useRef<HTMLDivElement>(null)
const wrapRef = useRef<HTMLDivElement>(null)
<div className="wrapper" ref={wrapRef}>
<div className="author" ref={authorRef}>
```

-   控制显示隐藏的逻辑

```ts
useEffect(() => {
    const wrapDOM = wrapRef.current!
    const authDOM = authorRef.current!
    const onScroll = function () {
        const rect = authDOM.getBoundingClientRect()!
        console.log(rect.top)

        if (rect.top <= 0) {
            setIsShowAuthor(true)
        } else {
            setIsShowAuthor(false)
        }
    }
    wrapDOM.addEventListener('scroll', onScroll)
    return () => {
        wrapDOM.removeEventListener('scroll', onScroll)
    }
}, [])
```
