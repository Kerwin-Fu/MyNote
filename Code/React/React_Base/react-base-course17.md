---
title: 17_极客园 H5
date: 2021-12-24 16:30:14
tags:
---

## 今日目标

✔ 掌握个人中心功能的开发。

<!-- more -->

## 布局页面结构

### 目标

能够使用准备好的模板搭建布局页面结构。

### 步骤

1. 使用 antd-mobile 的 TabBar 组件创建底部标签栏。

2. 样式在素材中已经准备好。

### 代码

`Layout/index.tsx`

```ts
import styles from './index.module.scss'
import { TabBar } from 'antd-mobile'
import Icon from '@/components/Icon'

const tabs = [
    { path: '/home', icon: 'iconbtn_home', text: '首页' },
    { path: '/home/question', icon: 'iconbtn_qa', text: '问答' },
    { path: '/home/video', icon: 'iconbtn_video', text: '视频' },
    { path: '/home/profile', icon: 'iconbtn_mine', text: '我的' },
]

export default function Layout() {
    return (
        <div className={styles.root}>
            <TabBar className='tab-bar'>
                {tabs.map((item) => (
                    <TabBar.Item key={item.path} icon={<Icon type={item.icon} />} title={item.text} />
                ))}
            </TabBar>
        </div>
    )
}
```

`pages/Layout/index.module.scss`

```scss
@import '@scss/hairline.scss';

.root {
    height: 100%;

    :global {
        // 底部 tabbar 的样式
        .tab-bar {
            @include hairline(top, #f0f0f0);
            position: fixed;
            z-index: 1;
            bottom: 0;
            width: 100%;
            height: 46px;
            background-color: #f7f8fa;

            .icon {
                color: #fc6627;
            }
        }
    }
}
```

## 路由切换功能

1. 注册 onChange 事件，实现路由的跳转。

```ts
export default function Layout() {
    const history = useHistory()
    // #2
    const changeRoute = (path: string) => {
        history.push(path)
    }
    return (
        <div className={styles.root}>
            {/* #1 */}
            <TabBar className='tab-bar' onChange={changeRoute}>
                {tabs.map((item) => (
                    <TabBar.Item key={item.path} icon={<Icon type={item.icon} />} title={item.text} />
                ))}
            </TabBar>
        </div>
    )
}
```

2. 点击高亮效果。

```ts
<TabBar.Item key={item.path} icon={(active: boolean) => <Icon type={active ? `${item.icon}_sel` : item.icon} />} title={item.text} />
```

3. 解决刷新时默认高亮的问题。

```ts
<TabBar className='tab-bar' onChange={changeRoute} activeKey={location.pathname}></TabBar>
```

4. 完整代码。

```ts
import styles from './index.module.scss'
import { TabBar } from 'antd-mobile'
import Icon from '@/components/Icon'
import { useHistory, useLocation } from 'react-router-dom'

const tabs = [
    { path: '/home', icon: 'iconbtn_home', text: '首页' },
    { path: '/home/question', icon: 'iconbtn_qa', text: '问答' },
    { path: '/home/video', icon: 'iconbtn_video', text: '视频' },
    { path: '/home/profile', icon: 'iconbtn_mine', text: '我的' },
]

export default function Layout() {
    const history = useHistory()
    const location = useLocation()
    const changeRoute = (path: string) => {
        history.push(path)
    }
    return (
        <div className={styles.root}>
            <TabBar className='tab-bar' onChange={changeRoute} activeKey={location.pathname}>
                {tabs.map((item) => (
                    <TabBar.Item key={item.path} icon={(active: boolean) => <Icon type={active ? `${item.icon}_sel` : item.icon} />} title={item.text} />
                ))}
            </TabBar>
        </div>
    )
}
```

## 配置二级路由

### 目标

能够点击 Tab 栏实现二级路由切换的效果。

### 步骤

1. 粘贴素材中【布局页面模板】文件夹中的 Home、Question、Video、Profile 到项目的 pages 目录。

2. `pages/Layout/index.tsx` 文件中配置路由。

```ts
import styles from './index.module.scss'
import { TabBar } from 'antd-mobile'
import Icon from '@/components/Icon'
// !#1
import { useHistory, useLocation, Switch, Route } from 'react-router-dom'
import Home from '@/pages/Home'
import Question from '@/pages/Question'
import Video from '@/pages/Video'
import Profile from '@/pages/Profile'

const tabs = [
    { path: '/home', icon: 'iconbtn_home', text: '首页' },
    { path: '/home/question', icon: 'iconbtn_qa', text: '问答' },
    { path: '/home/video', icon: 'iconbtn_video', text: '视频' },
    { path: '/home/profile', icon: 'iconbtn_mine', text: '我的' },
]

export default function Layout() {
    const history = useHistory()
    const location = useLocation()
    const changeRoute = (path: string) => {
        history.push(path)
    }
    return (
        <div className={styles.root}>
            {/* //!#2 */}
            <Switch>
                <Route path='/home' exact component={Home} />
                <Route path='/home/question' exact component={Question} />
                <Route path='/home/video' exact component={Video} />
                <Route path='/home/profile' exact component={Profile} />
            </Switch>
            <TabBar className='tab-bar' onChange={changeRoute} activeKey={location.pathname}>
                {tabs.map((item) => (
                    <TabBar.Item key={item.path} icon={(active: boolean) => <Icon type={active ? `${item.icon}_sel` : item.icon} />} title={item.text} />
                ))}
            </TabBar>
        </div>
    )
}
```

## 个人中心-结构与样式

### 目标

能够根据模板搭建个人中心页面结构。

### 步骤

粘贴【个人中心模板】Profile 文件夹替换项目的 pages/Profile 文件夹。

`pages/Profile/index.tsx`

```ts
import { Link, useHistory } from 'react-router-dom'

import Icon from '@/components/Icon'
import styles from './index.module.scss'

const Profile = () => {
    const history = useHistory()

    return (
        <div className={styles.root}>
            <div className='profile'>
                {/* 个人信息 */}
                <div className='user-info'>
                    <div className='avatar'>
                        <img src={'http://toutiao.itheima.net/images/user_head.jpg'} alt='' />
                    </div>
                    <div className='user-name'>黑马先锋</div>
                    <Link to='/profile/edit'>
                        个人信息 <Icon type='iconbtn_right' />
                    </Link>
                </div>

                {/* 今日阅读 */}
                <div className='read-info'>
                    <Icon type='iconbtn_readingtime' />
                    今日阅读
                    <span>10</span>
                    分钟
                </div>

                {/* 动态 - 对应的这一行 */}
                <div className='count-list'>
                    <div className='count-item'>
                        <p>1</p>
                        <p>动态</p>
                    </div>
                    <div className='count-item'>
                        <p>9</p>
                        <p>关注</p>
                    </div>
                    <div className='count-item'>
                        <p>99</p>
                        <p>粉丝</p>
                    </div>
                    <div className='count-item'>
                        <p>200</p>
                        <p>被赞</p>
                    </div>
                </div>

                {/* 消息通知 - 对应的这一行 */}
                <div className='user-links'>
                    <div className='link-item'>
                        <Icon type='iconbtn_mymessages' />
                        <div>消息通知</div>
                    </div>
                    <div className='link-item'>
                        <Icon type='iconbtn_mycollect' />
                        <div>收藏</div>
                    </div>
                    <div className='link-item'>
                        <Icon type='iconbtn_history1' />
                        <div>浏览历史</div>
                    </div>
                    <div className='link-item'>
                        <Icon type='iconbtn_myworks' />
                        <div>我的作品</div>
                    </div>
                </div>
            </div>
            {/* 更多服务 */}
            <div className='more-service'>
                <h3>更多服务</h3>
                <div className='service-list'>
                    <div className='service-item'>
                        <Icon type='iconbtn_feedback' />
                        <div>用户反馈</div>
                    </div>
                    <div className='service-item' onClick={() => history.push('/chat')}>
                        <Icon type='iconbtn_xiaozhitongxue' />
                        <div>小智同学</div>
                    </div>
                </div>
            </div>
        </div>
    )
}

export default Profile
```

`pages/Profile/index.module.scss`

