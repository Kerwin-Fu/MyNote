---
title: 19_极客园 H5
date: 2022-01-07 22:05:48
tags:
---

## 今日目标

✔ 频道管理。

<!-- more -->

## 页面结构

### 目标

能够根据模板搭建首页页面结构。

### 步骤

`pages/Home/index.tsx`

```ts
import Icon from '@/components/Icon'

import styles from './index.module.scss'

const Home = () => {
    return (
        <div className={styles.root}>
            {/* 频道 Tabs 列表 */}
            <div className='tabs-opration'>
                <Icon type='iconbtn_search' />
                <Icon type='iconbtn_channel' />
            </div>
        </div>
    )
}

export default Home
```

`pages/Home/index.module.scss`

```scss
.root {
    height: 100%;

    :global {
        .tabs {
            height: 100%;
            padding: 44px 0 50px 0;

            .adm-tabs-header {
                position: fixed;
                top: 0;
                z-index: 2;
                height: 44px;
                width: calc(100vw - 86px);
                background-color: #fff;
            }

            .adm-tabs-tab {
                line-height: 25px;
                color: #9ea1ae;
                font-size: 15px;
            }
            .adm-tabs-tab-active {
                color: var(--adm-color-text);
                font-size: 18px;
            }

            .adm-tabs-tab-line {
                height: 3px;
                border-radius: 2px;
            }

            .adm-tabs-content {
                height: 100%;
                padding: 0;
            }
        }

        .tabs-opration {
            position: absolute;
            top: 0;
            right: 0;
            z-index: 2;
            display: flex;
            align-items: center;
            width: 86px;
            height: 44px;
            padding: 0 14px;
            background: #fff;
            border-bottom: 1px solid #efefef;

            .icon {
                color: #9ea1ae;
                font-size: 18px;

                &:first-child {
                    margin-right: 21px;
                }
            }
        }
    }
}

:global {
    .channel-popup {
        .adm-popup-body {
            overflow-y: scroll;
        }
    }
}
```

## 使用 Tabs 组件

### 目标

能够掌握 antd-mobile 组件中 Tabs 组件的使用。

### 步骤

1. 打开 antd-mobile 组件库的文档，找到 Tabs 组件。

2. 找到示例代码，并拷贝到项目中。

3. 分析 Tabs 组件的结构和基本使用。

4. 调整 Tabs 的样式。

**核心代码**：

`pages/Home/index.tsx`

```ts
import Icon from '@/components/Icon'
import { Tabs } from 'antd-mobile'
import styles from './index.module.scss'

const Home = () => {
    return (
        <div className={styles.root}>
            <Tabs className='tabs' activeLineMode='fixed'>
                <Tabs.Tab title='推荐' key='1'>
                    推荐频道的内容
                </Tabs.Tab>
                <Tabs.Tab title='html' key='2'>
                    html频道的内容
                </Tabs.Tab>
                <Tabs.Tab title='开发者资讯' key='3'>
                    开发者资讯频道的内容
                </Tabs.Tab>
                <Tabs.Tab title='c++' key='4'>
                    c++频道的内容
                </Tabs.Tab>
                <Tabs.Tab title='css' key='5'>
                    css频道的内容
                </Tabs.Tab>
            </Tabs>
            {/* 频道 Tabs 列表 */}
            <div className='tabs-opration'>
                <Icon type='iconbtn_search' />
                <Icon type='iconbtn_channel' />
            </div>
        </div>
    )
}

export default Home
```

## 获取频道列表数据

### 目标

能够获取到首页的频道列表数据。

### 步骤

`types/data.d.ts`

```ts
export type Channel = {
    id: number
    name: string
}
```

`actions/home.ts`

```ts
import { ApiResponse, Channel } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export const getUserChannel = (): RootThunkAction => {
    return async (dispatch, getState) => {
        const res = await request.get<ApiResponse<{ channels: Channel[] }>>('/user/channels')
        const { channels } = res.data.data
        console.log('频道', channels)
    }
}
```

`pages/Home/index.tsx`

```ts
// 测试测试请求发了没，实际应该拿 'home' 的数据
useInitState(getUserChannel, 'profile')
```

## 获取频道列表数据-存储与渲染

`types/store.d.ts`

```ts
import type { Channel } from './data'

export type HomeAction = {
    type: 'home/saveUserChannels'
    payload: Channel[]
}

type RootAction = LoginAction | ProfileAction | HomeAction
```

`actions/home.ts`

```ts
export function getUserChannel(): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<{ channels: Channel[] }>>('/user/channels')
        dispatch({
            type: 'home/saveUserChannels',
            payload: res.data.data.channels,
        })
    }
}
```

`reducers/home.ts`

```ts
import { Channel } from '@/types/data'
import { HomeAction } from '@/types/store'
type HomeType = {
    userChannels: Channel[]
}
const initValue: HomeType = {
    userChannels: [],
}
export default function home(state = initValue, action: HomeAction): HomeType {
    if (action.type === 'home/saveUserChannels') {
        return {
            ...state,
            userChannels: action.payload,
        }
    }
    return state
}
```

`reducers/index.ts`

```ts
import home from './home'

export default combineReducers({ login, profile, home })
```

渲染频道数据，在组件 Home/index.tsx 中

