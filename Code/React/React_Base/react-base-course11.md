---
title: 11_极客园 PC
date: 2021-11-22 00:50:54
tags:
---

## 今日目标

✔ 理解内容管理的业务逻辑。

✔ 掌握富文本编辑器的使用。

<!-- more -->

## 内容管理结构

-   Card 组件，[文档](https://ant.design/components/card-cn/)。

```js
import React from 'react'
import { Card } from 'antd'
import styles from './index.module.scss'

export default function Article() {
    return (
        <div className={styles.root}>
            <Card title='面包屑导航'>我是内容</Card>
        </div>
    )
}
```

-   Breadcrumb

```jsx
import { Card, Breadcrumb } from 'antd'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'

export default function Article() {
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                Article
            </Card>
        </div>
    )
}
```

## 搜索表单结构

-   表单结构。

```jsx
<Form>
    <Form.Item label='状态'></Form.Item>
    <Form.Item label='频道'></Form.Item>
    <Form.Item label='日期'></Form.Item>
    <Form.Item>
        <Button type='primary' htmlType='submit'>
            筛选
        </Button>
    </Form.Item>
</Form>
```

-   状态。

```jsx
<Form initialValues={{ status: -1 }}>
    <Form.Item label='状态' name='status'>
        <Radio.Group>
            <Radio value={-1}>全部</Radio>
            <Radio value={0}>草稿</Radio>
            <Radio value={1}>待审核</Radio>
            <Radio value={2}>审核通过</Radio>
            <Radio value={3}>审核失败</Radio>
        </Radio.Group>
    </Form.Item>
</Form>
```

-   下拉框。

```jsx
<Form.Item label='频道' name='channel_id'>
    <Select placeholder='请选择频道' style={{ width: 200 }}>
        <Select.Option value='jack'>Jack</Select.Option>
        <Select.Option value='lucy'>Lucy</Select.Option>
        <Select.Option value='rose'>Rose</Select.Option>
    </Select>
</Form.Item>
```

-   日期选择。

```jsx
<Form.Item label='日期' name='date'>
    <DatePicker.RangePicker />
</Form.Item>
```

-   完整代码。

```jsx
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker } from 'antd'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
const { RangePicker } = DatePicker

export default function Article() {
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                {/* 表单 */}
                <Form
                    initialValues={{
                        status: -1,
                    }}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            <Radio value={-1}>全部</Radio>
                            <Radio value={0}>草稿</Radio>
                            <Radio value={1}>待审核</Radio>
                            <Radio value={2}>审核通过</Radio>
                            <Radio value={3}>审核失败</Radio>
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            <Select.Option value='1'>Jack</Select.Option>
                            <Select.Option value='2'>Sherry</Select.Option>
                            <Select.Option value='3'>Moka</Select.Option>
                        </Select>
                    </Form.Item>
                    {/* name 先指定为 date，后续再处理 */}
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary'>筛选</Button>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

## 日期中文处理

`index.js` 入口文件，[参考文档](https://ant-design.gitee.io/components/date-picker-cn/#%E5%9B%BD%E9%99%85%E5%8C%96%E9%85%8D%E7%BD%AE)。

```jsx
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import { ConfigProvider } from 'antd'
import 'antd/dist/antd.css'
import './index.scss'
import store from './store'
import App from './App'
// #1
import 'moment/locale/zh-cn'
// #2
import locale from 'antd/lib/locale/zh_CN'

ReactDOM.render(
    <Provider store={store}>
        {/* #3 */}
        <ConfigProvider locale={locale}>
            <App />
        </ConfigProvider>
    </Provider>,
    document.getElementById('root')
)
```

## 请求频道数据并渲染

### 步骤

1. 在 `store/constants/index.js` 文件中创建 actionType。

2. 在 `store/actions/article.js` 文件中创建异步的 action 和 actionCreator。

3. 在 `store/reducers/article.js` 文件中创建 reducer。

4. 在 `store/reducers/index.js` 文件中合并 reducer。

5. 在 `pages/Article/index.js` 组件中通过 useDispatch 来触发 action，通过 useSelector 来拿到数据。

### 代码

`store/constants/index.js`

```jsx
export const LOGIN = 'LOGIN'
export const GET_USER_INFO = 'GET_USER_INFO'
export const LOGOUT = 'LOGOUT'
export const GET_CHANNEL_LIST = 'GET_CHANNEL_LIST'
```

`store/actions/article.js`

```jsx
import request from '@/utils/request'
import { GET_CHANNEL_LIST } from '../constants'

export const getChannelListAc = (payload) => ({
    type: GET_CHANNEL_LIST,
    payload,
})

export const getChannelList = () => {
    return async (dispatch) => {
        const res = await request.get('/channels')
        // 注意这里是 getChannelListAc 而不是 getChannelList
        dispatch(getChannelListAc(res.data.data.channels))
    }
}
```

`reducers/article.js`

```jsx
import { GET_CHANNEL_LIST } from '../constants'
const initValue = {
    channels: [],
}
export default function article(state = initValue, action) {
    switch (action.type) {
        case GET_CHANNEL_LIST:
            return {
                ...state,
                channels: action.payload,
            }
        default:
            return state
    }
}
```

`store/reducers/index.js`

```jsx
import { combineReducers } from 'redux'
import login from './login'
import user from './user'
import article from './article'
const rootReducer = combineReducers({
    login,
    user,
    article,
})
export default rootReducer
```

`pages/Article/index.js`

```jsx
import { useEffect } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
import { getChannelList } from '@/store/actions/article'
const { RangePicker } = DatePicker

export default function Article() {
    const dispatch = useDispatch()
    // #2
    const channels = useSelector((state) => state.article.channels)
    useEffect(() => {
        // #1
        dispatch(getChannelList())
    }, [dispatch])
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                {/* 表单 */}
                <Form
                    initialValues={{
                        status: -1,
                    }}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            <Radio value={-1}>全部</Radio>
                            <Radio value={0}>草稿</Radio>
                            <Radio value={1}>待审核</Radio>
                            <Radio value={2}>审核通过</Radio>
                            <Radio value={3}>审核失败</Radio>
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {/* #3 */}
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary'>筛选</Button>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

### 提交

点击筛选按钮，测试下能不能拿到选择到的数据。

```js
import { useEffect } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
import { getChannelList } from '@/store/actions/article'
const { RangePicker } = DatePicker

export default function Article() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    // #2
    const onFinish = (values) => {
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                {/* #1 */}
                <Form
                    initialValues={{
                        status: -1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            <Radio value={-1}>全部</Radio>
                            <Radio value={0}>草稿</Radio>
                            <Radio value={1}>待审核</Radio>
                            <Radio value={2}>审核通过</Radio>
                            <Radio value={3}>审核失败</Radio>
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        {/* #3 */}
                        <Button type='primary' htmlType='submit'>
                            筛选
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

## 搭建表格基本结构

`pages/Article/index.js`

```jsx
const dataSource = [
    {
        key: '1',
        name: '胡彦斌',
        age: 32,
        address: '西湖区湖底公园1号',
    },
    {
        key: '2',
        name: '胡彦祖',
        age: 42,
        address: '西湖区湖底公园1号',
    },
]

const columns = [
    {
        title: '姓名',
        dataIndex: 'name',
        key: 'name',
    },
    {
        title: '年龄',
        dataIndex: 'age',
        key: 'age',
    },
    {
        title: '住址',
        dataIndex: 'address',
        key: 'address',
    },
]
```

```jsx
<Card title='根据筛选条件共查询到xxx条结果:' style={{ marginTop: 10 }}>
    <Table dataSource={dataSource} columns={columns} />
</Card>
```

## 请求所有表格数据

### 步骤

1. 在 `store/constants/index.js` 文件中创建 actionType。

2. 在 `store/actions/article.js` 文件中创建异步的 action 和 actionCreator。

3. 在 `store/reducers/article.js` 文件中创建 reducer。

4. 在 `pages/Article/index.js` 组件中通过 useDispatch 来触发 action，通过 useSelector 来拿到数据。

### 代码

1. `store/constants/index.js`

```js
export const LOGIN = 'LOGIN'
export const GET_USER_INFO = 'GET_USER_INFO'
export const LOGOUT = 'LOGOUT'
export const GET_CHANNEL_LIST = 'GET_CHANNEL_LIST'
export const GET_ARTICLE_LIST = 'GET_ARTICLE_LIST'
```

2. `store/actions/article.js`

```jsx
import request from '@/utils/request'
import { GET_ARTICLE_LIST, GET_CHANNEL_LIST } from '../constants'

export const getChannelListAc = (payload) => ({
    type: GET_CHANNEL_LIST,
    payload,
})

export const getChannelList = () => {
    return async (dispatch) => {
        const res = await request.get('/channels')
        dispatch(getChannelListAc(res.data.data.channels))
    }
}

export const getArticleListAc = (payload) => ({
    type: GET_ARTICLE_LIST,
    payload,
})

export const getArticleList = (params) => {
    return async (dispatch) => {
        const res = await request({
            url: '/mp/articles',
            params,
        })
        dispatch(getArticleListAc(res.data.data))
    }
}
```

3. `store/reducers/article.js`

```js
import { GET_ARTICLE_LIST, GET_CHANNEL_LIST } from '../constants'
const initValue = {
    channels: [],
    articles: {},
}
export default function article(state = initValue, action) {
    switch (action.type) {
        case GET_CHANNEL_LIST:
            // !注意这里合并的一定是 state，而不是 initValue
            return {
                ...state,
                channels: action.payload,
            }
        case GET_ARTICLE_LIST:
            return {
                ...state,
                articles: action.payload,
            }
        default:
            return state
    }
}
```

4. `pages/Article/index.js`

```jsx
import { useEffect } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker, Table } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
import { getChannelList, getArticleList } from '@/store/actions/article'
const { RangePicker } = DatePicker

const dataSource = [
    {
        key: '1',
        name: '胡彦斌',
        age: 32,
        address: '西湖区湖底公园1号',
    },
    {
        key: '2',
        name: '胡彦祖',
        age: 42,
        address: '西湖区湖底公园1号',
    },
]

const columns = [
    {
        title: '姓名',
        dataIndex: 'name',
        key: 'name',
    },
    {
        title: '年龄',
        dataIndex: 'age',
        key: 'age',
    },
    {
        title: '住址',
        dataIndex: 'address',
        key: 'address',
    },
]

export default function Article() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    useEffect(() => {
        dispatch(getChannelList())
        // #mark：这里没有传递参数表示获取所有的
        dispatch(getArticleList())
    }, [dispatch])
    const onFinish = (values) => {
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form
                    initialValues={{
                        status: -1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            <Radio value={-1}>全部</Radio>
                            <Radio value={0}>草稿</Radio>
                            <Radio value={1}>待审核</Radio>
                            <Radio value={2}>审核通过</Radio>
                            <Radio value={3}>审核失败</Radio>
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary' htmlType='submit'>
                            筛选
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
            <Card title='根据筛选条件共查询到xxx条结果：' style={{ marginTop: 10 }}>
                <Table dataSource={dataSource} columns={columns} />
            </Card>
        </div>
    )
}
```

## 渲染表格数据

### 使用数据并渲染数量

```js
import { useEffect } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker, Table } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
import { getChannelList, getArticleList } from '@/store/actions/article'
const { RangePicker } = DatePicker

const columns = [
    {
        title: '封面',
        dataIndex: 'cover',
        key: 'cover',
    },
    {
        title: '标题',
        dataIndex: 'title',
        key: 'title',
    },
    {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
    },
    {
        title: '发布时间',
        dataIndex: 'pubdate',
        key: 'pubdate',
    },
    {
        title: '阅读数',
        dataIndex: 'read_count',
        key: 'read_count',
    },
    {
        title: '评论数',
        dataIndex: 'comment_count',
        key: 'comment_count',
    },
    {
        title: '点赞数',
        dataIndex: 'like_count',
        key: 'like_count',
    },
    {
        title: '操作',
        key: 'id',
    },
]

export default function Article() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    const articles = useSelector((state) => state.article.articles)
    useEffect(() => {
        dispatch(getChannelList())
        dispatch(getArticleList())
    }, [dispatch])
    const onFinish = (values) => {
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form
                    initialValues={{
                        status: -1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            <Radio value={-1}>全部</Radio>
                            <Radio value={0}>草稿</Radio>
                            <Radio value={1}>待审核</Radio>
                            <Radio value={2}>审核通过</Radio>
                            <Radio value={3}>审核失败</Radio>
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary' htmlType='submit'>
                            筛选
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
            {/* #mark */}
            <Card title={`根据筛选条件共查询到 ${articles.total_count} 条结果`} style={{ marginTop: 10 }}>
                <Table dataSource={articles.results} columns={columns} />
            </Card>
        </div>
    )
}
```

### 表格状态处理

```js
const STATUS = [
    { id: -1, title: '全部', color: 'magenta' },
    { id: 0, title: '草稿', color: 'red' },
    { id: 1, title: '待审核', color: 'volcano' },
    { id: 2, title: '审核通过', color: 'lime' },
    { id: 3, title: '审核失败', color: 'gold' },
]

const columns = [
    {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
        render: (status) => {
            // 指定 dataIndex，render 里面的参数表示 dataIndex 的值，不指定的话 render 里面的参数表示当前行的数据
            const obj = STATUS.find((item) => item.id === status)
            return <Tag color={obj.color}>{obj.title}</Tag>
        },
    },
]
```

### 表单状态处理

上面表单里面的状态也不用写死啦，可以根据 STATUS 来进行渲染。

```jsx
<Form.Item label='状态' name='status'>
    <Radio.Group>
        {STATUS.map((item) => (
            <Radio key={item.id} value={item.id}>
                {item.title}
            </Radio>
        ))}
    </Radio.Group>
</Form.Item>
```

### 表格封面处理

```js
const columns = [
    {
        title: '封面',
        dataIndex: 'cover',
        key: 'cover',
        render(cover) {
            if (cover.type === 0) {
                return <Image width={200} height={150} src={img} />
            } else {
                return <Image width={200} height={150} src={cover.images[0]} fallback={img} />
            }
        },
    },
]
```

### 操作按钮处理

```js
const columns = [
    {
        title: '操作',
        key: 'id',
        render() {
            return (
                <Space>
                    <Button type='primary' shape='circle' icon={<EditOutlined />}></Button>
                    <Button type='primary' shape='circle' danger icon={<DeleteOutlined />}></Button>
                </Space>
            )
        },
    },
]
```

### 滚动条处理

`pages/Layout/index.js`

```jsx
<Layout style={{ padding: 20, overflow: 'auto' }}>
```

### key 属性处理

-   如果数据中有 key 属性，那么就不用指定 key 属性。

-   如果数据中没有 key 属性，必须通过 rowKey 属性明确声明用哪个字段当做 key。

```jsx
<Table rowKey='id' dataSource={this.state.articles} columns={columns} />
// <Table rowKey={(record) => record.id} dataSource={articles.results} columns={columns}/>
```

### 筛选功能

```js
const onFinish = (values) => {
    const params = {}
    if (values.status !== -1) {
        params.status = values.status
    }
    if (values.channel_id) {
        params.channel_id = values.channel_id
    }
    if (values.date) {
        params.begin_pubdate = values.date[0].startOf('day').format('YYYY-MM-DD HH:mm:ss')
        params.end_pubdate = values.date[1].endOf('day').format('YYYY-MM-DD HH:mm:ss')
    }
    dispatch(getArticleList(params))
}
```

### 完整代码

`pages/Article/index.js`

```jsx
import { useEffect } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker, Table, Tag, Image, Space } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import { EditOutlined, DeleteOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import { getChannelList, getArticleList } from '@/store/actions/article'
import img from '@/assets/error.png'
const { RangePicker } = DatePicker

const STATUS = [
    { id: -1, title: '全部', color: 'magenta' },
    { id: 0, title: '草稿', color: 'red' },
    { id: 1, title: '待审核', color: 'volcano' },
    { id: 2, title: '审核通过', color: 'lime' },
    { id: 3, title: '审核失败', color: 'gold' },
]

const columns = [
    {
        title: '封面',
        dataIndex: 'cover',
        key: 'cover',
        render(cover) {
            if (cover.type === 0) {
                return <Image width={200} height={150} src={img} />
            } else {
                return <Image width={200} height={150} src={cover.images[0]} fallback={img} />
            }
        },
    },
    {
        title: '标题',
        dataIndex: 'title',
        key: 'title',
    },
    {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
        render: (status) => {
            const obj = STATUS.find((item) => item.id === status)
            return <Tag color={obj.color}>{obj.title}</Tag>
        },
    },
    {
        title: '发布时间',
        dataIndex: 'pubdate',
        key: 'pubdate',
    },
    {
        title: '阅读数',
        dataIndex: 'read_count',
        key: 'read_count',
    },
    {
        title: '评论数',
        dataIndex: 'comment_count',
        key: 'comment_count',
    },
    {
        title: '点赞数',
        dataIndex: 'like_count',
        key: 'like_count',
    },
    {
        title: '操作',
        key: 'id',
        render() {
            return (
                <Space>
                    <Button type='primary' shape='circle' icon={<EditOutlined />}></Button>
                    <Button type='primary' shape='circle' danger icon={<DeleteOutlined />}></Button>
                </Space>
            )
        },
    },
]

export default function Article() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    const articles = useSelector((state) => state.article.articles)
    useEffect(() => {
        dispatch(getChannelList())
        dispatch(getArticleList())
    }, [dispatch])
    const onFinish = (values) => {
        const params = {}
        if (values.status !== -1) {
            params.status = values.status
        }
        if (values.channel_id) {
            params.channel_id = values.channel_id
        }
        if (values.date) {
            params.begin_pubdate = values.date[0].startOf('day').format('YYYY-MM-DD HH:mm:ss')
            params.end_pubdate = values.date[1].endOf('day').format('YYYY-MM-DD HH:mm:ss')
        }
        dispatch(getArticleList(params))
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form
                    initialValues={{
                        status: -1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            {STATUS.map((item) => (
                                <Radio key={item.id} value={item.id}>
                                    {item.title}
                                </Radio>
                            ))}
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary' htmlType='submit'>
                            筛选
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
            <Card title={`根据筛选条件共查询到 ${articles.total_count} 条结果`} style={{ marginTop: 10 }}>
                <Table rowKey='id' dataSource={articles.results} columns={columns} />
            </Card>
        </div>
    )
}
```

## 表格分页功能

### 基本效果

```jsx
<Table
    rowKey={(record) => record.id}
    dataSource={articles.results}
    columns={columns}
    pagination={{
        position: ['bottomCenter'],
        total: articles.total_count,
        pageSize: articles.per_page,
        current: articles.page,
    }}
/>
```

### useRef

📝 点击分页的时候需要依赖 params 中的数据发起请求。

`pages/Article/index.js`

```jsx
import { useEffect, useRef } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker, Table, Tag, Image, Space } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import { EditOutlined, DeleteOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import { getChannelList, getArticleList } from '@/store/actions/article'
import img from '@/assets/error.png'
const { RangePicker } = DatePicker

const STATUS = [
    { id: -1, title: '全部', color: 'magenta' },
    { id: 0, title: '草稿', color: 'red' },
    { id: 1, title: '待审核', color: 'volcano' },
    { id: 2, title: '审核通过', color: 'lime' },
    { id: 3, title: '审核失败', color: 'gold' },
]

const columns = [
    {
        title: '封面',
        dataIndex: 'cover',
        key: 'cover',
        render(cover) {
            if (cover.type === 0) {
                return <Image width={200} height={150} src={img} />
            } else {
                return <Image width={200} height={150} src={cover.images[0]} fallback={img} />
            }
        },
    },
    {
        title: '标题',
        dataIndex: 'title',
        key: 'title',
    },
    {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
        render: (status) => {
            const obj = STATUS.find((item) => item.id === status)
            return <Tag color={obj.color}>{obj.title}</Tag>
        },
    },
    {
        title: '发布时间',
        dataIndex: 'pubdate',
        key: 'pubdate',
    },
    {
        title: '阅读数',
        dataIndex: 'read_count',
        key: 'read_count',
    },
    {
        title: '评论数',
        dataIndex: 'comment_count',
        key: 'comment_count',
    },
    {
        title: '点赞数',
        dataIndex: 'like_count',
        key: 'like_count',
    },
    {
        title: '操作',
        key: 'id',
        render() {
            return (
                <Space>
                    <Button type='primary' shape='circle' icon={<EditOutlined />}></Button>
                    <Button type='primary' shape='circle' danger icon={<DeleteOutlined />}></Button>
                </Space>
            )
        },
    },
]

export default function Article() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    const articles = useSelector((state) => state.article.articles)
    // #1
    const params = useRef({})
    useEffect(() => {
        dispatch(getChannelList())
        dispatch(getArticleList())
    }, [dispatch])
    const onFinish = (values) => {
        // #2
        // const params = {}
        // #3 把 params 都换成 params.current
        if (values.status !== -1) {
            params.current.status = values.status
        }
        if (values.channel_id) {
            params.current.channel_id = values.channel_id
        }
        if (values.date) {
            params.current.begin_pubdate = values.date[0].startOf('day').format('YYYY-MM-DD HH:mm:ss')
            params.current.end_pubdate = values.date[1].endOf('day').format('YYYY-MM-DD HH:mm:ss')
        }
        // #5 从第一页开始筛选
        params.current.page = 1
        dispatch(getArticleList(params.current))
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form
                    initialValues={{
                        status: -1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            {STATUS.map((item) => (
                                <Radio key={item.id} value={item.id}>
                                    {item.title}
                                </Radio>
                            ))}
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary' htmlType='submit'>
                            筛选
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
            <Card title={`根据筛选条件共查询到 ${articles.total_count} 条结果`} style={{ marginTop: 10 }}>
                <Table
                    rowKey='id'
                    dataSource={articles.results}
                    columns={columns}
                    pagination={{
                        position: ['bottomCenter'],
                        total: articles.total_count,
                        pageSize: articles.per_page,
                        current: articles.page,
                        onChange(page, pageSize) {
                            // #4
                            params.current.page = page
                            params.current.per_page = pageSize
                            dispatch(getArticleList(params.current))
                        },
                    }}
                />
            </Card>
        </div>
    )
}
```

### 总结

useRef 的另一个作用：多次渲染之间进行数据共享。

## 删除文章

1. 准备弹框。

```js
const columns = [
    {
        title: '操作',
        key: 'id',
        render() {
            return (
                <Space>
                    <Button type='primary' shape='circle' icon={<EditOutlined />}></Button>
                    <Popconfirm title='确定要删除该文章吗？'>
                        <Button type='primary' shape='circle' danger icon={<DeleteOutlined />}></Button>
                    </Popconfirm>
                </Space>
            )
        },
    },
]
```

2. `store/actions/article.js`，提供一个删除的 action。

```jsx
export const delArticle = (id) => {
    return async () => {
        await request({
            method: 'delete',
            url: `/mp/articles/${id}`,
        })
    }
}
```

3. 在组件中提供 del 方法，通过 dispatch action 进行删除的操作。

`pages/Article/index.js`，由于需要在 column 内部用到 Hook，所以把 column 迁移到了函数内部定义。

```jsx
import { useEffect, useRef } from 'react'
import { Card, Breadcrumb, Form, Button, Radio, Select, DatePicker, Table, Tag, Image, Space, Popconfirm, message } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import { EditOutlined, DeleteOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import { getChannelList, getArticleList, delArticle } from '@/store/actions/article'
import img from '@/assets/error.png'
const { RangePicker } = DatePicker

const STATUS = [
    { id: -1, title: '全部', color: 'magenta' },
    { id: 0, title: '草稿', color: 'red' },
    { id: 1, title: '待审核', color: 'volcano' },
    { id: 2, title: '审核通过', color: 'lime' },
    { id: 3, title: '审核失败', color: 'gold' },
]

export default function Article() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    const articles = useSelector((state) => state.article.articles)
    const params = useRef({})
    useEffect(() => {
        dispatch(getChannelList())
        dispatch(getArticleList())
    }, [dispatch])
    const onFinish = (values) => {
        if (values.status !== -1) {
            params.current.status = values.status
        }
        if (values.channel_id) {
            params.current.channel_id = values.channel_id
        }
        if (values.date) {
            params.current.begin_pubdate = values.date[0].startOf('day').format('YYYY-MM-DD HH:mm:ss')
            params.current.end_pubdate = values.date[1].endOf('day').format('YYYY-MM-DD HH:mm:ss')
        }
        params.current.page = 1
        dispatch(getArticleList(params.current))
    }
    // #2
    const del = async (id) => {
        await dispatch(delArticle(id))
        await dispatch(getArticleList(params.current))
        message.success('删除成功')
    }
    const columns = [
        {
            title: '封面',
            dataIndex: 'cover',
            key: 'cover',
            render(cover) {
                if (cover.type === 0) {
                    return <Image width={200} height={150} src={img} />
                } else {
                    return <Image width={200} height={150} src={cover.images[0]} fallback={img} />
                }
            },
        },
        {
            title: '标题',
            dataIndex: 'title',
            key: 'title',
        },
        {
            title: '状态',
            dataIndex: 'status',
            key: 'status',
            render: (status) => {
                const obj = STATUS.find((item) => item.id === status)
                return <Tag color={obj.color}>{obj.title}</Tag>
            },
        },
        {
            title: '发布时间',
            dataIndex: 'pubdate',
            key: 'pubdate',
        },
        {
            title: '阅读数',
            dataIndex: 'read_count',
            key: 'read_count',
        },
        {
            title: '评论数',
            dataIndex: 'comment_count',
            key: 'comment_count',
        },
        {
            title: '点赞数',
            dataIndex: 'like_count',
            key: 'like_count',
        },
        {
            title: '操作',
            dataIndex: 'id', // #3 注意这里添加了 dataIndex 为 id，才能保证 render 中的参数是 id
            key: 'id',
            render(id) {
                return (
                    <Space>
                        <Button type='primary' shape='circle' icon={<EditOutlined />}></Button>
                        {/* #1 */}
                        <Popconfirm title='确定要删除该文章吗？' onConfirm={() => del(id)}>
                            <Button type='primary' shape='circle' danger icon={<DeleteOutlined />}></Button>
                        </Popconfirm>
                    </Space>
                )
            },
        },
    ]
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>内容管理</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form
                    initialValues={{
                        status: -1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='状态' name='status'>
                        <Radio.Group>
                            {STATUS.map((item) => (
                                <Radio key={item.id} value={item.id}>
                                    {item.title}
                                </Radio>
                            ))}
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} placeholder='请选择频道'>
                            {channels.map((item) => (
                                <Select.Option key={item.id} value={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                    <Form.Item label='日期' name='date'>
                        <RangePicker />
                    </Form.Item>
                    <Form.Item>
                        <Button type='primary' htmlType='submit'>
                            筛选
                        </Button>
                    </Form.Item>
                </Form>
            </Card>
            <Card title={`根据筛选条件共查询到 ${articles.total_count} 条结果`} style={{ marginTop: 10 }}>
                <Table
                    rowKey='id'
                    dataSource={articles.results}
                    columns={columns}
                    pagination={{
                        position: ['bottomCenter'],
                        total: articles.total_count,
                        pageSize: articles.per_page,
                        current: articles.page,
                        onChange(page, pageSize) {
                            params.current.page = page
                            params.current.per_page = pageSize
                            dispatch(getArticleList(params.current))
                        },
                    }}
                />
            </Card>
        </div>
    )
}
```

若删除的时候控制台出现了警告，把 antd@4.17.3 改成 antd@4.16.3 试试。

## 发布文章

### 基本结构

1. 外部卡片。

```js
import React from 'react'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
import { Card, Breadcrumb } from 'antd'

export default function Publish() {
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb separator='>'>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>发布文章</Breadcrumb.Item>
                    </Breadcrumb>
                }
            ></Card>
        </div>
    )
}
```

2. 标题。

```js
<Form labelCol={{ span: 4 }} wrapperCol={{ span: 20 }} size='large'>
    <Form.Item label='标题'>
        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
    </Form.Item>
</Form>
```

3. 获取频道数据并渲染。

```js
import { useEffect } from 'react'
import { Form, Input, Card, Breadcrumb, Select } from 'antd'
import { useDispatch, useSelector } from 'react-redux'
import { Link } from 'react-router-dom'
import styles from './index.module.scss'
import { getChannelList } from '@/store/actions/article'

export default function Publish() {
    const dispatch = useDispatch()
    // #2
    const channels = useSelector((state) => state.article.channels)
    useEffect(() => {
        // #1
        dispatch(getChannelList())
    }, [dispatch])
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb separator='>'>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>发布文章</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form labelCol={{ span: 4 }} wrapperCol={{ span: 20 }} size='large'>
                    {/* 标题 */}
                    <Form.Item label='标题'>
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    {/* 频道 */}
                    <Form.Item label='频道' name='channel_id'>
                        <Select style={{ width: 200 }} allowClear placeholder='请选择频道'>
                            {/* #3 */}
                            {channels.map((item) => (
                                <Select.Option value={item.id} key={item.id}>
                                    {item.name}
                                </Select.Option>
                            ))}
                        </Select>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

4. 封面、内容和按钮。

```jsx
<Form.Item label='封面'></Form.Item>
<Form.Item label='内容'></Form.Item>
<Form.Item wrapperCol={{ offset: 4, span: 20 }}>
    <Space>
        <Button type='primary'>发布文章</Button>
        <Button>存入草稿</Button>
    </Space>
</Form.Item>
```

### 优化频道数据

#### 自定义 Hook

目标：通过自定义 Hook，抽离频道的状态和业务逻辑。

`hooks/index.js`

```js
import { getChannelList } from '@/store/actions/article'
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
/**
 * 获取频道列表
 * @returns array
 */
export function useChannels() {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    return channels
}
```

`pages/Publish/index.js`

```js
import { useChannels } from '@/hooks'
export default function Publish() {
    const channels = useChannels()
}
```

#### 自定义组件

目标：抽离组件的状态、业务逻辑、UI。

1. `src/components/Channel/index.js`

```js
import { useEffect } from 'react'
import { Select } from 'antd'
import { getChannelList } from '@/store/actions/article'
import { useDispatch, useSelector } from 'react-redux'

// 注意：当外部的 Form 组件提供了 initialValues 属性并提供了初始值，这里的 value 属性也会有值
// export default function Channel({ value, onChange }) {
export default function Channel(props) {
    const dispatch = useDispatch()
    const channels = useSelector((state) => state.article.channels)
    useEffect(() => {
        dispatch(getChannelList())
    }, [dispatch])
    return (
        <Select placeholder='请选择频道' style={{ width: 200 }} {...props}>
            {channels.map((item) => (
                <Select.Option key={item.id} value={item.id}>
                    {item.name}
                </Select.Option>
            ))}
        </Select>
    )
}
```

2. `pages/Publish/index.js`

```js
<Form.Item label='频道' name='channel_id'>
    <Channel />
</Form.Item>
```

### 富文本编辑器

#### 步骤

1. 安装富文本编辑器：`yarn add react-quill`。

2. 导入富文本编辑器组件以及样式文件。

3. 渲染富文本编辑器组件。

4. 通过 Form 组件的 initialValues 为富文本编辑器设置初始值，否则默认的 value 是 undefined 会报错。

5. 调整富文本编辑器的样式。

#### 代码

`pages/Publish/index.js`

```js
import { Form, Input, Card, Breadcrumb, Space, Button } from 'antd'
import { Link } from 'react-router-dom'
// !#5 限制了富文本的高度
import styles from './index.module.scss'
import Channel from '@/components/Channel'
// !#1 引入组件和样式
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'

export default function Publish() {
    const onFinish = (values) => {
        // !#6 测试能不能收集到数据
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb separator='>'>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>发布文章</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                {/* //!#4 指定 content 初始值为空，负责默认是 undefined 会报错 */}
                <Form
                    labelCol={{ span: 4 }}
                    wrapperCol={{ span: 20 }}
                    size='large'
                    initialValues={{
                        content: '',
                    }}
                    onFinish={onFinish}
                >
                    {/* //!#8 根据接口文档的要求，这里需要指定 name 为 title */}
                    <Form.Item label='标题' name='title'>
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Channel />
                    </Form.Item>
                    <Form.Item label='封面'></Form.Item>
                    {/* //!#3 提供 name 属性 */}
                    <Form.Item label='内容' name='content'>
                        {/* //!#2 使用组件 */}
                        <ReactQuill />
                    </Form.Item>
                    <Form.Item wrapperCol={{ offset: 4, span: 20 }}>
                        <Space>
                            {/* //!#7 记得指定 htmlType，否则无法触发 onFinish 事件 */}
                            <Button type='primary' htmlType='submit'>
                                发布文章
                            </Button>
                            <Button>存入草稿</Button>
                        </Space>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

`pages/Publish/index.module.scss`

```scss
.root {
    :global {
        .ql-editor {
            height: 300px;
        }
    }
}
```

### 封面的单选按钮

```js
<Form.Item label='封面' name='type'>
    <Radio.Group>
        <Radio value={1}>单图</Radio>
        <Radio value={3}>三图</Radio>
        <Radio value={0}>无图</Radio>
    </Radio.Group>
</Form.Item>
```

### 封面的上传组件

1. 在新的 Form.Item 中使用 Upload 组件。

```js
<Form.Item>
    <Upload />
</Form.Item>
```

2. 添加上传图标。

```js
<Form.Item>
    <Upload>
        <PlusOutlined />
    </Upload>
</Form.Item>
```

3. 给 Upload 组件指定 listType 属性。

```js
<Form.Item>
    <Upload listType='picture-card'>
        <PlusOutlined />
    </Upload>
</Form.Item>
```

4. 给 Upload 组件设置 fileList 属性用于收集数据。

```js
const [fileList] = useState([
    {
        url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
    },
])
```

```js
<Form.Item>
    <Upload listType='picture-card' fileList={fileList}>
        <PlusOutlined />
    </Upload>
</Form.Item>
```

5. 设置 Form.Item 的偏移量。

```js
<Form.Item wrapperCol={{ offset: 4 }}>
    <Upload listType='picture-card' fileList={fileList}>
        <PlusOutlined />
    </Upload>
</Form.Item>
```

6. 完整代码

```js
import { useState } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload } from 'antd'
import { Link } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'

export default function Publish() {
    const [fileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const onFinish = (values) => {
        console.log(values)
    }
    return (
        <div className={styles.root}>
            <Card
                title={
                    <Breadcrumb separator='>'>
                        <Breadcrumb.Item>
                            <Link to='/home'>首页</Link>
                        </Breadcrumb.Item>
                        <Breadcrumb.Item>发布文章</Breadcrumb.Item>
                    </Breadcrumb>
                }
            >
                <Form
                    labelCol={{ span: 4 }}
                    wrapperCol={{ span: 20 }}
                    size='large'
                    initialValues={{
                        content: '',
                        type: 1,
                    }}
                    onFinish={onFinish}
                >
                    <Form.Item label='标题' name='title'>
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    <Form.Item label='频道' name='channel_id'>
                        <Channel />
                    </Form.Item>
                    <Form.Item label='封面' name='type'>
                        <Radio.Group>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    {/* //!#mark */}
                    <Form.Item wrapperCol={{ offset: 4 }}>
                        <Upload listType='picture-card' fileList={fileList}>
                            <PlusOutlined />
                        </Upload>
                    </Form.Item>
                    <Form.Item label='内容' name='content'>
                        <ReactQuill />
                    </Form.Item>
                    <Form.Item wrapperCol={{ offset: 4, span: 20 }}>
                        <Space>
                            <Button type='primary' htmlType='submit'>
                                发布文章
                            </Button>
                            <Button>存入草稿</Button>
                        </Space>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```