```scss
.root {
    height: 100%;
    background-color: #f8f8f8;

    :global {
        .profile {
            height: 299px;
            padding: 0 15px;
            border-bottom-left-radius: 500px 120px;
            border-bottom-right-radius: 500px 120px;
            background: linear-gradient(318deg, #b2b5db 2%, #565482 85%, #494675 97%);
        }

        .user-info {
            display: flex;
            align-items: center;
            padding: 40px 0 15px;

            .user-name {
                flex: 1;
                margin-left: 18px;
                font-size: 24px;
                font-weight: 400;
                color: #fff;
            }

            a {
                line-height: 16px;
                color: #fff;
                font-size: 12px;
                text-decoration: none;

                &:visited {
                    color: #fff;
                }
            }

            .icon {
                width: 9px;
                height: 16px;
                vertical-align: middle;
            }
        }

        .avatar {
            width: 50px;
            height: 50px;
            border: 3px solid rgba(255, 255, 255, 0.16);
            border-radius: 50%;
            overflow: hidden;

            img {
                width: 100%;
                height: 100%;
                object-fit: cover;
            }
        }

        .read-info {
            display: flex;
            align-items: center;
            width: fit-content;
            height: 30px;
            padding: 5px 8px 5px 5px;
            border-radius: 15px;
            font-size: 12px;
            color: #f7f8fa;
            background: linear-gradient(319deg, #585089 0%, #2c2a6b 98%);

            .icon {
                margin-right: 5px;
                font-size: 20px;
            }

            span {
                display: inline-block;
                margin: 0 3px;
                font-size: 18px;
                font-weight: bold;
                color: #ffbc3d;
            }
        }

        .count-list {
            display: flex;
            justify-content: space-between;
            padding: 20px 0 15px 0;

            .count-item {
                flex: 1;
                text-align: center;
                font-size: 13px;
                color: #fff;

                p {
                    &:first-child {
                        height: 14px;
                        margin-bottom: 6px;
                    }
                }
            }
        }

        .user-links {
            display: flex;

            padding: 20px 0;
            font-size: 12px;
            text-align: center;
            background-color: #fff;
            border-radius: 10px;
            color: #333;

            .link-item {
                flex: 1;
            }

            .icon {
                margin-bottom: 11px;
                font-size: 23px;
            }
        }

        .more-service {
            // height: 120px;
            margin: 11px 16px 0 16px;
            border-radius: 10px;
            background-color: #fff;

            h3 {
                padding: 15px;
                font-size: 17px;
                color: #333;
            }

            .service-list {
                display: flex;
            }

            .service-item {
                width: 25%;
                padding-bottom: 20px;
                text-align: center;
                font-size: 13px;
            }

            .icon {
                margin-bottom: 11px;
                font-size: 22px;
            }
        }
    }
}
```

## 个人中心-获取用户信息

### 目标

完成获取用户信息的功能。

### 分析

在使用 TS 开发与接口相关功能时，推荐按照以下步骤。

1. 先按照接口的返回数据，准备 TS 类型。

2. 然后，在发送请求时，指定该请求的返回值类型。

3. 在接下来的操作中，如果需要用到接口的数据，都会有类型提示了。如果将来后端接口返回的数据变了，只需要修改对应的 TS 类型，然后，所有用到该类型的地方如果有问题，都会自动提示出来，方便项目代码的重构和修改。

`types/data.d.ts`

```ts
export type User = {
    id: string
    name: string
    photo: string
    art_count: number
    follow_count: number
    fans_count: number
    like_count: number
}
```

### 步骤

1. 定义 action。

`actions/profile.ts`

```ts
import { ApiResponse, User } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export function getUser(): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<User>>('/user')
        console.log(res)
    }
}
```

2. 触发 action。

`pages/Profile/index.tsx`

```ts
import { useEffect } from 'react'
import { Link, useHistory } from 'react-router-dom'
import { useDispatch } from 'react-redux'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
// !#1
import { getUser } from '@/store/actions/profile'

const Profile = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    // !#2
    useEffect(() => {
        dispatch(getUser())
    }, [dispatch])
}

export default Profile
```

3. 配置请求拦截器统一携带 Token。

`utils/request.ts`

```ts
request.interceptors.request.use(
    function (config) {
        const token = getToken().token
        if (token) {
            config.headers!.Authorization = `Bearer ${token}`
        }
        return config
    },
    function (error) {
        return Promise.reject(error)
    }
)
```

## 个人中心-存储用户信息到 Redux

### 目标

能够将用户信息保存到 Redux 中。

### 步骤

4. 在 reducers/index.ts 中，将 profile 合并到根 reducer 中。

### 代码

1. 准备保存状态到 Redux 的 action 类型。

`types/store.d.ts`

```ts
import store from '@/store'
import { ThunkAction } from 'redux-thunk'
import { Token } from './data'

// 各个默认的 action
export type LoginAction = {
    type: 'login/login'
    payload: Token
}
// #1
export type ProfileAction = {
    type: 'profile/getUser'
    payload: User
}

// store 的 state 的类型
export type RootState = ReturnType<typeof store.getState>
// 所有的 action 的类型
// #2
export type RootAction = LoginAction | ProfileAction
// thunkAction 类型
export type RootThunkAction = ThunkAction<void, RootState, unknown, RootAction>
```

2. 在获取个人信息的 action 中将用户信息存储到 Redux 中。

`actions/profile.ts`

```ts
import { ApiResponse, User } from '@/types/data'
import { RootThunkAction } from '@/types/store'
import request from '@/utils/request'

export function getUser(): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<User>>('/user')
        const { data } = res.data
        dispatch({
            type: 'profile/getUser',
            payload: data,
        })
    }
}
```

3. 创建 `reducers/profile.ts`，并完成存储用户信息的功能。

```ts
import { User } from '@/types/data'
import { ProfileAction } from '@/types/store'
type ProfileState = { user: User }
const initState = { user: {} } as ProfileState // 建议再加一个 `user: {}`，防止后面报错
// const initState: ProfileState = { user: {} } as ProfileState // 建议再加一个 `user: {}`，防止后面报错
// const initState: ProfileState = { user: {} as User } // 也 ok

const profile = (state = initState, action: ProfileAction): ProfileState => {
    switch (action.type) {
        case 'profile/getUser':
            return {
                ...state,
                user: action.payload,
            }
        default:
            return state
    }
}
export default profile
```

4. 合并 profileReducer 到 rootReducer。

`reducers/index.ts`

```ts
import { combineReducers } from 'redux'
import login from './login'
import profile from './profile'
export default combineReducers({ login, profile })
```

## 个人中心-渲染用户信息

### 目标

能够展示用户信息到界面。

### 步骤

1. 导入 useSelector。

2. 调用 useSelector 获取 user 状态。

3. 从 user 对象中解构出用户数据并展示在页面中。

### 代码

```ts
import { useEffect } from 'react'
import { Link, useHistory } from 'react-router-dom'
import { useDispatch, useSelector } from 'react-redux'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { getUser } from '@/store/actions/profile'
import { RootState } from '@/types/store'
const Profile = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const user = useSelector((state: RootState) => state.profile.user)
    useEffect(() => {
        dispatch(getUser())
    }, [dispatch])
    return (
        <div className={styles.root}>
            <div className='profile'>
                {/* 个人信息 */}
                <div className='user-info'>
                    <div className='avatar'>
                        <img src={user.photo} alt='' />
                    </div>
                    <div className='user-name'>{user.name}</div>
                    <Link to='/profile/edit'>
                        个人信息 <Icon type='iconbtn_right' />
                    </Link>
                </div>

                {/* 今日阅读 */}
                <div className='read-info'>
                    <Icon type='iconbtn_readingtime' />
                    今日阅读
                    <span>10</span>
                    分钟
                </div>

                {/* 动态 - 对应的这一行 */}
                <div className='count-list'>
                    <div className='count-item'>
                        <p>{user.art_count}</p>
                        <p>动态</p>
                    </div>
                    <div className='count-item'>
                        <p>{user.like_count}</p>
                        <p>关注</p>
                    </div>
                    <div className='count-item'>
                        <p>{user.fans_count}</p>
                        <p>粉丝</p>
                    </div>
                    <div className='count-item'>
                        <p>{user.follow_count}</p>
                        <p>被赞</p>
                    </div>
                </div>

                {/* 消息通知 - 对应的这一行 */}
                <div className='user-links'>
                    <div className='link-item'>
                        <Icon type='iconbtn_mymessages' />
                        <div>消息通知</div>
                    </div>
                    <div className='link-item'>
                        <Icon type='iconbtn_mycollect' />
                        <div>收藏</div>
                    </div>
                    <div className='link-item'>
                        <Icon type='iconbtn_history1' />
                        <div>浏览历史</div>
                    </div>
                    <div className='link-item'>
                        <Icon type='iconbtn_myworks' />
                        <div>我的作品</div>
                    </div>
                </div>
            </div>
            {/* 更多服务 */}
            <div className='more-service'>
                <h3>更多服务</h3>
                <div className='service-list'>
                    <div className='service-item'>
                        <Icon type='iconbtn_feedback' />
                        <div>用户反馈</div>
                    </div>
                    <div className='service-item' onClick={() => history.push('/chat')}>
                        <Icon type='iconbtn_xiaozhitongxue' />
                        <div>小智同学</div>
                    </div>
                </div>
            </div>
        </div>
    )
}

export default Profile
```