```ts
import Icon from '@/components/Icon'
import { getUserChannel } from '@/store/actions/home'
import { useInitState } from '@/utils/hooks'
import { Tabs } from 'antd-mobile'
import styles from './index.module.scss'

const Home = () => {
    const { userChannels } = useInitState(getUserChannel, 'home')
    return (
        <div className={styles.root}>
            <Tabs className='tabs' activeLineMode='fixed'>
                {userChannels.map((item) => (
                    <Tabs.Tab title={item.name} key={item.id}>
                        {item.name}的内容
                    </Tabs.Tab>
                ))}
            </Tabs>
            {/* 频道 Tabs 列表 */}
            <div className='tabs-opration'>
                <Icon type='iconbtn_search' />
                <Icon type='iconbtn_channel' />
            </div>
        </div>
    )
}

export default Home
```

解决初始高亮 Tab 的位置问题

```ts
{
    userChannels.length > 0 && (
        <Tabs className='tabs' activeLineMode='fixed'>
            {userChannels.map((item) => (
                <Tabs.Tab title={item.name} key={item.id}>
                    {item.name}的内容
                </Tabs.Tab>
            ))}
        </Tabs>
    )
}
```

## 频道管理-控制弹层显示隐藏

### 目标

能够渲染频道管理弹出层。

### 步骤

1. 将模板 Channels 拷贝到 Home/components 目录中。

2. 在 Home 组件中导入 Channels 组件。

3. 使用 Popup 组件渲染 Channels 内容。

4. 创建控制频道管理弹出层展示或隐藏的状态。

5. 控制弹出层的展示或隐藏。

### 代码

`Home/index.tsx`

```ts
import { useState } from 'react'
import Icon from '@/components/Icon'
import { getUserChannel } from '@/store/actions/home'
import { useInitState } from '@/utils/hooks'
import { Tabs, Popup } from 'antd-mobile'
import styles from './index.module.scss'
import Channels from './components/Channels'

const Home = () => {
    const [visible, setVisible] = useState(false)
    const { userChannels } = useInitState(getUserChannel, 'home')
    const show = () => {
        setVisible(true)
    }
    const hide = () => {
        setVisible(false)
    }
    return (
        <div className={styles.root}>
            {userChannels.length > 0 && (
                <Tabs className='tabs' activeLineMode='fixed'>
                    {userChannels.map((item) => (
                        <Tabs.Tab title={item.name} key={item.id}>
                            {item.name}的内容
                        </Tabs.Tab>
                    ))}
                </Tabs>
            )}
            {/* 频道 Tabs 列表 */}
            <div className='tabs-opration'>
                <Icon type='iconbtn_search' />
                <Icon type='iconbtn_channel' onClick={show} />
            </div>
            <Popup position='left' visible={visible}>
                <Channels hide={hide} />
            </Popup>
        </div>
    )
}

export default Home
```

`Home/components/Channels/index.tsx`

```ts
type Props = {
    hide: () => void
}

const Channels = ({ hide }: Props) => {
    return (
        <div className={styles.root}>
            <div className='channel-header'>
                <Icon type='iconbtn_channel_close' onClick={hide} />
            </div>
        </div>
    )
}
```

## 频道管理-渲染我的频道

### 目标

能够渲染我的频道列表。

### 步骤

我的频道中展示的数据就是在首页中获取到的用户频道列表数据，因此，只需要在频道管理组件中拿到用户频道列表数据即可

1. 在 Channels 中，从 redux 中获取到用户频道数据。

2. 渲染用户频道列表数据。

`Channels/index.tsx`

```ts
import { useSelector } from 'react-redux'
import { RootState } from '@/types/store'

const Channels = ({ hide }: Props) => {
    const { userChannels } = useSelector((state: RootState) => state.home)
    return (
        <div className='channel-list'>
            {/* 选中时，添加类名 selected */}
            {userChannels.map((item) => (
                <span key={item.id} className={classnames('channel-list-item')}>
                    {item.name}
                    <Icon type='iconbtn_tag_close' />
                </span>
            ))}
        </div>
    )
}
```

## 频道管理-获取所有频道

### 目标

能够获取到所有频道的数据。

### 代码

频道推荐中展示的是除了我的频道之外的其他频道数据，由于接口并没有直接提供频道推荐数据，

因此，可以拿到所有频道数据，然后，排除掉我的频道数据，剩下的就是频道推荐数据了。

`types/store.d.ts`

```ts
export type HomeAction =
    | {
          type: 'home/saveUserChannels'
          payload: Channel[]
      }
    | {
          type: 'home/saveAllChannels'
          payload: Channel[]
      }
```

`actions/home.ts`

```ts
export function getAllChannel(): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<{ channels: Channel[] }>>('/channels')
        dispatch({
            type: 'home/saveAllChannels',
            payload: res.data.data.channels,
        })
    }
}
```

`reducers/home.ts`

```ts
import { Channel } from '@/types/data'
import { HomeAction } from '@/types/store'
type HomeType = {
    userChannels: Channel[]
    allChannels: Channel[]
}
const initValue: HomeType = {
    userChannels: [],
    allChannels: [],
}
export default function home(state = initValue, action: HomeAction): HomeType {
    if (action.type === 'home/saveUserChannels') {
        return {
            ...state,
            userChannels: action.payload,
        }
    }
    if (action.type === 'home/saveAllChannels') {
        return {
            ...state,
            allChannels: action.payload,
        }
    }
    return state
}
```

