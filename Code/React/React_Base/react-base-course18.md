---
title: 18_极客园 H5
date: 2021-12-24 16:40:56
tags:
---

## 今日目标

✔ 掌握 WebSocket 的使用。

<!-- more -->

## 为什么需要 websocket

> WebSocket 是一种数据通信协议，类似于我们常见的 http 协议。

初次接触 WebSocket 的人，都会问同样的问题：我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？

答案很简单，因为 HTTP 协议有一个缺陷：通信只能由客户端发起。http 基于请求响应实现。

举例来说，我们想了解今天的天气，只能是客户端向服务器发出请求，服务器返回查询结果。HTTP 协议做不到服务器主动向客户端推送信息。

这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。我们只能使用["轮询"](https://www.pubnub.com/blog/2014-12-01-http-long-polling/)：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。最典型的场景就是聊天室。

轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。因此，工程师们一直在思考，有没有更好的方法。WebSocket 就是这样发明的。

## websocket 简介

WebSocket 协议在 2008 年诞生，2011 年成为国际标准。所有浏览器都已经支持了。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于[服务器推送技术](https://en.wikipedia.org/wiki/Push_technology)的一种。

典型的 websocket 应用场景：

-   即时通讯，，，客服
-   聊天室 广播
-   点餐

![image-20201121170006970](/resource/images/image-20201121170006970.png)

## 静态结构

> 目标：实现小智同学页面的静态结构和样式

从素材中获取小智同学的结构与样式

## 动态渲染聊天记录列表

### 目标

将聊天数据存在数组状态中，再动态渲染到界面上。

1. 声明一个数组状态

```ts
const [messageList, setMessageList] = useState<{ type: 'robot' | 'user'; text: string }[]>([
    { type: 'robot', text: '亲爱的用户您好，小智同学为您服务。' },
    { type: 'user', text: '你好' },
])
```

2. 从 Redux 中获取当前用户基本信息

```ts
const { user } = useInitState(getUser, 'profile')
```

3. 根据数组数据，动态渲染聊天记录列表

```ts
import { useState } from 'react'
import Icon from '@/components/Icon'
import { NavBar, Input } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import styles from './index.module.scss'
import { useInitState } from '@/utils/hooks'
import { getUser } from '@/store/actions/profile'

const Chat = () => {
    const history = useHistory()
    // #1
    const [messageList] = useState<{ type: 'robot' | 'user'; text: string }[]>([
        { type: 'robot', text: '亲爱的用户您好，小智同学为您服务。' },
        { type: 'user', text: '你好' },
    ])
    // #2
    const { user } = useInitState(getUser, 'profile')
    return (
        <div className={styles.root}>
            {/* 顶部导航栏 */}
            <NavBar className='fixed-header' onBack={() => history.go(-1)}>
                小智同学
            </NavBar>

            <div className='chat-list'>
                {/* #3 */}
                {messageList.map((msg, index) => {
                    if (msg.type === 'robot') {
                        // 机器
                        return (
                            <div className='chat-item' key={index}>
                                <Icon type='iconbtn_xiaozhitongxue' />
                                <div className='message'>{msg.text}</div>
                            </div>
                        )
                    } else {
                        // 用户
                        return (
                            <div className='chat-item user' key={index}>
                                <img src={user.photo} alt='' />
                                <div className='message'>{msg.text}</div>
                            </div>
                        )
                    }
                })}
            </div>
            {/* 底部消息输入框 */}
            <div className='input-footer'>
                <Input className='no-border' placeholder='请描述您的问题' />
                <Icon type='iconbianji' />
            </div>
        </div>
    )
}

export default Chat
```

<img src="/resource/images/image-20210904085509862.png" alt="image-20210904085509862" style="zoom:50%;" />

## 建立与服务器的连接

### 目标

使用 socket.io 客户端与服务器建立 WebSocket 长连接。

### 基本使用

1. 安装包：`yarn add socket.io-client` 只安装客户端要使用到的包。

2. 和服务器进行连接。

```ts
import io from 'socket.io-client'
// 和服务器建立链接
const client = io('地址', {
    query: {
        token: 用户token,
    },
    transports: ['websocket'],
})
```

3. 和服务器进行通讯。

```ts
client.on('connect', () => {
    // 当和服务器建立连接成功触发
})
client.on('message', () => {
    // 接收到服务器的消息触发
})
client.on('disconnect', () => {
    // 和服务器断开链接触发
})
client.emit('message', 值) // 主动给服务器发送消息
client.close() // 主动关闭和服务器的链接
```

### 建立连接

本项目聊天客服的后端接口，使用的是基于 WebSocket 协议的 socket.io 接口，我们可以使用专门的 socket.io 客户端库，就能轻松建立起连接并进行互相通信。

借助 `useEffect`，在进入页面时调用客户端库建立 socket.io 连接

1. 安装 socket.io 客户端库：`socket.io-client`

```bash
yarn add socket.io-client
```

2. 在进入机器人客服页面时，创建 socket.io 客户端

```ts
import { useState, useEffect } from 'react'
import Icon from '@/components/Icon'
import { NavBar, Input } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import styles from './index.module.scss'
import { useInitState } from '@/utils/hooks'
import { getUser } from '@/store/actions/profile'
// #1
import io from 'socket.io-client'
import { getToken } from '@/utils/storage'

const Chat = () => {
    const history = useHistory()
    const [messageList, setMessageList] = useState<{ type: 'robot' | 'user'; text: string }[]>([
        { type: 'robot', text: '亲爱的用户您好，小智同学为您服务。' },
        { type: 'user', text: '你好' },
    ])
    const { user } = useInitState(getUser, 'profile')
    // #2
    useEffect(() => {
        const client = io('http://toutiao.itheima.net', {
            transports: ['websocket'],
            query: {
                token: getToken().token,
            },
        })
        client.on('connect', () => {
            // 向聊天记录中添加一条消息
            setMessageList((messageList) => [...messageList, { type: 'robot', text: '我现在恭候着您的提问。' }])
        })
        return () => {
            client.close()
        }
    }, [])
}
```

## 给机器人发消息

### 目标

将输入框内容通过 socket.io 发送到服务端。

### 步骤

实现思路：使用 socket.io 实例的 `emit()` 方法发送信息。

1. 声明一个状态，并绑定消息输入框。

```ts
const [message, setMessage] = useState('')
```

```jsx
<Input className='no-border' placeholder='请描述您的问题' value={message} onChange={(v) => setMessage(v)} />
```

2. 为消息输入框添加键盘事件，在输入回车时发送消息。

```jsx
// #2
<Input className='no-border' placeholder='请描述您的问题' value={message} onChange={(v) => setMessage(v)} onKeyUp={onSendMessage} />
```

3. 完整代码。

```ts
import { useState, useEffect, useRef } from 'react'
import Icon from '@/components/Icon'
import { NavBar, Input } from 'antd-mobile'
import { useHistory } from 'react-router-dom'
import styles from './index.module.scss'
import { useInitState } from '@/utils/hooks'
import { getUser } from '@/store/actions/profile'
import io, { Socket } from 'socket.io-client'
import { getToken } from '@/utils/storage'

const Chat = () => {
    const history = useHistory()
    const [messageList, setMessageList] = useState<{ type: 'robot' | 'user'; text: string }[]>([
        { type: 'robot', text: '亲爱的用户您好，小智同学为您服务。' },
        { type: 'user', text: '你好' },
    ])
    // #1
    const [message, setMessage] = useState('')
    // #4
    const clientRef = useRef<Socket | null>(null)
    const { user } = useInitState(getUser, 'profile')
    // #3
    const onSendMessage = (e: React.KeyboardEvent<HTMLInputElement>) => {
        if (e.key === 'Enter') {
            // #6
            clientRef.current?.emit('message', {
                msg: message,
                timestamp: Date.now(),
            })
            setMessageList((messageList) => [...messageList, { type: 'user', text: message }])
            setMessage('')
        }
    }
    useEffect(() => {
        const client = io('http://toutiao.itheima.net', {
            transports: ['websocket'],
            query: {
                token: getToken().token,
            },
        })
        // #5
        clientRef.current = client
        client.on('connect', () => {
            // 向聊天记录中添加一条消息
            setMessageList((messageList) => [...messageList, { type: 'robot', text: '我现在恭候着您的提问。' }])
        })
        return () => {
            client.close()
        }
    }, [])
    return (
        <div className={styles.root}>
            {/* 顶部导航栏 */}
            <NavBar className='fixed-header' onBack={() => history.go(-1)}>
                小智同学
            </NavBar>

            <div className='chat-list'>
                {messageList.map((msg, index) => {
                    if (msg.type === 'robot') {
                        // 机器
                        return (
                            <div className='chat-item' key={index}>
                                <Icon type='iconbtn_xiaozhitongxue' />
                                <div className='message'>{msg.text}</div>
                            </div>
                        )
                    } else {
                        // 用户
                        return (
                            <div className='chat-item user' key={index}>
                                <img src={user.photo} alt='' />
                                <div className='message'>{msg.text}</div>
                            </div>
                        )
                    }
                })}
            </div>
            {/* 底部消息输入框 */}
            {/* #2 */}
            <div className='input-footer'>
                <Input className='no-border' placeholder='请描述您的问题' value={message} onChange={(v) => setMessage(v)} onKeyUp={onSendMessage} />
                <Icon type='iconbianji' />
            </div>
        </div>
    )
}

export default Chat
```

## 接收机器人回复的消息

### 目标

1. 通过 socket.io 监听回复的消息，并添加到聊天列表中。

2. 且当消息较多出现滚动条时，有后续新消息的话总将滚动条滚动到最底部。

3. 使用 socket.io 实例的 `message` 事件接收信息，在聊天列表数据变化时，操作列表容器元素来设置滚动量。

### 步骤

1. 在 socket.io 实例的 `message` 事件中，将接收到的消息添加到聊天列表：

```ts
useEffect(() => {
    const client = io('http://toutiao.itheima.net', {
        transports: ['websocket'],
        query: {
            token: getToken().token,
        },
    })
    clientRef.current = client
    client.on('connect', () => {
        // 向聊天记录中添加一条消息
        setMessageList((messageList) => [...messageList, { type: 'robot', text: '我现在恭候着您的提问。' }])
    })
    client.on('message', (data) => {
        // 向聊天记录中添加机器人回复的消息
        setMessageList((messageList) => [...messageList, { type: 'robot', text: data.msg }])
    })
    return () => {
        client.close()
    }
}, [])
```

2. 声明一个 ref 并设置到聊天列表的容器元素上。

```ts
const chatListRef = useRef<HTMLDivElement>(null)
```

```ts
<div className="chat-list" ref={chatListRef}>
```

3. 通过 `useEffect` 监听聊天数据变化，对聊天容器元素的 scrollTop 进行设置。

```ts
// 监听聊天数据的变化，改变聊天容器元素的 scrollTop 值让页面滚到最底部
useEffect(() => {
    const current = chatListRef.current!
    current.scrollTop = current.scrollHeight
    // current.scrollTop = current.scrollHeight - current.offsetHeight
}, [messageList])
```