## 个人信息-页面结构

### 目标

能够根据模板展示个人信息页面。

### 步骤

1. 将【个人中心模板】中的 Edit 文件夹拷贝到 Profile 项目的目录中。

`pages/Profile/Edit/index.tsx`

```ts
import { Button, List, DatePicker, NavBar } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import classNames from 'classnames'
import styles from './index.module.scss'
const Item = List.Item

const ProfileEdit = () => {
    const history = useHistory()
    return (
        <div className={styles.root}>
            <div className='content'>
                {/* 标题 */}
                <NavBar
                    style={{
                        '--border-bottom': '1px solid #F0F0F0',
                    }}
                    onBack={() => history.go(-1)}
                >
                    个人信息
                </NavBar>

                <div className='wrapper'>
                    {/* 列表 */}
                    <List className='profile-list'>
                        {/* 列表项 */}
                        <Item
                            extra={
                                <span className='avatar-wrapper'>
                                    <img width={24} height={24} src={'http://toutiao.itheima.net/images/user_head.jpg'} alt='' />
                                </span>
                            }
                            arrow
                        >
                            头像
                        </Item>
                        <Item arrow extra={'黑马先锋'}>
                            昵称
                        </Item>
                        <Item arrow extra={<span className={classNames('intro', 'normal')}>{'未填写'}</span>}>
                            简介
                        </Item>
                    </List>

                    <List className='profile-list'>
                        <Item arrow extra={'男'}>
                            性别
                        </Item>
                        <Item arrow extra={'1999-9-9'}>
                            生日
                        </Item>
                    </List>

                    <DatePicker visible={false} value={new Date()} title='选择年月日' min={new Date(1900, 0, 1, 0, 0, 0)} max={new Date()} />
                </div>

                <div className='logout'>
                    <Button className='btn'>退出登录</Button>
                </div>
            </div>
        </div>
    )
}

export default ProfileEdit
```

`pages/Profile/Edit/index.module.scss`

```scss
.root {
    height: 100%;

    :global {
        .content {
            position: relative;
            z-index: 1;
            height: 100%;
        }

        .wrapper {
            background-color: #f7f8fa;
        }

        .profile-list {
            font-size: 14px;

            .adm-list-item {
                padding-left: 17px;
            }

            .adm-list-item-content-extra {
                padding-right: 13px;
                color: #3a3948;

                .avatar-wrapper {
                    display: inline-block;
                    border-radius: 50%;
                    overflow: hidden;
                    vertical-align: middle;

                    img {
                        display: block;
                        object-fit: cover;
                    }
                }

                .intro {
                    color: #c8c9cc;

                    &.normal {
                        color: #3a3948;
                    }
                }
            }

            .adm-list-item-content-arrow {
                padding-left: 0;
            }

            &:nth-child(2) {
                margin-top: 12px;
            }
        }

        .logout {
            position: absolute;
            bottom: 60px;
            width: 100%;
            height: 44px;
            padding: 0 16px;

            .btn {
                width: 100%;
                height: 100%;
                border-radius: 4px;
                color: #fff;
                font-size: 16px;
                background: linear-gradient(315deg, #fe4f4f, #fc6627);
            }
        }
    }
}

// popup 不在当前组件结构中，因此，只能设置全局样式
:global {
    .adm-picker-popup-body {
        .adm-picker-popup-header {
            background-size: 0;
            &::after {
                display: none !important;
            }
        }
    }
    .adm-picker-header {
        .adm-picker-header-button {
            font-size: 14px;
            color: #969799;
        }
        .adm-picker-header-button:nth-last-child(1) {
            color: #fc6627;
        }
    }
}
```

2. 在 `App.tsx` 中配置个人信息页面的路由。

```tsx
<Route path='/profile/edit' component={ProfileEdit} />
```

3. `App.scss` 中调整下字体大小。

```scss
@import '@scss/hairline.scss';
.app {
    height: 100%;
    .adm-list-default {
        border: none;
        font-size: 16px;
    }
    .adm-nav-bar-title {
        font-size: 17px;
    }
    .adm-nav-bar-back-arrow {
        font-size: 17px;
    }
}
```

## 个人信息-获取并渲染

### 目标

能够获取并展示编辑时的个人信息。

### 步骤

1. 在 `types/data.d.ts` 中，根据接口准备好返回数据类型。

```ts
export type UserProfile = {
    id: string
    photo: string
    name: string
    mobile: string
    gender: number
    birthday: string
    intro: string
}
```

2. 在 `types/store.d.ts` 中创建相应的 Redux action 类型。

```ts
import store from '@/store'
import { ThunkAction } from 'redux-thunk'
import { Token } from './data'

// 各个默认的 action
export type LoginAction = {
    type: 'login/login'
    payload: Token
}
// #mark
export type ProfileAction =
    | {
          type: 'profile/getUser'
          payload: User
      }
    | {
          type: 'profile/getUserProfile'
          payload: UserProfile
      }

// store 的 state 的类型
export type RootState = ReturnType<typeof store.getState>
// 所有的 action 的类型
export type RootAction = LoginAction | ProfileAction
// thunkAction 类型
export type RootThunkAction = ThunkAction<void, RootState, unknown, RootAction>
```

3. 在 `actions/profile.ts` 中，创建获取编辑时的个人信息的 action 并进行 dispatch。

```ts
export const getUserProfile = (): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<UserProfile>>('/user/profile')
        dispatch({
            type: 'profile/getUserProfile',
            payload: res.data.data,
        })
    }
}
```

4. 在 reducers 中处理该 action，并将状态存储到 Redux 中。

`reducers/profile.ts`

```ts
// #1
import { User, UserProfile } from '@/types/data'
import { ProfileAction } from '@/types/store'
// #2
type ProfileState = { user: User; userProfile: UserProfile }
// #3
const initState = { user: {}, userProfile: {} } as ProfileState

const profile = (state = initState, action: ProfileAction): ProfileState => {
    switch (action.type) {
        case 'profile/getUser':
            return {
                ...state,
                user: action.payload,
            }
        // #4
        case 'profile/getUserProfile':
            return {
                ...state,
                userProfile: action.payload,
            }
        default:
            return state
    }
}
export default profile
```

5. 在 `Profile/Edit/index.tsx` 获取信息并渲染。

```ts
import { useEffect } from 'react'
import { Button, List, DatePicker, NavBar } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import { useDispatch, useSelector } from 'react-redux'
import classNames from 'classnames'
import styles from './index.module.scss'
import { getUserProfile } from '@/store/actions/profile'
import { RootState } from '@/types/store'
const Item = List.Item

const ProfileEdit = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const { userProfile } = useSelector((state: RootState) => state.profile)
    useEffect(() => {
        dispatch(getUserProfile())
    }, [dispatch])
    return (
        <div className={styles.root}>
            <div className='content'>
                {/* 标题 */}
                <NavBar
                    style={{
                        '--border-bottom': '1px solid #F0F0F0',
                    }}
                    onBack={() => history.go(-1)}
                >
                    个人信息
                </NavBar>

                <div className='wrapper'>
                    {/* 列表 */}
                    <List className='profile-list'>
                        {/* 列表项 */}
                        <Item
                            extra={
                                <span className='avatar-wrapper'>
                                    <img width={24} height={24} src={userProfile.photo} alt='' />
                                </span>
                            }
                            arrow
                        >
                            头像
                        </Item>
                        <span arrow extra={userProfile.name}>
                            昵称
                        </Item>
                        <Item arrow extra={<span className={classNames('intro', userProfile.intro && 'normal')}>{userProfile.intro || '未填写'}</span>}>
                            简介
                        </Item>
                    </List>

                    <List className='profile-list'>
                        <Item arrow extra={userProfile.gender === 0 ? '男' : '女'}>
                            性别
                        </Item>
                        <Item arrow extra={userProfile.birthday}>
                            生日
                        </Item>
                    </List>

                    <DatePicker visible={false} value={new Date()} title='选择年月日' min={new Date(1900, 0, 1, 0, 0, 0)} max={new Date()} />
                </div>

                <div className='logout'>
                    <Button className='btn'>退出登录</Button>
                </div>
            </div>
        </div>
    )
}

export default ProfileEdit
```