`home/index.tsx`

```ts
const Home = () => {
    useInitialState(getAllChannel, 'home')
}
```

## 频道管理-渲染频道推荐

### 目标

能够渲染频道推荐列表。

### 代码

`Channels/index.tsx`

```ts
const Channels = ({ hide }: Props) => {
    const { userChannels } = useSelector((state: RootState) => state.home)
    const recommendChannels = useSelector((state: RootState) => {
        const { userChannels, allChannels } = state.home
        return allChannels.filter((item) => userChannels.findIndex((v) => v.id === item.id) === -1)
    })
    return (
        <div className='channel-list'>
            {recommendChannels.map((item) => (
                <span key={item.id} className='channel-list-item'>
                    + {item.name}
                </span>
            ))}
        </div>
    )
}
```

使用 lodash 提供的方法，计算出推荐频道。

```bash
yarn add lodash @types/lodash
```

```js
const recommendChannels = differenceBy(allChannels, userChannels, 'id')
```

## 获取频道列表数据-逻辑优化

### 分析

-   如果用户登录了，需要发送请求获取用户的频道

-   如果用户没有登录，从 localstorage 中获取用户的频道信息

-   如果本地没有用户的频道信息，需要发送请求获取默认的频道，把默认频道存储到本地。

### 代码

`utils/storage.ts`

```ts
const CHANNEL_KEY = 'GEEK_H5_CHANNEL'
export function setChannels(channels: Channel[]): void {
    localStorage.setItem(CHANNEL_KEY, JSON.stringify(channels))
}
export function getChannels(): Channel[] {
    return JSON.parse(localStorage.getItem(CHANNEL_KEY) || '[]')
}
```

`actions/home.ts`

```ts
export const getUserChannel = (): RootThunkAction => {
    return async (dispatch) => {
        if (hasToken()) {
            // !#1 登录了
            const res = await request.get<ApiResponse<{ channels: Channel[] }>>('/user/channels')
            dispatch({
                type: 'home/saveUserChannels',
                payload: res.data.data.channels,
            })
        } else {
            // !#2 未登录
            const channels = getChannels()
            if (channels.length > 0) {
                // !#3 本地有
                dispatch({
                    type: 'home/saveUserChannels',
                    payload: channels,
                })
            } else {
                // !#4 本地无
                const res = await request.get<ApiResponse<{ channels: Channel[] }>>('/user/channels')
                dispatch({
                    type: 'home/saveUserChannels',
                    payload: res.data.data.channels,
                })
                // !#5 存储
                setChannels(res.data.data.channels)
            }
        }
    }
}
```

## 频道管理-点击我的频道高亮

### 目标

能够实现切换频道功能。

### 分析

首页顶部的频道和频道管理中的我的频道是关联在一起的：

1. 点击频道管理中的我的频道时，首页顶部的频道会切换，并高亮。

2. 点击首页顶部的频道时，频道管理对应的频道也要高亮。

因此，需要准备一个状态用来记录当前选中频道，并且两个组件中都需要用到该状态，所以，可以直接将该状态存储到 redux 中，实现状态共享。

然后，不管是首页顶部的频道还是频道管理中的我的频道，只需要在点击切换时，修改 redux 中记录的状态值即可。

### 代码

`redcuers/home.ts`

```ts
type HomeType = {
    userChannels: Channel[]
    allChannels: Channel[]
    active: number
}

const initValue: HomeType = {
    userChannels: [],
    allChannels: [],
    active: 0,
}
```

`types/store.d.ts`

```ts
export type HomeAction =
    | {
          type: 'home/saveUserChannels'
          payload: Channel[]
      }
    | {
          type: 'home/saveAllChannels'
          payload: Channel[]
      }
    | {
          type: 'home/changeActive'
          payload: number
      }
```

`actions/home.ts`

```ts
export function changeActive(id: number): HomeAction {
    return {
        type: 'home/changeActive',
        payload: id,
    }
}
```

`reducers/home.ts`

```ts
if (action.type === 'home/changeActive') {
    return {
        ...state,
        active: action.payload,
    }
}
```

`pages/Home/components/Channels/index.tsx`