## 自定义 Hooks 基本语法

### 目标

能够知道什么是自定义 Hooks。

### 分析

-   除了使用 React 提供的 Hooks 之外，开发者还可以创建自己的 Hooks，也就是自定义 Hooks。

-   问题：为什么要创建自定义 Hooks？

-   回答：实现状态逻辑复用，也就是将与状态相关的逻辑代码封装到一个函数中，哪个地方用到了，哪个地方调用即可。

-   自定义 Hooks 的特点如下。

    a，名称必须以 `use` 开头。

    b，和内置的 React Hooks 一样，自定义 Hooks 也是一个函数。

```ts
// 创建自定义 Hooks 函数
const useXxx = (params) => {
    // 需要复用的状态逻辑代码
    return xxx
}
```

```ts
// 使用自定义 Hooks 函数
const Hello = () => {
  const xxx = useXxx(...)
}
```

🤔 自定义 Hooks 就是一个函数，可以完全按照对函数的理解，来理解自定义 Hooks，参数和返回值都可以可选的，可以提供也可以不提供，根据实际需求来实现即可。

### 总结

-   自定义 Hooks 是函数吗？

-   自定义 Hooks 的名称有什么约束？

-   自定义 Hooks 可以没有参数或返回值吗？

## 自定义一个 Hook

### 目标

能够通过封装自定义 Hook 实现进入页面就获取数据的功能。

### 目标

函数封装的基本思想：将相同的逻辑直接拷贝到函数中，不同的逻辑通过函数参数传入，外部需要用到的数据，就通过函数返回值返回。

-   分发的 action 函数不同，获取的状态不同。

-   所以，只需要把这两点作为自定义 Hook 的参数即可，最后把状态返回。

`utils/hooks.ts`

```ts
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
export function useInitState(action: any, stateName: any) {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(action())
    }, [dispatch, action])
    const state = useSelector((state: any) => state[stateName])
    return state
}
```

`pages/Profile/Edit.tsx`

```tsx
const ProfileEdit = () => {
    const history = useHistory()
    /* const dispatch = useDispatch()
    const { userProfile } = useSelector((state: RootState) => state.profile)
    useEffect(() => {
        dispatch(getUserProfile())
    }, [dispatch]) */
    const { userProfile } = useInitState(getUserProfile, 'profile')
}

export default ProfileEdit
```

## 为自定义 Hook 添加类型

### 目标

能够为实现的自定义 Hook 添加类型。

### 分析

对于 useInitState 这个自定义 Hook 来说，只需要为参数指定类型即可。

-   参数 action。

    就是一个函数，所以，直接指定为最简单的函数类型即可。

-   参数 stateName。

    a，stateName 表示从 Redux 状态中取出的状态名称，比如，`'profile'`。

    b，所以，stateName 应该是 RootState 中的所有状态名称中的任意一个。

    c，但是，具体是哪一个不确定，只有在使用该函数时才能确定下来。

    d，问题：如果一个类型不确定，应该是什么 TS 中的什么类型来实现？

### 代码

`utils/hooks.ts`

```tsx
import { RootState } from '@/types/store'
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
export function useInitState(action: () => void, stateName: 'login' | 'profile') {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(action())
    }, [dispatch, action])
    const state = useSelector((state: RootState) => state[stateName])
    return state
}
```

问题：`pages/Profile/Edit/index.tsx` 中会有错误。

```ts
const { userProfile } = useInitState(getUserProfile, 'profile')
```

解决如下。

```ts
import { RootState } from '@/types/store'
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
export function useInitState<T extends keyof RootState>(action: () => void, stateName: T) {
    const dispatch = useDispatch()
    useEffect(() => {
        dispatch(action())
    }, [dispatch, action])
    const state = useSelector((state: RootState) => state[stateName])
    return state
}
```

`src/pages/Profile/index.tsx` 也可以改写如下。

```ts
import { useInitState } from '@/utils/hooks'
const Profile = () => {
    const history = useHistory()
    /* const dispatch = useDispatch()
    const user = useSelector((state: RootState) => state.profile.user)
    useEffect(() => {
        dispatch(getUser())
    }, [dispatch]) */
    const { user } = useInitState(getUser, 'profile')
}
```

## 修改昵称和简介-显示弹层

1. 准备弹层。

`src/pages/Profile/Edit/index.tsx`

```ts
<Popup visible={true} position='right'>
    <div style={{ height: 400, width: '80vw' }}>xxx</div>
</Popup>
```

2. 准备状态，用于控制弹层的显示。

```ts
type InputState = {
    visible: boolean
    type: '' | 'name' | 'intro'
}
const [showInput, setShowInput] = useState<InputState>({
    type: '',
    visible: false,
})
```

3. 点击昵称和简介的时候，需要弹层并设置 type。

```ts
<Item
    arrow
    extra={userProfile.name}
    onClick={() =>
        setShowInput({
            visible: true,
            type: 'name',
        })
    }
>
    昵称
</Item>
```

## 修改昵称和简介-准备 EditInput 组件

### 目标

准备 EditInput 组件，点击返回的时候隐藏弹层。

### 准备 EditInput 组件

`src/pages/Profile/Edit/EditInput/index.tsx`

```ts
import { Input, NavBar } from 'antd-mobile'

import styles from './index.module.scss'

const EditInput = () => {
    return (
        <div className={styles.root}>
            <NavBar className='navbar' right={<span className='commit-btn'>提交</span>}>
                编辑昵称
            </NavBar>

            <div className='edit-input-content'>
                <h3>昵称</h3>

                <div className='input-wrap'>
                    <Input placeholder='请输入' />
                </div>
            </div>
        </div>
    )
}

export default EditInput
```

`src/pages/Profile/Edit/EditInput/index.module.scss`

```scss
.root {
    width: 375px;
    height: 100%;
    background-color: #f7f8fa;

    :global {
        .navbar {
            background-color: transparent;
        }

        .commit-btn {
            color: #fc6627;
            font-size: 17px;
        }

        .edit-input-content {
            padding: 0 16px;

            h3 {
                padding: 15px 0;
                font-size: 17px;
                color: #333;
            }

            .input-wrap {
                height: 44px;
                padding-left: 16px;

                background-color: #fff;

                input {
                    height: 44px;
                    font-size: 14px;
                }
            }

            .textarea {
                --font-size: 14px;
                padding: 16px;
                padding-bottom: 8px;
                border-radius: 4px;
                background-color: #fff;

                .adm-text-area-count {
                    font-size: 12px;
                }
            }
        }
    }
}
```

### 点击返回隐藏弹层

`src/pages/Profile/Edit/index.tsx`

```ts
const hideInput = () => {
    setShowInput({
        type: '',
        visible: false,
    })
}

;<EditInput hideInput={hideInput}></EditInput>
```

`src/pages/Profile/Edit/EditInput/index.tsx`

```ts
import { Input, NavBar } from 'antd-mobile'
import styles from './index.module.scss'
type Props = {
    hideInput: () => void
}
const EditInput = ({ hideInput }: Props) => {
    return (
        <div className={styles.root}>
            <NavBar className='navbar' right={<span className='commit-btn'>提交</span>} onBack={hideInput}>
                编辑昵称
            </NavBar>

            <div className='edit-input-content'>
                <h3>昵称</h3>

                <div className='input-wrap'>
                    <Input placeholder='请输入' />
                </div>
            </div>
        </div>
    )
}

export default EditInput
```

## 修改昵称和简介-修改标题文字

1. 传递 type 属性。

```ts
<EditInput hideInput={hideInput} type={showInput.type} />
```

2. 根据 type 控制内容的展示。

```ts
import { Input, NavBar, TextArea } from 'antd-mobile'
import styles from './index.module.scss'
type Props = {
    hideInput: () => void
    // #1
    type: '' | 'name' | 'intro'
}
// #2
const EditInput = ({ hideInput, type }: Props) => {
    return (
        <div className={styles.root}>
            <NavBar className='navbar' right={<span className='commit-btn'>提交</span>} onBack={hideInput}>
                {/* #3 */}
                编辑{type === 'name' ? '昵称' : '简介'}
            </NavBar>

            <div className='edit-input-content'>
                {/* #4 */}
                <h3>{type === 'name' ? '昵称' : '简介'}</h3>

                {/* #5 */}
                {type === 'name' ? (
                    <div className='input-wrap'>
                        <Input placeholder='请输入' />
                    </div>
                ) : (
                    <TextArea className='textarea' placeholder='请输入简介' showCount maxLength={99} />
                )}
            </div>
        </div>
    )
}

export default EditInput
```