```ts
import classnames from 'classnames'
import { useSelector, useDispatch } from 'react-redux'
import { RootState } from '@/types/store'
import Icon from '@/components/Icon'
import { differenceBy } from 'lodash'
import styles from './index.module.scss'
import { changeActive } from '@/store/actions/home'
type Props = {
    hide: () => void
}
const Channels = ({ hide }: Props) => {
    const dispatch = useDispatch()
    // !#3 获取 Redux 中的 active 并和循环时候的 item.id 进行比较，命中则应用高亮的 class
    const { userChannels, allChannels, active } = useSelector((state: RootState) => state.home)
    const recommendChannels = differenceBy(allChannels, userChannels, 'id')
    // !#2 修改 Redux 中的 active 并隐藏弹框
    const onChange = (id: number) => {
        dispatch(changeActive(id))
        hide()
    }
    return (
        <div className={styles.root}>
            <div className='channel-header'>
                <Icon type='iconbtn_channel_close' onClick={hide} />
            </div>
            <div className='channel-content'>
                {/* 编辑时，添加类名 edit */}
                <div className={classnames('channel-item')}>
                    <div className='channel-item-header'>
                        <span className='channel-item-title'>我的频道</span>
                        <span className='channel-item-title-extra'>点击进入频道</span>
                        <span className='channel-item-edit'>编辑</span>
                    </div>
                    <div className='channel-list'>
                        {/* //!#1 绑定点击事件 */}
                        {userChannels.map((item) => (
                            <span
                                key={item.id}
                                className={classnames('channel-list-item', {
                                    selected: item.id === active,
                                })}
                                onClick={() => onChange(item.id)}
                            >
                                {item.name}
                                <Icon type='iconbtn_tag_close' />
                            </span>
                        ))}
                    </div>
                </div>
                <div className='channel-item'>
                    <div className='channel-item-header'>
                        <span className='channel-item-title'>频道推荐</span>
                        <span className='channel-item-title-extra'>点击添加频道</span>
                    </div>
                    <div className='channel-list'>
                        {recommendChannels.map((item) => (
                            <span key={item.id} className='channel-list-item'>
                                + {item.name}
                            </span>
                        ))}
                    </div>
                </div>
            </div>
        </div>
    )
}

export default Channels
```

## 频道管理-处理 Tabs 的高亮

### 目标

能够实现首页频道切换和高亮功能。

### 步骤

1. 在 Home 组件中拿到该状态，并设置为 Tabs 组件的 activeKey。

2. 为 Tabs 组件添加 onChange，拿到当前选中的 tab 的键，并且分发 action 来修改 channelActiveKey。

### 核心代码

`Home/index.tsx`

```ts
import { useState } from 'react'
import { useDispatch } from 'react-redux'
import Icon from '@/components/Icon'
import { changeActive, getAllChannel, getUserChannel } from '@/store/actions/home'
import { useInitState } from '@/utils/hooks'
import { Tabs, Popup } from 'antd-mobile'
import styles from './index.module.scss'
import Channels from './components/Channels'

const Home = () => {
    const dispatch = useDispatch()
    const [visible, setVisible] = useState(false)
    const { userChannels, active } = useInitState(getUserChannel, 'home')
    useInitState(getAllChannel, 'home')
    const show = () => {
        setVisible(true)
    }
    const hide = () => {
        setVisible(false)
    }
    // !#2
    const onChange = (key: string) => {
        dispatch(changeActive(+key))
    }
    return (
        <div className={styles.root}>
            {/* //!#1 */}
            {userChannels.length > 0 && (
                <Tabs className='tabs' activeLineMode='fixed' activeKey={active + ''} onChange={onChange}>
                    {userChannels.map((item) => (
                        <Tabs.Tab title={item.name} key={item.id}>
                            {item.name}的内容
                        </Tabs.Tab>
                    ))}
                </Tabs>
            )}
            {/* 频道 Tabs 列表 */}
            <div className='tabs-opration'>
                <Icon type='iconbtn_search' />
                <Icon type='iconbtn_channel' onClick={show} />
            </div>
            <Popup position='left' visible={visible}>
                <Channels hide={hide} />
            </Popup>
        </div>
    )
}

export default Home
```

## 频道管理-编辑状态的处理

### 目标

能够切换频道编辑状态。

### 步骤

1. 添加控制是否为编辑的状态。

2. 给编辑/保存按钮添加点击事件。

3. 在点击事件中切换编辑状态。

4. 根据编辑状态判断展示保存或编辑文字内容。

### 代码

`pages/Home/components/Channels/index.tsx`

```ts
import { useState } from 'react'
import classnames from 'classnames'
import { useSelector, useDispatch } from 'react-redux'
import { RootState } from '@/types/store'
import Icon from '@/components/Icon'
import { differenceBy } from 'lodash'
import styles from './index.module.scss'
import { changeActive } from '@/store/actions/home'
type Props = {
    hide: () => void
}
const Channels = ({ hide }: Props) => {
    const dispatch = useDispatch()
    // !#1
    const [isEdit, setIsEdit] = useState(false)
    const { userChannels, allChannels, active } = useSelector((state: RootState) => state.home)
    const recommendChannels = differenceBy(allChannels, userChannels, 'id')
    const onChange = (id: number) => {
        // !#6 编辑状态不允许切换
        if (isEdit) return
        dispatch(changeActive(id))
        hide()
    }
    // !#4
    const changeEdit = () => {
        setIsEdit(!isEdit)
    }
    return (
        <div className={styles.root}>
            <div className='channel-header'>
                <Icon type='iconbtn_channel_close' onClick={hide} />
            </div>
            <div className='channel-content'>
                {/* //!#2 编辑时，添加类名 edit */}
                <div className={classnames('channel-item', isEdit && 'edit')}>
                    <div className='channel-item-header'>
                        <span className='channel-item-title'>我的频道</span>
                        {/* //!#5 */}
                        <span className='channel-item-title-extra'>{isEdit ? '点击删除频道' : '点击进入频道'}</span>
                        {/* //!#3 */}
                        <span className='channel-item-edit' onClick={changeEdit}>
                            {isEdit ? '完成' : '编辑'}
                        </span>
                    </div>
                    <div className='channel-list'>
                        {userChannels.map((item) => (
                            <span
                                key={item.id}
                                className={classnames('channel-list-item', {
                                    selected: item.id === active,
                                })}
                                onClick={() => onChange(item.id)}
                            >
                                {item.name}
                                <Icon type='iconbtn_tag_close' />
                            </span>
                        ))}
                    </div>
                </div>
                <div className='channel-item'>
                    <div className='channel-item-header'>
                        <span className='channel-item-title'>频道推荐</span>
                        <span className='channel-item-title-extra'>点击添加频道</span>
                    </div>
                    <div className='channel-list'>
                        {recommendChannels.map((item) => (
                            <span key={item.id} className='channel-list-item'>
                                + {item.name}
                            </span>
                        ))}
                    </div>
                </div>
            </div>
        </div>
    )
}

export default Channels
```

## 频道管理-删除频道

### 目标

能够删除我的频道数据。

### 分析

1. 推荐频道不能删除。

2. 至少要保留 4 个频道。

### 步骤

1. 修改频道项的点击事件参数为 channel 即当前频道数据。

2. 在我的频道项的点击事件中，判断当前是否为编辑状态。

3. 如果不是编辑状态，执行频道切换操作。

4. 如果是编辑状态，判断是否为推荐频道或频道数量小于等于 4。

5. 如果是，阻止删除。

6. 如果不是，分发删除频道的 action。

### 代码

`Channels/index.tsx`

```ts
<Icon type='iconbtn_tag_close' onClick={() => dispatch(delChannel(item.id))} />
```

`actions/home.ts`

```ts
export function delChannel(id: number): RootThunkAction {
    return async (dispatch, getState) => {
        const { userChannels } = getState().home
        if (hasToken()) {
            // 登录了
            await request.delete('/user/channels', {
                data: {
                    channels: [id],
                },
            })
        } else {
            // 没有登录
            // const userChannels = getChannels()
            setChannels(userChannels.filter((item) => item.id !== id))
        }
        // 更新到 Redux
        /* dispatch({
            type: 'home/saveUserChannels',
            payload: userChannels.filter((item) => item.id !== id),
        }) */
        dispatch(getUserChannel())
    }
}
```

### 优化

推荐不能有 x 号，超过 4 条才能删。

```ts
<div className='channel-list'>
    {userChannels.map((item) => (
        <span
            key={item.id}
            className={classnames('channel-list-item', {
                selected: item.id === active,
            })}
            onClick={() => onChange(item.id)}
        >
            {item.name}
            {item.id !== 0 && (
                <Icon
                    type='iconbtn_tag_close'
                    onClick={() => {
                        if (userChannels.length <= 4) return
                        // 删除的是当前的，就回到推荐吧
                        if (item.id === active) dispatch(changeActive(0))
                        dispatch(delChannel(item.id))
                    }}
                />
            )}
        </span>
    ))}
</div>
```

## 频道管理-添加频道

### 目标

能够实现添加频道功能。

### 代码

在 `store/actions/home.ts` 中，实现 Action Creator。

```ts
export const addChannel = (channel: Channel): RootThunkAction => {
    return async (dispatch, getState) => {
        const { userChannels } = getState().home
        if (hasToken()) {
            await request.patch('/user/channels', {
                channels: [
                    {
                        id: channel.id,
                        seq: userChannels.length + 1,
                    },
                ],
            })
        } else {
            setChannels([...userChannels, channel])
        }
        dispatch({
            type: 'home/saveUserChannels',
            payload: [...userChannels, channel],
        })
    }
}
```

`Channels/index.tsx`

```ts
const Channels = () => {
    const onAddChannel = (channel: Channel) => {
        dispatch(addChannel(channel))
    }
    return (
        <div className='channel-list'>
            {restChannel.map((item) => (
                <span onClick={() => onAddChannel(item)}>+ {item.name}</span>
            ))}
        </div>
    )
}
```

## 文章列表-基本结构

### 目标

能够根据模板搭建频道文章列表结构。

### 步骤

1. 将模板 ArticleList 和 ArticleItem 拷贝到 `pages/Home/components` 目录中。

2. 在 Home 组件中渲染文章列表结构。

3. 分析每个模板的作用，以及模板的结构。

### 代码

`Home/index.tsx`

```ts
import ArticleList from './components/ArticleList'

const Home = () => {
    return (
        // ...
        <Tabs.Tab>
            {/* 在每个 Tabs.TabPane 中渲染文章列表组件 */}
            <ArticleList />
        </Tabs.Tab>
    )
}
```

## 文章列表-获取文章列表数据

### 目标

能够获取到文章列表数据并且进行渲染。

### 步骤

1. 分析接口文档，定义频道的数据类型，`types/data.d.ts` 中。

2. 准备获取文章列表的 action，在 action 中发送请求。

3. 发送请求需要频道 id，父组件将频道 id 传递给 ArticleList 组件。

4. ArticleList 组件接受频道 Id 并发送请求获取频道列表数据。

### 代码

-   分析接口文档，定义频道的数据类型，`types/data.d.ts` 中：

```ts
export type Article = {
    art_id: string
    title: string
    aut_id: string
    comm_count: number
    pubdate: string
    aut_name: string
    is_top: number
    cover: {
        type: 0 | 1 | 3
        images: string[]
    }
}

export type ArticleRes = {
    pre_timestamp: string
    results: Article[]
}
```