## 修改昵称和简介-数据回显

### 目标

能够在弹出层文本框中展示昵称或者简介。

### 代码

`Edit/components/EditInput/index.tsx`

```ts
import { useState } from 'react'
import { RootState } from '@/types/store'
import { Input, NavBar, TextArea } from 'antd-mobile'
import { useSelector } from 'react-redux'
import styles from './index.module.scss'
type Props = {
    hideInput: () => void
    type: '' | 'name' | 'intro'
}
const EditInput = ({ hideInput, type }: Props) => {
    // #1
    const { userProfile } = useSelector((state: RootState) => state.profile)
    // #2
    const [value, setValue] = useState(type === 'name' ? userProfile.name : userProfile.intro)
    return (
        <div className={styles.root}>
            <NavBar className='navbar' right={<span className='commit-btn'>提交</span>} onBack={hideInput}>
                编辑{type === 'name' ? '昵称' : '简介'}
            </NavBar>

            <div className='edit-input-content'>
                <h3>{type === 'name' ? '昵称' : '简介'}</h3>

                {/* #3 */}
                {type === 'name' ? (
                    <div className='input-wrap'>
                        <Input placeholder='请输入昵称' value={value} onChange={(v) => setValue(v)} maxLength={11} />
                    </div>
                ) : (
                    <TextArea className='textarea' placeholder='请输入简介' showCount maxLength={99} value={value} onChange={(v) => setValue(v)} />
                )}
            </div>
        </div>
    )
}

export default EditInput
```

## 修改昵称和简介-修复回显 Bug

-   默认情况，关闭弹层之后，数据回显还在，导致昵称和简介回显错乱。

-   通过 `destroyOnClose` 可以保证关闭弹层时销毁组件。

`pages/Profile/Edit/index.tsx`

```ts
<Popup visible={showInput.visible} position='right' onMaskClick={() => setShowInput({ type: '', visible: false })} destroyOnClose>
    <EditInput hideInput={hideInput} type={showInput.type} />
</Popup>
```

## 修改昵称和简介-自动获取光标

`pages/Profile/Edit/EditInput/index.tsx`

```ts
import { useState, useRef, useEffect } from 'react'
import { RootState } from '@/types/store'
import { Input, NavBar, TextArea } from 'antd-mobile'
import { useSelector } from 'react-redux'
import styles from './index.module.scss'
import { InputRef } from 'antd-mobile/es/components/input'
import { TextAreaRef } from 'antd-mobile/es/components/text-area'
type Props = {
    hideInput: () => void
    type: '' | 'name' | 'intro'
}
const EditInput = ({ hideInput, type }: Props) => {
    const { userProfile } = useSelector((state: RootState) => state.profile)
    const [value, setValue] = useState(type === 'name' ? userProfile.name : userProfile.intro)
    // #1
    const inputRef = useRef<InputRef>(null)
    const textRef = useRef<TextAreaRef>(null)

    // #3
    useEffect(() => {
        if (type === 'name') {
            inputRef.current?.focus()
        } else {
            textRef.current?.focus()
            document.querySelector('textarea')?.setSelectionRange(-1, -1)
        }
    }, [type])
    return (
        <div className={styles.root}>
            <NavBar className='navbar' right={<span className='commit-btn'>提交</span>} onBack={hideInput}>
                编辑{type === 'name' ? '昵称' : '简介'}
            </NavBar>

            <div className='edit-input-content'>
                <h3>{type === 'name' ? '昵称' : '简介'}</h3>
                {/* #2 */}
                {type === 'name' ? (
                    <div className='input-wrap'>
                        <Input placeholder='请输入昵称' value={value} onChange={(v) => setValue(v)} maxLength={11} ref={inputRef} />
                    </div>
                ) : (
                    <TextArea className='textarea' placeholder='请输入简介' showCount maxLength={99} value={value} onChange={(v) => setValue(v)} ref={textRef} />
                )}
            </div>
        </div>
    )
}

export default EditInput
```

## 修改昵称和简介-回传给父组件

### 目标

能够在点击提交时拿到昵称并回传给父组件。

### 分析

用户个人信息的状态是在 Edit 父组件中拿到的，所以修改用户个人信息也应该由 Edit 父组件发起，因此，需要将修改后的昵称回传给 Edit 父组件。

### 步骤

1. 在 Edit 组件定义 onUpdate 函数并传递给 EditInput 组件。

2. 在 EditInput 组件中为提交按钮绑定点击事件并调用传递过来的 onUpdate 函数。

3. 在 Edit 组件中根据 onUpdate 形参做相应的处理。

### 代码

`Edit/index.tsx`

```ts
const onUpdate = (key: string, value: string) => {
    console.log(key, value)
}

;<EditInput hideInput={hideInput} type={showInput.type} onUpdate={onUpdate} />
```

`Edit/components/EditInput/index.tsx`

```ts
import { useState, useRef, useEffect } from 'react'
import { RootState } from '@/types/store'
import { Input, NavBar, TextArea } from 'antd-mobile'
import { useSelector } from 'react-redux'
import styles from './index.module.scss'
import { InputRef } from 'antd-mobile/es/components/input'
import { TextAreaRef } from 'antd-mobile/es/components/text-area'
type Props = {
    hideInput: () => void
    type: '' | 'name' | 'intro'
    // #1
    onUpdate: (key: string, value: string) => void
}
// #2
const EditInput = ({ hideInput, type, onUpdate }: Props) => {
    const { userProfile } = useSelector((state: RootState) => state.profile)
    const [value, setValue] = useState(type === 'name' ? userProfile.name : userProfile.intro)
    const inputRef = useRef<InputRef>(null)
    const textRef = useRef<TextAreaRef>(null)

    useEffect(() => {
        if (type === 'name') {
            inputRef.current?.focus()
        } else {
            textRef.current?.focus()
            document.querySelector('textarea')?.setSelectionRange(-1, -1)
        }
    }, [type])
    return (
        <div className={styles.root}>
            <NavBar
                className='navbar'
                right={
                    // #3
                    <span className='commit-btn' onClick={() => onUpdate(type, value)}>
                        提交
                    </span>
                }
                onBack={hideInput}
            >
                编辑{type === 'name' ? '昵称' : '简介'}
            </NavBar>

            <div className='edit-input-content'>
                <h3>{type === 'name' ? '昵称' : '简介'}</h3>
                {type === 'name' ? (
                    <div className='input-wrap'>
                        <Input placeholder='请输入昵称' value={value} onChange={(v) => setValue(v)} maxLength={11} ref={inputRef} />
                    </div>
                ) : (
                    <TextArea className='textarea' placeholder='请输入简介' showCount maxLength={99} value={value} onChange={(v) => setValue(v)} ref={textRef} />
                )}
            </div>
        </div>
    )
}

export default EditInput
```

## 修改昵称和简介-发送请求修改

`actions/profile.ts`

```tsx
export function updateUserProfile(key: string, value: string): RootThunkAction {
    return async (dispatch) => {
        await request.patch('/user/profile', {
            [key]: value,
        })
        dispatch(getUserProfile())
    }
}
```

`pages/Profile/Edit/index.tsx`

```ts
const onUpdate = async (key: string, value: string) => {
    await dispatch(updateUserProfile(key, value))
    Toast.show({ icon: 'success', content: '修改成功' })
    hideInput()
}
```

## 修改性别和头像-准备弹出层

### 目标

能够显示修改性别弹出层。

### 步骤

1. 将准备好的修改性别的模板拷贝到 components 目录中。

2. 导入修改性别组件，在 Popup 组件中渲染。

### 代码

`Edit/index.tsx`

```tsx
import EditList from './components/EditList'
;<Popup visible={true} position='bottom' destroyOnClose>
    <EditList />
</Popup>
```

## 修改性别和头像-控制弹层的显示隐藏

### 目标

能搞控制修改性别弹出层的展示或隐藏。

### 分析

修改性别和修改头像的弹出层内容几乎是一样的，因此，也可以复用同一个弹出层组件。因此，接下来要从**复用**的角度，设计修改性别弹出层的逻辑（可以参考刚刚实现的修改昵称和简介）。

### 步骤

1. 准备用于控制修改性别弹出层的状态。

2. 为性别添加点击事件，在点击事件中修改状态进行展示。

3. 创建隐藏弹出层的控制函数，在点击遮罩时关闭弹出层，并传递给 EditList 组件。