-   准备获取文章列表的 action，`actions/home.ts` 中：

```ts
export const getArticleList = (channel_id: number, timestamp: string): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<ArticleRes>>('/articles', {
            params: {
                channel_id,
                timestamp,
            },
        })
        console.log(res)
    }
}
```

-   在 `Home/index.tsx` 父组件中将频道 id 传递给 ArticleList 组件 。

```ts
const Home = () => {
    return (
        <Tabs>
            {userChannel.map((item) => (
                <Tabs.Tab title={item.name} key={item.id}>
                    {/* 传递频道 id */}
                    <ArticleList channelId={item.id} />
                </Tabs.Tab>
            ))}
        </Tabs>
    )
}
```

-   ArticleList 组件接受频道 Id `Home/components/ArticleList.tsx`

```ts
import { getArticleList } from '@/store/actions/home'
import { useEffect } from 'react'
import { useDispatch } from 'react-redux'
import ArticleItem from '../ArticleItem'
import styles from './index.module.scss'
type Props = {
    channelId: number
}
const ArticleList = ({ channelId }: Props) => {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(getArticleList(channelId, Date.now() + ''))
    }, [dispatch, channelId])
    return (
        <div className={styles.root}>
            {[1, 2, 3].map((item, index) => (
                <div className='article-item' key={index}>
                    <ArticleItem />
                </div>
            ))}
        </div>
    )
}

export default ArticleList
```

## 文章列表-将数据存储到 Redux 中

### 目标

能够将数据存储到 Redux 中。

### 分析

问题：用什么样的数据格式存储频道文章列表数据？每个频道，都对应到一个文章列表数据

```ts
// 推荐频道
0 ==> {
    pre_timestamp: xxxx,
    articles: []
}
// html 频道
1 ==> {

}
// 开发者资讯频道
2 ==> {}
```

为了高效的存储数据，我们使用 `对象` 来存储频道文章列表数据。

```ts
// 数据格式：
channelArticles = {
    1: {
        timestamp: '1638408103353',
        articles: [],
    },
    2: {
        timestamp: '1638408103353',
        articles: [],
    },
    3: {
        timestamp: '1638408103353',
        articles: [],
    },
}
```

### 代码

-   在 `types/store.d.ts` 中，定义 action 的类型。

```ts
export type HomeAction =
    | {
          type: 'home/saveUserChannels'
          payload: Channel[]
      }
    | {
          type: 'home/saveAllChannels'
          payload: Channel[]
      }
    | {
          type: 'home/changeActive'
          payload: number
      }
    | {
          type: 'home/saveChannelArticles'
          payload: {
              channel_id: number
              timestamp: string
              articles: Article[]
          }
      }
```

-   在 actions/home.ts 中，分发 action。

```ts
export const getArticleList = (channel_id: number, timestamp: string): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<ArticleRes>>('/articles', {
            params: {
                channel_id,
                timestamp,
            },
        })
        const { pre_timestamp, results } = res.data.data
        dispatch({
            type: 'home/saveChannelArticles',
            payload: {
                channel_id,
                timestamp: pre_timestamp,
                articles: results,
            },
        })
    }
}
```

-   在 reducers/home.ts 中。

```ts
import { Article, Channel } from '@/types/data'
import { HomeAction } from '@/types/store'
type HomeType = {
    userChannels: Channel[]
    allChannels: Channel[]
    active: number
    // #1
    channelArticles: {
        [key: number]: {
            timestamp: string
            articles: Article[]
        }
    }
}
const initValue: HomeType = {
    userChannels: [],
    allChannels: [],
    active: 0,
    // #2
    channelArticles: {},
}
export default function home(state = initValue, action: HomeAction): HomeType {
    if (action.type === 'home/saveUserChannels') {
        return {
            ...state,
            userChannels: action.payload,
        }
    }
    if (action.type === 'home/saveAllChannels') {
        return {
            ...state,
            allChannels: action.payload,
        }
    }
    if (action.type === 'home/changeActive') {
        return {
            ...state,
            active: action.payload,
        }
    }
    // #3
    if (action.type === 'home/saveChannelArticles') {
        const { channel_id, timestamp, articles } = action.payload
        return {
            ...state,
            channelArticles: {
                ...state.channelArticles,
                [channel_id]: {
                    timestamp,
                    articles,
                },
            },
        }
    }
    return state
}
```

## 文章列表-渲染文章列表

### 目标

根据文章列表数据渲染文章。

### 步骤

-   在 ArticleList 中获取文章列表数据。

```ts
const ArticleList = ({ channelId }: Props) => {
    const dispatch = useDispatch()
    const { channelArticles } = useSelector((state: RootState) => state.home)
    const { articles = [] } = channelArticles[channelId] || {}
    useEffect(() => {
        dispatch(getArticleList(channelId, Date.now() + ''))
    }, [dispatch, channelId])
    return (
        <div className={styles.root}>
            {articles.map((item, index) => (
                <div key={item.art_id} className='article-item'>
                    <ArticleItem article={item} />
                </div>
            ))}
        </div>
    )
}
```

-   传递给 ArticleItem 组件进行渲染