4. 为 EditList 组件添加 props 类型，并接收隐藏函数。

5. 为取消按钮添加点击事件来触发隐藏弹出层。

### 代码

`pages/Profile/Edit/index.tsx`

```ts
import { useState } from 'react'
import { Button, List, DatePicker, NavBar, Popup, Toast } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import { useDispatch } from 'react-redux'
import classNames from 'classnames'
import styles from './index.module.scss'
import { getUserProfile, updateUserProfile } from '@/store/actions/profile'
import { useInitState } from '@/utils/hooks'
import EditInput from './EditInput'
import EditList from './EditList'
const Item = List.Item

type InputState = {
    visible: boolean
    type: '' | 'name' | 'intro'
}
// #1
type ListState = {
    visible: boolean
    type: '' | 'gender' | 'photo'
}

const ProfileEdit = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const { userProfile } = useInitState(getUserProfile, 'profile')
    const [showInput, setShowInput] = useState<InputState>({
        type: '',
        visible: false,
    })
    // #2
    const [showList, setShowList] = useState<ListState>({
        visible: false,
        type: '',
    })
    const hideInput = () => {
        setShowInput({
            type: '',
            visible: false,
        })
    }
    // #3
    const hideList = () => {
        setShowList({
            type: '',
            visible: false,
        })
    }
    const onUpdate = async (key: string, value: string) => {
        await dispatch(updateUserProfile(key, value))
        Toast.show({ icon: 'success', content: '修改成功' })
        hideInput()
    }
    return (
        <div className={styles.root}>
            <div className='content'>
                {/* 标题 */}
                <NavBar
                    style={{
                        '--border-bottom': '1px solid #F0F0F0',
                    }}
                    onBack={() => history.go(-1)}
                >
                    个人信息
                </NavBar>

                <div className='wrapper'>
                    {/* 列表 */}
                    <List className='profile-list'>
                        {/* 列表项 */}
                        {/* #5 */}
                        <Item
                            extra={
                                <span className='avatar-wrapper'>
                                    <img width={24} height={24} src={userProfile.photo} alt='' />
                                </span>
                            }
                            arrow
                            onClick={() =>
                                setShowList({
                                    visible: true,
                                    type: 'photo',
                                })
                            }
                        >
                            头像
                        </Item>
                        <Item
                            arrow
                            extra={userProfile.name}
                            onClick={() =>
                                setShowInput({
                                    visible: true,
                                    type: 'name',
                                })
                            }
                        >
                            昵称
                        </Item>
                        <Item
                            arrow
                            extra={<span className={classNames('intro', userProfile.intro && 'normal')}>{userProfile.intro || '未填写'}</span>}
                            onClick={() =>
                                setShowInput({
                                    visible: true,
                                    type: 'intro',
                                })
                            }
                        >
                            简介
                        </Item>
                    </List>

                    <List className='profile-list'>
                        {/* #6 */}
                        <Item arrow extra={userProfile.gender === 0 ? '男' : '女'} onClick={() => setShowList({ visible: true, type: 'gender' })}>
                            性别
                        </Item>
                        <Item arrow extra={userProfile.birthday}>
                            生日
                        </Item>
                    </List>

                    <DatePicker visible={false} value={new Date()} title='选择年月日' min={new Date(1900, 0, 1, 0, 0, 0)} max={new Date()} />
                </div>

                <div className='logout'>
                    <Button className='btn'>退出登录</Button>
                </div>
            </div>
            <Popup visible={showInput.visible} position='right' onMaskClick={() => setShowInput({ type: '', visible: false })} destroyOnClose>
                <EditInput hideInput={hideInput} type={showInput.type} onUpdate={onUpdate} />
            </Popup>
            {/* #4 */}
            <Popup visible={showList.visible} position='bottom' destroyOnClose onMaskClick={hideList}>
                {/* #7 */}
                <EditList onClose={hideList} />
            </Popup>
        </div>
    )
}

export default ProfileEdit
```

`pages/Profile/Edit/EditList/index.tsx`

```tsx
import styles from './index.module.scss'
type Props = {
    onClose: () => void
}
const EditList = ({ onClose }: Props) => {
    return (
        <div className={styles.root}>
            <div className='list-item'>男</div>
            <div className='list-item'>女</div>

            <div className='list-item' onClick={onClose}>
                取消
            </div>
        </div>
    )
}

export default EditList
```

## 修改性别和头像-控制内容的展示

1. 父组件把 type 传给子组件。

`src/pages/Profile/Edit/index.tsx`

```ts
<EditList hideList={hideList} type={showList.type}></EditList>
```

2. 子组件根据 type 属性控制需要显示的内容。

`src/pages/Profile/Edit/EditList/index.tsx`

```ts
import styles from './index.module.scss'
type Props = {
    onClose: () => void
    type: '' | 'gender' | 'photo'
}
const genderList = [
    { title: '男', value: '0' },
    { title: '女', value: '1' },
]

const photoList = [
    { title: '拍照', value: '' },
    { title: '本地选择', value: '' },
]
const EditList = ({ onClose, type }: Props) => {
    const list = type === 'gender' ? genderList : photoList
    return (
        <div className={styles.root}>
            {list.map((item) => (
                <div key={item.title} className='list-item'>
                    {item.title}
                </div>
            ))}

            <div className='list-item' onClick={onClose}>
                取消
            </div>
        </div>
    )
}

export default EditList
```

## 修改性别和头像-修改性别功能

1. 父组件把 onUpdate 传递给子组件。

```ts
const onUpdate = async (key: string, value: string) => {
    await dispatch(updateUserProfile(key, value))
    Toast.show({ icon: 'success', content: '修改成功' })
    hideInput()
    hideList()
}
```

```ts
<EditList onClose={hideList} type={showList.type} onUpdate={onUpdate} />
```

2. 子组件注册点击事件并调用 onUpdate。

```ts
import styles from './index.module.scss'
type Props = {
    onClose: () => void
    type: '' | 'gender' | 'photo'
    // #1
    onUpdate: (key: string, value: string) => void
}
const genderList = [
    { title: '男', value: '0' },
    { title: '女', value: '1' },
]

const photoList = [
    { title: '拍照', value: '' },
    { title: '本地选择', value: '' },
]
// #2
const EditList = ({ onClose, type, onUpdate }: Props) => {
    const list = type === 'gender' ? genderList : photoList
    return (
        <div className={styles.root}>
            {/* #3 onClick */}
            {list.map((item) => (
                <div key={item.title} className='list-item' onClick={() => onUpdate(type, item.value)}>
                    {item.title}
                </div>
            ))}

            <div className='list-item' onClick={onClose}>
                取消
            </div>
        </div>
    )
}

export default EditList
```

## 修改头像-弹窗选择图片

### 目标

能够在点击拍照或本地选择时弹窗选择图片。

### 代码

`pages/Profile/Edit/index.tsx`

```ts
import { useState, useRef } from 'react'
import { Button, List, DatePicker, NavBar, Popup, Toast } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import { useDispatch } from 'react-redux'
import classNames from 'classnames'
import styles from './index.module.scss'
import { getUserProfile, updateUserProfile } from '@/store/actions/profile'
import { useInitState } from '@/utils/hooks'
import EditInput from './EditInput'
import EditList from './EditList'
const Item = List.Item

type InputState = {
    visible: boolean
    type: '' | 'name' | 'intro'
}
type ListState = {
    visible: boolean
    type: '' | 'gender' | 'photo'
}

const ProfileEdit = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const { userProfile } = useInitState(getUserProfile, 'profile')
    const [showInput, setShowInput] = useState<InputState>({
        type: '',
        visible: false,
    })
    const [showList, setShowList] = useState<ListState>({
        visible: false,
        type: '',
    })
    const hideInput = () => {
        setShowInput({
            type: '',
            visible: false,
        })
    }
    const hideList = () => {
        setShowList({
            type: '',
            visible: false,
        })
    }
    // #3
    const fileRef = useRef<HTMLInputElement>(null)
    const onUpdate = async (key: string, value: string) => {
        // #1 修改头像
        if (key === 'photo') {
            fileRef.current!.click()
            return
        }
        await dispatch(updateUserProfile(key, value))
        Toast.show({ icon: 'success', content: '修改成功' })
        hideInput()
        hideList()
    }
    return (
        <div className={styles.root}>
            <div className='content'>
                {/* 标题 */}
                <NavBar
                    style={{
                        '--border-bottom': '1px solid #F0F0F0',
                    }}
                    onBack={() => history.go(-1)}
                >
                    个人信息
                </NavBar>

                <div className='wrapper'>
                    {/* 列表 */}
                    <List className='profile-list'>
                        {/* 列表项 */}
                        <Item
                            extra={
                                <span className='avatar-wrapper'>
                                    <img width={24} height={24} src={userProfile.photo} alt='' />
                                </span>
                            }
                            arrow
                            onClick={() =>
                                setShowList({
                                    visible: true,
                                    type: 'photo',
                                })
                            }
                        >
                            头像
                        </Item>
                        <Item
                            arrow
                            extra={userProfile.name}
                            onClick={() =>
                                setShowInput({
                                    visible: true,
                                    type: 'name',
                                })
                            }
                        >
                            昵称
                        </Item>
                        <Item
                            arrow
                            extra={<span className={classNames('intro', userProfile.intro && 'normal')}>{userProfile.intro || '未填写'}</span>}
                            onClick={() =>
                                setShowInput({
                                    visible: true,
                                    type: 'intro',
                                })
                            }
                        >
                            简介
                        </Item>
                    </List>

                    <List className='profile-list'>
                        <Item arrow extra={userProfile.gender === 0 ? '男' : '女'} onClick={() => setShowList({ visible: true, type: 'gender' })}>
                            性别
                        </Item>
                        <Item arrow extra={userProfile.birthday}>
                            生日
                        </Item>
                    </List>

                    <DatePicker visible={false} value={new Date()} title='选择年月日' min={new Date(1900, 0, 1, 0, 0, 0)} max={new Date()} />
                </div>

                <div className='logout'>
                    <Button className='btn'>退出登录</Button>
                </div>
            </div>
            {/* #2 */}
            <input type='file' hidden ref={fileRef} />
            <Popup visible={showInput.visible} position='right' onMaskClick={() => setShowInput({ type: '', visible: false })} destroyOnClose>
                <EditInput hideInput={hideInput} type={showInput.type} onUpdate={onUpdate} />
            </Popup>
            <Popup visible={showList.visible} position='bottom' destroyOnClose onMaskClick={hideList}>
                <EditList onClose={hideList} type={showList.type} onUpdate={onUpdate} />
            </Popup>
        </div>
    )
}

export default ProfileEdit
```

## 修改头像-获取选择的头像

### 目标

能够组装修改头像需要的数据。

### 步骤

1. 创建函数，监听 `input[type=file]` 选择文件的变化 change。

2. 在函数中，创建 FormData 对象。

3. 根据接口，拿到接口需要规定的参数名，并将选择的文件添加到 FormData 对象中。

### 代码

`pages/Profile/Edit/index.tsx`

```ts
// e => 移上 e
const onChangePhoto = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files![0]
    const fd = new FormData()
    fd.append('photo', file)
}
```

```ts
<input type='file' hidden ref={fileRef} onChange={onChangePhoto} />
```

## 修改头像-更新头像

### 目标

能够实现更新头像。

### 步骤

1. 在 Edit 组件中，分发修改头像的 action，传入 FormData 对象，并关闭弹出层。

2. 在 actions 中，创建修改头像的 action，接收到传递过来的 FormData 对象。

3. 发送请求，更新用户头像。

4. 分发 action 重新渲染。

### 代码

`pages/Profile/Edit/index.tsx`

```ts
const onChangePhoto = async (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files![0]
    const fd = new FormData()
    fd.append('photo', file)
    // #1
    await dispatch(updateUserPhoto(fd))
    // #2
    Toast.show({
        icon: 'success',
        content: '修改头像成功',
    })
    // #3
    hideList()
}
```

`actions/profile.ts`

```ts
export function updateUserPhoto(fd: FormData): RootThunkAction {
    return async (dispatch) => {
        await request.patch('/user/photo', fd)
        dispatch(getUserProfile())
    }
}
```

## 修改生日

### 目标

能够完成修改生日的功能。

### 步骤

`pages/Profile/Edit/index.tsx`

1. 创建状态 showBirthday 用来控制日期选择器的展示或隐藏。

```ts
const [showBirthday, setShowBirthday] = useState(false)
```

2. 将 showBirthday 设置为日期选择器的 visible 属性。

```ts
<DatePicker visible={showBirthday} title='选择年月日' />
```

3. 给生日绑定点击事件，在点击事件中修改 showBirthday 值为 true 来展示日期选择器。

```ts
const onBirthdayShow = () => {
    setShowBirthday(true)
}
```

```ts
<Item arrow extra={userProfile.birthday} onClick={onBirthdayShow}>
    生日
</Item>
```

4. 为日期选择器设置 value，值为用户的生日值。

```ts
<DatePicker visible={showBirthday} title='选择年月日' value={new Date(userProfile.birthday)} />
```

有可能 value 没有生效，需要指定 min 属性。

```ts
<DatePicker visible={showBirthday} title='选择年月日' value={new Date(userProfile.birthday)} min={new Date('1900-01-01')} max={new Date()} />
```

5. 在日期选择器关闭的回调中，隐藏日期选择器。

```ts
const onBirthdayHide = () => {
    setShowBirthday(false)
}
```

```ts
<DatePicker visible={showBirthday} title='选择年月日' onCancel={onBirthdayHide} value={new Date(userProfile.birthday)} min={new Date('1900-01-01')} max={new Date()} />
```

6. 修改功能。

```ts
<DatePicker
    visible={showBirthday}
    title='选择年月日'
    onCancel={onBirthdayHide}
    value={new Date(userProfile.birthday)}
    min={new Date('1900-01-01')}
    max={new Date()}
    onConfirm={(val) => {
        onUpdate('birthday', dayjs(val).format('YYYY-MM-DD'))
    }}
/>
```

## 退出登录-弹窗确认

### 目标

能够点击退出按钮时弹窗确认是否退出。

### 分析

不需要自定义样式的情况下，使用 `Dialog.confirm` 来弹窗确认即可。如果需要自定义弹窗按钮的样式，需要使用 `Dialog.show` 基础方法来实现。

### 步骤

1. 为退出登录按钮绑定点击事件。

2. 在点击事件中，使用 Dialog 弹窗让用户确认是否退出登录。

### 代码

`pages/Profile/Edit/index.tsx`

```ts
const logout = () => {
    Dialog.show({
        title: '温馨提示',
        content: '你确定要退出吗？',
        closeOnAction: true, // 只要点 action 就会被关闭
        actions: [
            [
                {
                    key: 'cancel',
                    text: '取消',
                    style: {
                        color: 'blue',
                    },
                },
                {
                    key: 'confirm',
                    text: '确定',
                    danger: true,
                    bold: true,
                    onClick: () => {
                        // 1. 清除token
                        dispatch(logout())
                        // 2. 跳转到登录
                        history.replace('/login')
                        // 3. 给一个提示消息
                        Toast.show({
                            icon: 'success',
                            content: '退出成功',
                        })
                    },
                },
            ],
        ],
    })
}
```

## 退出登录-功能完成

### 目标

能够实现退出功能。

### 步骤

1. 为退出按钮，绑定点击事件，在点击事件中分发退出 action。

2. 在 `types/store.d.ts` 中，创建退出登录的 action 类型。

```ts
export type LoginAction =
    | {
          type: 'login/login'
          payload: Token
      }
    | {
          type: 'login/logout'
      }
```

3. 在 `actions/login.ts` 中，创建退出 action 并清理 token。

```ts
export function logout(): LoginAction {
    removeToken()
    return {
        type: 'login/logout',
    }
}
```

4. 在 `reducers/login.ts` 中，处理退出 action 清空 token。

```ts
import { Token } from '@/types/data'
import { LoginAction } from '@/types/store'
import { getToken } from '@/utils/storage'
const initState: Token = getToken()
const login = (state = initState, action: LoginAction) => {
    if (action.type === 'login/login') {
        return action.payload
    }
    // !mark
    if (action.type === 'login/logout') {
        return {
            token: '',
            refresh_token: '',
        }
    }
    return state
}
export default login
```

## 封装鉴权路由组件

### 目标