```ts
import classnames from 'classnames'
import { useSelector } from 'react-redux'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { Article } from '@/types/data'
import { RootState } from '@/types/store'

type Props = {
    article: Article
}

const ArticleItem = ({ article }: Props) => {
    const token = useSelector((state: RootState) => state.login.token)
    const {
        title,
        cover: { type, images },
        aut_name,
        comm_count,
        pubdate,
    } = article
    return (
        <div className={styles.root}>
            <div className={classnames('article-content', type === 3 && 't3', type === 0 && 'none-mt')}>
                <h3>{title}</h3>
                {type !== 0 && (
                    <div className='article-imgs'>
                        {images.map((image, index) => (
                            <div key={index} className='article-img-wrapper'>
                                <img src={image} alt='' />
                            </div>
                        ))}
                    </div>
                )}
            </div>
            <div className={classnames('article-info', type === 0 && 'none-mt')}>
                <span>{aut_name}</span>
                <span>{comm_count} 评论</span>
                <span>{pubdate} 天前</span>
                <span className='close'>{token && <Icon type='iconbtn_essay_close' />}</span>
            </div>
        </div>
    )
}

export default ArticleItem
```

-   处理时间

`src/index.tsx`

```ts
import dayjs from 'dayjs'
import relativeTime from 'dayjs/plugin/relativeTime'
import 'dayjs/locale/zh-cn'
dayjs.extend(relativeTime)
dayjs.locale('zh-cn')
```

`src/pages/Home/components/ArticleItem/index.tsx`

```ts
import dayjs from 'dayjs'
;<span>{dayjs(pubdate).fromNow()} 天前</span>
```

## 文章列表-触底加载更多

### 目标

能够使用 antd-mobile 的 InfiniteScroll 组件。

### 代码

-   使用 InfiniteScroll 组件

```ts
import { getArticleList } from '@/store/actions/home'
import { RootState } from '@/types/store'
import { useDispatch, useSelector } from 'react-redux'
import { InfiniteScroll } from 'antd-mobile'
import ArticleItem from '../ArticleItem'
import styles from './index.module.scss'
type Props = {
    channelId: number
}
const ArticleList = ({ channelId }: Props) => {
    const dispatch = useDispatch()
    const { channelArticles } = useSelector((state: RootState) => state.home)
    const { articles = [] } = channelArticles[channelId] || {}
    // #4 可以注释掉了
    /* useEffect(() => {
        dispatch(getArticleList(channelId, Date.now() + ''))
    }, [dispatch, channelId]) */
    // #2
    const hasMore = true
    // #3
    const loadMore = async () => {
        await dispatch(getArticleList(channelId, Date.now() + ''))
    }
    return (
        <div className={styles.root}>
            {articles.map((item, index) => (
                <div key={item.art_id} className='article-item'>
                    <ArticleItem article={item} />
                </div>
            ))}
            {/* #1 */}
            <InfiniteScroll loadMore={loadMore} hasMore={hasMore} />
        </div>
    )
}

export default ArticleList
```

-   修改 reducers/home.ts，将数据改成追加

```ts
if (action.type === 'home/saveChannelArticles') {
    const { channel_id, timestamp, articles } = action.payload
    const old = state.channelArticles[channel_id]?.articles || []
    return {
        ...state,
        channelArticles: {
            ...state.channelArticles,
            [channel_id]: {
                timestamp,
                articles: [...old, ...articles],
            },
        },
    }
}
```

-   时间戳和 hasMore 的处理。

```ts
const ArticleList = ({ channelId }: Props) => {
    const dispatch = useDispatch()
    const { channelArticles } = useSelector((state: RootState) => state.home)
    // #1
    const { articles = [], timestamp } = channelArticles[channelId] || {}
    // #3
    const hasMore = timestamp !== null
    const loadMore = async () => {
        // #2
        await dispatch(getArticleList(channelId, timestamp || Date.now() + ''))
    }
}
```

## 文章列表-下拉刷新

### 目标

能够下拉刷新文章数据。

### 分析

`Home/components/ArticleList/index.tsx`

```ts
const onRefresh = async () => {
    await dispatch(getNewList(channelId, Date.now() + ''))
}
return (
    <div className={styles.root}>
        <PullToRefresh onRefresh={onRefresh}>
            {articles.map((item, index) => (
                <div key={item.art_id} className='article-item'>
                    <ArticleItem article={item} />
                </div>
            ))}
            <InfiniteScroll loadMore={loadMore} hasMore={hasMore} />
        </PullToRefresh>
    </div>
)
```

`store.d.ts`

```ts
export type HomeAction =
    | {
          type: 'home/saveUserChannels'
          payload: Channel[]
      }
    | {
          type: 'home/saveAllChannels'
          payload: Channel[]
      }
    | {
          type: 'home/changeActive'
          payload: number
      }
    | {
          type: 'home/saveChannelArticles'
          payload: {
              channel_id: number
              timestamp: string
              articles: Article[]
          }
      }
    | {
          type: 'home/saveNewArticles'
          payload: {
              channel_id: number
              timestamp: string
              articles: Article[]
          }
      }
```

`actions/home.ts`

```ts
export const getNewList = (channel_id: number, timestamp: string): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<ArticleRes>>('/articles', {
            params: {
                channel_id,
                timestamp,
            },
        })
        const { pre_timestamp, results } = res.data.data
        dispatch({
            type: 'home/saveNewArticles',
            payload: {
                channel_id,
                timestamp: pre_timestamp,
                articles: results,
            },
        })
    }
}
```