能够封装鉴权路由组件实现登录访问控制功能，[链接](https://v5.reactrouter.com/web/example/auth-workflow)。

### 步骤

1. 在 components 目录中创建 PrivateRoute 路由组件。

2. 在 PrivateRoute 组件中，实现路由的登录访问控制逻辑。

3. 未登录时，重定向到登录页面，并传递要访问的路由地址。

4. 登录时，直接渲染要访问的路由。

### 代码

`components/PrivateRoute.tsx`

```ts
import { hasToken } from '@/utils/storage'
import { Route, Redirect, RouteProps } from 'react-router-dom'

export default function PrivateRoute({ children, ...rest }: RouteProps) {
    return (
        <Route
            {...rest}
            render={({ location }) =>
                hasToken() ? (
                    children
                ) : (
                    <Redirect
                        to={{
                            pathname: '/login',
                            state: { from: location.pathname },
                        }}
                    />
                )
            }
        />
    )
}
```

`App.tsx`

```ts
<PrivateRoute path='/profile/edit'>
    <ProfileEdit />
</PrivateRoute>
```

`pages/Layout/index.tsx`

```ts
<PrivateRoute path='/home/profile' exact>
    <Profile />
</PrivateRoute>
```

## 登录成功跳转到相应页面

### 目标

能够在登录时根据重定向路径跳转到相应页面。

### 步骤

1. 在 Login 组件中导入 useLocation 来获取路由重定向时传入的 state。

2. 调用 useLocation hook 时，指定 state 的类型。

3. 登录完成跳转页面时，判断 state 是否存在。

4. 如果存在，跳转到 state 指定的页面。

5. 如果不存在，默认跳转到首页。

### 代码

`pages/Login/index.tsx`

```ts
const location = useLocation<{ from: string }>()
const onFinish = async (values: LoginForm) => {
    await dispatch(login(values))
    Toast.show({
        content: '登录成功',
        icon: 'success',
        duration: 600,
        afterClose() {
            // history.push('/home')
            history.replace(location.state ? location.state.from : '/home')
        },
    })
}
```

## 理解无感刷新 Token

### 目标

能够理解什么是无感刷新 Token。

### 分析

一般情况下，我们用到的移动端的 App（比如，微信）只要登录过一次，一般就不需要再次重新登录，除非很长时间没有使用过 App，这是如何做到的呢？这就用到我们要讲的无感刷新 Token 了。

我们知道，登录时会拿到一个登录成功的标识 Token（身份令牌），有了这个令牌就可以进行登录后的操作了，比如，获取个人资料、修改个人信息等等。

但是，为了安全，登录标识 Token 一般都是有有效期的，比如，咱们的极客园项目中 Token 的有效期是 2 个小时，如果不进行额外的处理，2 小时以后，就得再次登录才可以，但是，这种用户体验不好，特别是移动端（不管是 App 还是 H5）。

相对来说，更好的用户体验是前面提到的微信的那种方式，它的原理简单来说是这样的，在登录时，同时拿到两个 Token。

1. 登录成功的令牌：`Token`。

2. 刷新 Token 的令牌：`refresh_token`。

刷新 Token 的令牌用来：在 Token 过期后，换取新的 Token（续费），从而实现“永久登录”效果，这就是所谓的：**无感刷新 token**。

<img src="/resource/images/image-20210903111508949.png"/>

思想总结如下。

-   无 Token，直接跳到登录页。

-   有 Token，则用 Refresh Token 换新 Token：换成功则用新 Token 重发原先的请求，没换成功则跳到登录页。

## 实现无感刷新 Token

### 目标

能够实现无感刷新 Token 实现自动登录。

### 分析

概述：在登录超时或者 Token 失效时，也就是服务器接口返回 401，通过 refresh_token 换取新的 Token。

过程如下（以获取个人资料数据为例）：

1. 发送请求获取个人资料数据。

2. 接口返回 401，也就是 Token 失效了。

3. 在响应拦截器中统一处理，换取新的 Token。

4. 将新的 Token 存储到本地缓存中。

5. 继续发送获取个人资料的请求，完成数据获取，关键点。

6. 如果整个过程中出现了任意异常，一般来说就是 refresh_token 也过期了，换取 Token 失败，此时，要进行错误处理，也就是： 清除 Token，跳转到登录页面。

axios 请求拦截过程说明：

```text
1 axios.get()
2 请求拦截器
3 响应代码
4 响应拦截器

以上 4 个步骤的执行顺序：

【1 axios.get()】 --> 【2 请求拦截器】>>> 服务器处理 >>> 【4 响应拦截器】 --> 【3 响应代码】
```

### 步骤

1. 使用 try-catch 处理异常，出现异常时，清除 token，清空 Redux token，跳转到登录页面。

2. 判断本地存储中，是否有 `refresh_token`。

3. 如果没有，直接跳转到登录页面，让用户登录即可。

4. 如果有，就使用 `refresh_token` 来通过 **axios** 发送请求，换取新的 token。

5. 将新获取到的 Token 存储到本地缓存中和 Redux 中。

6. 继续发送原来的请求。

### 代码

`utils/request.ts`

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
        if (error.response.status === 401) {
            const token = getToken()
            // #1 没有刷新 refresh_token
            if (!token.refresh_token) {
                history.replace('/login', {
                    from: history.location.pathname,
                })
                Toast.show('登录信息过期')
                return Promise.reject(error)
            }
            // #2 使用 refresh_token 换取新 token
            // ...
            return Promise.reject(error)
        }
        Toast.show(error.response.data.message)
        return Promise.reject(error)
    }
)
```

存储到本地和 Redux 并重新发送请求

`actions/login.ts`

```ts
export function saveToken(token: Token): LoginAction {
    setToken(token)
    return {
        type: 'login/login',
        payload: token,
    }
}

export const login = (values: LoginForm): RootThunkAction => {
    return async (dispatch) => {
        const res = await request.post<ApiResponse<Token>>('/authorizations', values)
        /* dispatch({
            type: 'login/login',
            payload: res.data.data,
        })
        // !存到本地
        setToken(res.data.data) */
        dispatch(saveToken(res.data.data))
    }
}
```

`utils/history.js`

```ts
import { createBrowserHistory } from 'history'
const history = createBrowserHistory()
export default history
```

`src/App.tsx`

```ts
import history from './utils/history'
;<Router history={history}></Router>
```

`utils/request.ts`

```ts
request.interceptors.response.use(
    function (response) {
        return response
    },
    async function (error: AxiosError<{ message: string }>) {
        if (!error.response) {
            // Network Error
            Toast.show('网络繁忙，请稍后重试')
            return Promise.reject(error)
        }
        if (error.response.status === 401) {
            const token = getToken()
            // !#1 没有刷新 refresh_token
            if (!token.refresh_token) {
                history.replace('/login', {
                    from: history.location.pathname,
                })
                Toast.show('登录信息过期')
                return Promise.reject(error)
            }
            // !#2 使用 refresh_token 换取新 token
            // axios.put('/authorizations', null, { headers: {} })
            const res = await axios.request<ApiResponse<{ token: string }>>({
                method: 'put',
                url: '/authorizations',
                baseURL,
                headers: {
                    Authorization: `Bearer ${token.refresh_token}`,
                },
            })
            // !#3 存储到本地和 Redux
            store.dispatch(
                saveToken({
                    token: res.data.data.token,
                    refresh_token: token.refresh_token,
                })
            )
            // !#4 重新发请求
            return request.request(error.config)
        }
        Toast.show(error.response.data.message)
        return Promise.reject(error)
    }
)
```

刷新 Token 失败的处理。

```ts
request.interceptors.response.use(
    function (response) {
        return response
    },
    async function (error: AxiosError<{ message: string }>) {
        if (!error.response) {
            // Network Error
            Toast.show('网络繁忙，请稍后重试')
            return Promise.reject(error)
        }
        if (error.response.status === 401) {
            const token = getToken()
            // !#1 没有刷新 refresh_token
            if (!token.refresh_token) {
                history.replace('/login', {
                    from: history.location.pathname,
                })
                Toast.show('登录信息过期')
                return Promise.reject(error)
            }
            try {
                // !#2 使用 refresh_token 换取新 token
                // axios.put('/authorizations', null, { headers: {} })
                const res = await axios.request<ApiResponse<{ token: string }>>({
                    method: 'put',
                    url: '/authorizations',
                    baseURL,
                    headers: {
                        Authorization: `Bearer ${token.refresh_token}`,
                    },
                })
                // !#3 存储到本地和 Redux
                store.dispatch(
                    saveToken({
                        token: res.data.data.token,
                        refresh_token: token.refresh_token,
                    })
                )
                // !#4 重新发请求
                return request.request(error.config)
            } catch {
                // 刷新 Token 失败
                history.replace('/login', {
                    from: history.location.pathname,
                })
                Toast.show('登录信息失败')
                // store.dispatch(saveToken({ token: '', refresh_token: '' }))
                store.dispatch(logout())
                return Promise.reject(error)
            }
        }
        Toast.show(error.response.data.message)
        return Promise.reject(error)
    }
)
```