`reducers/home.ts`

```ts
if (action.type === 'home/saveNewArticles') {
    const { channel_id, timestamp, articles } = action.payload
    return {
        ...state,
        channelArticles: {
            ...state.channelArticles,
            [channel_id]: {
                timestamp,
                articles: [...articles],
            },
        },
    }
}
```

## IntersectionObserver

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
    </head>

    <body>
        <div style="height: 2000px;"></div>
        <img data-src="http://geek.itheima.net/resources/images/19.jpg" alt="" />
        <script>
            const oImg = document.querySelector('img')
            const observer = new IntersectionObserver(function ([{ isIntersecting }]) {
                if (isIntersecting) {
                    oImg.src = oImg.getAttribute('data-src')
                    observer.unobserve()
                }
            })
            observer.observe(oImg)
        </script>
    </body>
</html>
```

## 文章列表-图片懒加载的封装

### 目标

封装一个拥有懒加载功能的图片组件，实现对文章列表项上的封面图片进行懒加载。

当前问题：文章列表一旦开始渲染，上面所有的封面图都会一次性加载，可能会浪费网络资源和降低用户体验。

解决方案：只有当用户滚动页面，真正浏览到这些列表项，才开始加载封面图片。

实现思路：利用浏览器提供的 `IntersectionObserver`，监听图片元素是否进入可视区域，进入后才真正去设置图片元素的 `src` 属性进行图片加载。

### 步骤

1. 基本封装。

```ts
import classnames from 'classnames'
import { useRef, useEffect } from 'react'
import styles from './index.module.scss'

type Props = {
    src: string
    className?: string
    alt?: string
}
const Img = ({ src, className }: Props) => {
    const imgRef = useRef<HTMLImageElement>(null)
    useEffect(() => {
        const img = imgRef.current!
        const observer = new IntersectionObserver(([{ isIntersecting }]) => {
            if (isIntersecting) {
                img.src = img.getAttribute('data-src')!
                observer.unobserve(img)
            }
        })
        observer.observe(img)
        return () => {
            observer.unobserve(img)
        }
    }, [])
    return (
        <div className={classnames(styles.root, className)}>
            {/* //!加载成功 */}
            <img alt='' data-src={src} ref={imgRef} />
        </div>
    )
}

export default Img
```

2. `pages/Question/index.tsx` 中基本使用。

```ts
import { NavBar } from 'antd-mobile'
import styles from './index.module.scss'
import Img from '@/components/Img'

const Question = () => {
    return (
        <div className={styles.root}>
            <NavBar>问答</NavBar>
            <div style={{ height: 2000 }}></div>
            <Img src='http://geek.itheima.net/resources/images/19.jpg' />
        </div>
    )
}

export default Question
```

3. 处理加载状态。

```ts
import classnames from 'classnames'
import { useRef, useEffect, useState } from 'react'
import Icon from '../Icon'
import styles from './index.module.scss'

type Props = {
    src: string
    className?: string
    alt?: string
}
const Img = ({ src, className }: Props) => {
    const [error, setError] = useState(false)
    const [loading, setLoading] = useState(true)
    const imgRef = useRef<HTMLImageElement>(null)
    // #2
    const onError = () => setError(true)
    const onLoad = () => setLoading(false)
    useEffect(() => {
        const img = imgRef.current!
        const observer = new IntersectionObserver(([{ isIntersecting }]) => {
            if (isIntersecting) {
                img.src = img.getAttribute('data-src')!
                observer.unobserve(img)
            }
        })
        observer.observe(img)
        return () => {
            observer.unobserve(img)
        }
    }, [])
    return (
        <div className={classnames(styles.root, className)}>
            {/* //!加载中... */}
            {loading && (
                <div className='image-icon'>
                    <Icon type='iconphoto' />
                </div>
            )}
            {/* //!加载失败 */}
            {/* #3 */}
            {error && (
                <div className='image-icon'>
                    <Icon type='iconphoto-fail' />
                </div>
            )}
            {/* #1 */}
            <img alt='' data-src={src} ref={imgRef} onError={onError} onLoad={onLoad} />
        </div>
    )
}

export default Img
```

4. `pages/Home/components/ArticleItem/index.tsx` 中使用懒加载组件。

```ts
<div className='article-imgs'>
    {images.map((image, index) => (
        <div key={index} className='article-img-wrapper'>
            <Img src={image} />
        </div>
    ))}
</div>
```

## 文章列表-点击文章项跳转到详情

### 目标

能够在点击文章项时跳转到文章详情页面。

### 步骤

1. 将文章详情页面模板拷贝到 pages 目录中。

2. 在 App 组件中配置文章详情页路由。

3. 为每个文章列表项绑定点击事件。

4. 点击时，根据文章 id，跳转到文章详情页对应的路由。

### 代码

`pages/Home/component/ArticleList/index.tsx`

```ts
<div key={item.art_id} className='article-item' onClick={() => history.push(`/article/${item.art_id}`)}>
    <ArticleItem article={item} />
</div>
```

`App.tsx`

```ts
import Article from './pages/Article'
;<Route path='/article/:id' component={Article} />
```
