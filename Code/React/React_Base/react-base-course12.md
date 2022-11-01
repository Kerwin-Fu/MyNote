---
title: 12_极客园 PC
date: 2021-11-25 14:12:22
tags:
---

## 今日目标

✔ 掌握发布和编辑文章的流程。

✔ 掌握项目打包。

<!-- more -->

## 上传图片并展示

### 目标

能够实现图片上传。

### 步骤

0. 根目录新建 `.env.development` 和 `.env.production` 文件，指定基准地址方便其他地方读取。

```bash
REACT_APP_URL = 'http://geek.itheima.net/v1_0/'
```

1. 为 Upload 组件添加 action 属性和 name，指定封面图片上传接口地址。

2. 创建状态 fileList 存储已上传封面图片地址，并设置为 Upload 组件的 fileList 属性值。

3. 为 Upload 添加 onChange 属性，监听封面图片上传、删除等操作。

4. 在 change 事件中拿到当前图片数据，并存储到状态 fileList 中。

### 代码

`pages/Punlish/index.js`

```jsx
import { useState } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload } from 'antd'
import { Link } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'

export default function Publish() {
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const onFinish = (values) => {
        console.log(values)
    }
    const onChange = ({ fileList }) => {
        // !#2
        setFileList(fileList)
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
                    {/* //!#1 指定 action、name、onChange */}
                    <Form.Item wrapperCol={{ offset: 4 }}>
                        <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange}>
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

## 上传图片数量和加号控制

### 目标

能够控制上传图片数量和加号的显示逻辑。

### 内容

🤔 利用 Upload 组件的 maxCount 属性来控制数量，可以自己定义 type 属性，把 type 指定为 maxCount 的值。

`pages/Publish/index.js`

1. 在 Radio.Group 组件监听 onChagne 事件。

```jsx
<Form.Item label='封面' name='type'>
    <Radio.Group onChange={onTypeChange}>
        <Radio value={1}>单图</Radio>
        <Radio value={3}>三图</Radio>
        <Radio value={0}>无图</Radio>
    </Radio.Group>
</Form.Item>
```

2. 提供 value 值和 onChange 事件对应的回调。

```jsx
const [type, setType] = useState(1)
const onTypeChange = (e) => {
    setType(e.target.value)
}
```

3. 指定 Upload 组件的 maxCount 属性对应的值为 type。

```jsx
<Upload maxCount={type} />
```

4. 加号控制（无图时却还能继续上传，是因为当值为 0 时 maxCount 不支持，可以通过下面的方式解决此问题）。

```jsx
<Upload maxCount={type}>{fileList.length < type && <PlusOutlined />}</Upload>
```

5. 完整代码。

```jsx
import { useState } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload } from 'antd'
import { Link } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'

export default function Publish() {
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const [type, setType] = useState(1)
    const onFinish = (values) => {
        console.log(values)
    }
    const onChange = ({ fileList }) => {
        setFileList(fileList)
    }
    const onTypeChange = (e) => {
        setType(e.target.value)
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
                        type,
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
                        <Radio.Group onChange={onTypeChange}>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    <Form.Item wrapperCol={{ offset: 4 }}>
                        <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange} maxCount={type}>
                            {fileList.length < type && <PlusOutlined />}
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

## 数量切换控制

### 目标

能够实现动态切换封面数量。

### 问题

问题：如果当前有 3 张图，选择单图只显示一张，再切换到三图继续显示三张，该如何实现？

-   3 张 -> 1 张

-   1 张 -> 3 张

-   思路：通过 ref 存储所有图片，需要几张就展示几张，也就是把 ref 当仓库，用多少拿多少。

### 步骤

1. 创建 ref 对象，用来存储已上传图片。

2. onChange 的时候存储当前的 fileList 到 ref 对象。

3. onTypeChange 的时候从 ref 对象中截取需要的部分。

### 代码

`pages/Publish/index.js`

```js
import { useState, useRef } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload } from 'antd'
import { Link } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'

export default function Publish() {
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const [type, setType] = useState(1)
    // !#1
    const fileRef = useRef(fileList)
    const onFinish = (values) => {
        console.log(values)
    }
    const onChange = ({ fileList }) => {
        setFileList(fileList)
        // !#2 每次上传完之后的最终结果都存一下
        fileRef.current = fileList
    }
    const onTypeChange = (e) => {
        const count = e.target.value
        // 数量
        setType(count)
        // !#3
        // 从最终的结果里面截取需要的部分
        setFileList(fileRef.current.slice(0, count))
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
                        type,
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
                        <Radio.Group onChange={onTypeChange}>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    {/* //!#4 */}
                    {type > 0 && (
                        <Form.Item wrapperCol={{ offset: 4 }}>
                            <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange} maxCount={type}>
                                {fileList.length < type && <PlusOutlined />}
                            </Upload>
                        </Form.Item>
                    )}
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

## 基础校验功能

-   期望 onBlur 时也能触发校验。

```js
<Form validateTrigger={['onBlur', 'onChange']} />
```

-   标题非空校验。

```js
<Form.Item
    label='标题'
    name='title'
    rules={[
        {
            required: true,
            message: '标题不能为空',
        },
    ]}
>
    <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
</Form.Item>
```

-   频道非空校验。

```jsx
<Form.Item
    label='频道'
    name='channel_id'
    rules={[
        {
            required: true,
            message: '频道不能为空',
        },
    ]}
>
    <Channel />
</Form.Item>
```

-   内容非空校验。

```js
<Form.Item
    label='内容'
    name='content'
    rules={[
        {
            required: true,
            message: '内容不能为空',
        },
    ]}
>
    <ReactQuill />
</Form.Item>
```

## 封面校验功能

-   点击封面对应的单选按钮时（注意点击时会对 fileList 有一个截取的操作），fileList 的长度应该和当前选中的数值一致。

```jsx
<Form.Item
    label='封面'
    name='type'
    rules={[
        {
            validator(_, value) {
                // value => 表示选中的数量
                if (fileList.length !== value) {
                    return Promise.reject(new Error(`请上传${value}张图片`))
                } else {
                    return Promise.resolve()
                }
            },
        },
    ]}
>
    <Radio.Group onChange={onTypeChange}>
        <Radio value={1}>单图</Radio>
        <Radio value={3}>三图</Radio>
        <Radio value={0}>无图</Radio>
    </Radio.Group>
</Form.Item>
```

-   需求：期望上传或删除图片时也会触发校验（之所以没有触发是因为上传或删除图片的操作并没有修改 type 属性）。

-   方法：获取到表单实例，调用 validateFields 方法。

```jsx
import { useState, useRef } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload } from 'antd'
import { Link } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'

export default function Publish() {
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const [type, setType] = useState(1)
    const fileRef = useRef(fileList)
    // !#1
    const formRef = useRef(null)
    const onFinish = (values) => {
        console.log(values)
    }
    const onChange = ({ fileList }) => {
        setFileList(fileList)
        fileRef.current = fileList
        // !#3
        formRef.current.validateFields(['type'])
    }
    const onTypeChange = (e) => {
        const count = e.target.value
        // 数量
        setType(count)
        // 从最终的结果里面截取需要的部分
        setFileList(fileRef.current.slice(0, count))
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
                {/* //!#2 */}
                <Form
                    labelCol={{ span: 4 }}
                    wrapperCol={{ span: 20 }}
                    size='large'
                    initialValues={{
                        content: '',
                        type,
                    }}
                    onFinish={onFinish}
                    validateTrigger={['onBlur', 'onChange']}
                    ref={formRef}
                >
                    <Form.Item
                        label='标题'
                        name='title'
                        rules={[
                            {
                                required: true,
                                message: '标题不能为空',
                            },
                        ]}
                    >
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    <Form.Item
                        label='频道'
                        name='channel_id'
                        rules={[
                            {
                                required: true,
                                message: '频道不能为空',
                            },
                        ]}
                    >
                        <Channel />
                    </Form.Item>
                    <Form.Item
                        label='封面'
                        name='type'
                        rules={[
                            {
                                validator(_, value) {
                                    // value => 表示选中的数量
                                    if (fileList.length !== value) {
                                        return Promise.reject(new Error(`请上传${value}张图片`))
                                    } else {
                                        return Promise.resolve()
                                    }
                                },
                            },
                        ]}
                    >
                        <Radio.Group onChange={onTypeChange}>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    {type > 0 && (
                        <Form.Item wrapperCol={{ offset: 4 }}>
                            <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange} maxCount={type}>
                                {fileList.length < type && <PlusOutlined />}
                            </Upload>
                        </Form.Item>
                    )}
                    <Form.Item
                        label='内容'
                        name='content'
                        rules={[
                            {
                                required: true,
                                message: '内容不能为空',
                            },
                        ]}
                    >
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

## 发布文章功能

1. 把表单数据整合成后端需要的格式。

```js
const onFinish = (values) => {
    const images = fileList.map((item) => {
        // 编辑的时候后端返回的是有 url 的
        if (item.url) {
            return item.url
        } else {
            // 自己手动上传的 url 要通过下面的方式获取
            return item.response.data.url
        }
    })
    const data = {
        ...values,
        cover: {
            type,
            images,
        },
    }
    console.log(data)
}
```

2. 提交数据到后端。

`store/actions/article.js`

```js
export const addArticle = (data) => {
    return async () => {
        await request.post('/mp/articles', data)
    }
}
```

`pages/Publish/index.js`

```js
const onFinish = async (values) => {
    const images = fileList.map((item) => {
        // 编辑的时候后端返回的是有 url 的
        if (item.url) {
            return item.url
        } else {
            // 自己手动上传的 url 要通过下面的方式获取
            return item.response.data.url
        }
    })
    const data = {
        ...values,
        cover: {
            type,
            images,
        },
    }
    await dispatch(addArticle(data))
    message.success('添加文章成功')
    history.push('/home/article')
}
```

3. 完整代码。

```js
import { useState, useRef } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload, message } from 'antd'
import { Link, useHistory } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import { useDispatch } from 'react-redux'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'
import { addArticle } from '@/store/actions/article'

export default function Publish() {
    const dispatch = useDispatch()
    const history = useHistory()
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const [type, setType] = useState(1)
    const fileRef = useRef(fileList)
    const formRef = useRef(null)
    const onFinish = async (values) => {
        const images = fileList.map((item) => {
            // 编辑的时候后端返回的是有 url 的
            if (item.url) {
                return item.url
            } else {
                // 自己手动上传的 url 要通过下面的方式获取
                return item.response.data.url
            }
        })
        const data = {
            ...values,
            cover: {
                type,
                images,
            },
        }
        await dispatch(addArticle(data))
        message.success('添加文章成功')
        history.push('/home/article')
    }
    const onChange = ({ fileList }) => {
        setFileList(fileList)
        fileRef.current = fileList
        formRef.current.validateFields(['type'])
    }
    const onTypeChange = (e) => {
        const count = e.target.value
        // 数量
        setType(count)
        // 从最终的结果里面截取需要的部分
        setFileList(fileRef.current.slice(0, count))
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
                        type,
                    }}
                    onFinish={onFinish}
                    validateTrigger={['onBlur', 'onChange']}
                    ref={formRef}
                >
                    <Form.Item
                        label='标题'
                        name='title'
                        rules={[
                            {
                                required: true,
                                message: '标题不能为空',
                            },
                        ]}
                    >
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    <Form.Item
                        label='频道'
                        name='channel_id'
                        rules={[
                            {
                                required: true,
                                message: '频道不能为空',
                            },
                        ]}
                    >
                        <Channel />
                    </Form.Item>
                    <Form.Item
                        label='封面'
                        name='type'
                        rules={[
                            {
                                validator(_, value) {
                                    // value => 表示选中的数量
                                    if (fileList.length !== value) {
                                        return Promise.reject(new Error(`请上传${value}张图片`))
                                    } else {
                                        return Promise.resolve()
                                    }
                                },
                            },
                        ]}
                    >
                        <Radio.Group onChange={onTypeChange}>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    {type > 0 && (
                        <Form.Item wrapperCol={{ offset: 4 }}>
                            <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange} maxCount={type}>
                                {fileList.length < type && <PlusOutlined />}
                            </Upload>
                        </Form.Item>
                    )}
                    <Form.Item
                        label='内容'
                        name='content'
                        rules={[
                            {
                                required: true,
                                message: '内容不能为空',
                            },
                        ]}
                    >
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

## 存入草稿功能

### 分析

🤔 如果要在非提交按钮中获取到表单数据，需要通过调用 Form 的实例方法来实现。

1. `getFieldsValue()` 仅获取表单数据，不进行表单校验。

2. `validateFields()` 先进行表单校验，再获取表单数据【此处，使用该方法】。

3. 存入草稿功能类似于发布文章，只是 draft 值为 true，所以可以复用发布文章的逻辑。

### 步骤

1. 通过 `Form.useForm()` 创建表单的控制实例。

2. 将创建好的实例，设置为 Form 的 form 属性值。

3. 为存入草稿按钮绑定点击事件。

4. 在事件中，调用表单的 validateFields 方法，先进行表单校验再获取表单值。

### 代码

1. 改造接口函数。

`store/actions/article.js`

```js
export const addArticle = (draft = false, data) => {
    return async () => {
        // await request.post('/mp/articles', data)
        await request({
            url: '/mp/articles',
            method: 'post',
            data,
            params: {
                draft,
            },
        })
    }
}
```

2. 给草稿注册点击事件。

```jsx
<Button onClick={addDraft}>存入草稿</Button>
```

2. 对表单进行校验，且获取到表单的值。

```jsx
const addDraft = async () => {
    // const [form] = Form.useForm() // 除了 ref 的方式，组件内置的这种写法也可以拿到 form 实例
    const values = await formRef.current.validateFields()
    add(true, values)
}
```

3. 封装了添加文章的方法。

```jsx
const add = async (draft, values) => {
    const images = fileList.map((item) => {
        if (item.url) {
            return item.url
        } else {
            return item.response.data.url
        }
    })
    const data = {
        ...values,
        cover: {
            type: type,
            images: images,
        },
    }
    // #mark，注意这儿 addArticle 要是 2 个参数
    await dispatch(addArticle(draft, data))
    message.success('添加文章成功')
    history.push('/home/article')
}
const onFinish = async (values) => {
    add(false, values)
}
const addDraft = async () => {
    const values = await formRef.current.validateFields()
    add(true, values)
}
```

4. 完整代码。

```js
import { useState, useRef } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload, message } from 'antd'
import { Link, useHistory } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import { useDispatch } from 'react-redux'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'
import { addArticle } from '@/store/actions/article'

export default function Publish() {
    const dispatch = useDispatch()
    const history = useHistory()
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const [type, setType] = useState(1)
    const fileRef = useRef(fileList)
    const formRef = useRef(null)
    // #1
    const add = async (draft, values) => {
        const images = fileList.map((item) => {
            if (item.url) {
                return item.url
            } else {
                return item.response.data.url
            }
        })
        const data = {
            ...values,
            cover: {
                type: type,
                images: images,
            },
        }
        await dispatch(addArticle(draft, data))
        message.success('添加文章成功')
        history.push('/home/article')
    }
    // #2
    const onFinish = async (values) => {
        add(false, values)
    }
    // #3
    const addDraft = async () => {
        const values = await formRef.current.validateFields()
        add(true, values)
    }
    const onChange = ({ fileList }) => {
        setFileList(fileList)
        fileRef.current = fileList
        formRef.current.validateFields(['type'])
    }
    const onTypeChange = (e) => {
        const count = e.target.value
        // 数量
        setType(count)
        // 从最终的结果里面截取需要的部分
        setFileList(fileRef.current.slice(0, count))
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
                        type,
                    }}
                    onFinish={onFinish}
                    validateTrigger={['onBlur', 'onChange']}
                    ref={formRef}
                >
                    <Form.Item
                        label='标题'
                        name='title'
                        rules={[
                            {
                                required: true,
                                message: '标题不能为空',
                            },
                        ]}
                    >
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    <Form.Item
                        label='频道'
                        name='channel_id'
                        rules={[
                            {
                                required: true,
                                message: '频道不能为空',
                            },
                        ]}
                    >
                        <Channel />
                    </Form.Item>
                    <Form.Item
                        label='封面'
                        name='type'
                        rules={[
                            {
                                validator(_, value) {
                                    // value => 表示选中的数量
                                    if (fileList.length !== value) {
                                        return Promise.reject(new Error(`请上传${value}张图片`))
                                    } else {
                                        return Promise.resolve()
                                    }
                                },
                            },
                        ]}
                    >
                        <Radio.Group onChange={onTypeChange}>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    {type > 0 && (
                        <Form.Item wrapperCol={{ offset: 4 }}>
                            <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange} maxCount={type}>
                                {fileList.length < type && <PlusOutlined />}
                            </Upload>
                        </Form.Item>
                    )}
                    <Form.Item
                        label='内容'
                        name='content'
                        rules={[
                            {
                                required: true,
                                message: '内容不能为空',
                            },
                        ]}
                    >
                        <ReactQuill />
                    </Form.Item>
                    <Form.Item wrapperCol={{ offset: 4, span: 20 }}>
                        <Space>
                            <Button type='primary' htmlType='submit'>
                                发布文章
                            </Button>
                            <Button onClick={addDraft}>存入草稿</Button>
                        </Space>
                    </Form.Item>
                </Form>
            </Card>
        </div>
    )
}
```

## 图片预览功能

### 控制弹框显示隐藏

1. 准备一个 Modal，用于展示图片。

```jsx
<Modal width={800} title='图片预览'>
    <div style={{ textAlign: 'center' }}>xx</div>
</Modal>
```

2. 给 Upload 组件提供 onPreview 事件，控制 Modal 的显示/隐藏。

```js
const [isVisible, setIsVisible] = useState(false)
const onPreview = () => {
    setIsVisible(true)
}
```

```js
<Modal width={800} title='图片预览' visible={isVisible}>
    <div style={{ textAlign: 'center' }}>xx</div>
</Modal>
```

### 控制 Modal 中图片的展示

```jsx
const [preview, setPreview] = useState('')
const onPreview = (file) => {
    setIsVisible(true)
    setPreview(file.url || file.response.data.url)
}
```

```js
<Modal width={800} title='图片预览' visible={isVisible}>
    <div style={{ textAlign: 'center' }}>
        <img src={preview} style={{ width: 600 }} alt='' />
    </div>
</Modal>
```

### 关闭弹框功能

不展示底部按钮，绑定 onCancel 事件关闭弹框。

```js
<Modal width={800} title='图片预览' visible={isVisible} footer={null} onCancel={() => setIsVisible(false)}>
    <div style={{ textAlign: 'center' }}>
        <img src={preview} style={{ width: 600 }} alt='' />
    </div>
</Modal>
```

### 完整代码

```js
import { useState, useRef } from 'react'
import { Form, Input, Card, Breadcrumb, Space, Button, Radio, Upload, message, Modal } from 'antd'
import { Link, useHistory } from 'react-router-dom'
import { PlusOutlined } from '@ant-design/icons'
import { useDispatch } from 'react-redux'
import styles from './index.module.scss'
import Channel from '@/components/Channel'
import ReactQuill from 'react-quill'
import 'react-quill/dist/quill.snow.css'
import { addArticle } from '@/store/actions/article'

export default function Publish() {
    const dispatch = useDispatch()
    const history = useHistory()
    const [fileList, setFileList] = useState([
        {
            url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
        },
    ])
    const [type, setType] = useState(1)
    const [isVisible, setIsVisible] = useState(false)
    const [preview, setPreview] = useState(false)
    const fileRef = useRef(fileList)
    const formRef = useRef(null)
    const add = async (draft, values) => {
        const images = fileList.map((item) => {
            if (item.url) {
                return item.url
            } else {
                return item.response.data.url
            }
        })
        const data = {
            ...values,
            cover: {
                type: type,
                images: images,
            },
        }
        await dispatch(addArticle(draft, data))
        message.success('添加文章成功')
        history.push('/home/article')
    }
    const onFinish = async (values) => {
        add(false, values)
    }
    const addDraft = async () => {
        const values = await formRef.current.validateFields()
        add(true, values)
    }
    const onChange = ({ fileList }) => {
        setFileList(fileList)
        fileRef.current = fileList
        formRef.current.validateFields(['type'])
    }
    const onTypeChange = (e) => {
        const count = e.target.value
        // 数量
        setType(count)
        // 从最终的结果里面截取需要的部分
        setFileList(fileRef.current.slice(0, count))
    }
    const onPreview = (file) => {
        setIsVisible(true)
        setPreview(file.url || file.response.data.url)
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
                        type,
                    }}
                    onFinish={onFinish}
                    validateTrigger={['onBlur', 'onChange']}
                    ref={formRef}
                >
                    <Form.Item
                        label='标题'
                        name='title'
                        rules={[
                            {
                                required: true,
                                message: '标题不能为空',
                            },
                        ]}
                    >
                        <Input placeholder='请输入文章的标题' style={{ width: 400 }} />
                    </Form.Item>
                    <Form.Item
                        label='频道'
                        name='channel_id'
                        rules={[
                            {
                                required: true,
                                message: '频道不能为空',
                            },
                        ]}
                    >
                        <Channel />
                    </Form.Item>
                    <Form.Item
                        label='封面'
                        name='type'
                        rules={[
                            {
                                validator(_, value) {
                                    // value => 表示选中的数量
                                    if (fileList.length !== value) {
                                        return Promise.reject(new Error(`请上传${value}张图片`))
                                    } else {
                                        return Promise.resolve()
                                    }
                                },
                            },
                        ]}
                    >
                        <Radio.Group onChange={onTypeChange}>
                            <Radio value={1}>单图</Radio>
                            <Radio value={3}>三图</Radio>
                            <Radio value={0}>无图</Radio>
                        </Radio.Group>
                    </Form.Item>
                    {type > 0 && (
                        <Form.Item wrapperCol={{ offset: 4 }}>
                            <Upload listType='picture-card' fileList={fileList} action={`${process.env.REACT_APP_URL}upload`} name='image' onChange={onChange} maxCount={type} onPreview={onPreview}>
                                {fileList.length < type && <PlusOutlined />}
                            </Upload>
                        </Form.Item>
                    )}
                    <Form.Item
                        label='内容'
                        name='content'
                        rules={[
                            {
                                required: true,
                                message: '内容不能为空',
                            },
                        ]}
                    >
                        <ReactQuill />
                    </Form.Item>
                    <Form.Item wrapperCol={{ offset: 4, span: 20 }}>
                        <Space>
                            <Button type='primary' htmlType='submit'>
                                发布文章
                            </Button>
                            <Button onClick={addDraft}>存入草稿</Button>
                        </Space>
                    </Form.Item>
                </Form>
            </Card>
            <Modal width={800} title='图片预览' visible={isVisible} footer={null} onCancel={() => setIsVisible(false)}>
                <div style={{ textAlign: 'center' }}>
                    <img src={preview} style={{ width: 600 }} alt='' />
                </div>
            </Modal>
        </div>
    )
}
```

## 编辑文章路由

目标：配置编辑文章的路由规则，根据有无 ID 展示面包屑和底部按钮的文案。

1. `pages/Layout/index.js`，准备路由出口。

```js
// 注意发布文章这儿加了 exact 严格匹配，否则此规则也会命中修改文章的路由
<Route exact path='/home/publish' component={Publish} />
<Route path='/home/publish/:id' component={Publish} />
```

2. `pages/Article/index.js`，路由入口处进行传参。

```jsx
<Button type='primary' shape='circle' icon={<EditOutlined />} onClick={() => history.push(`/home/publish/${id}`)}></Button>
```

3. `pages/Publish/index.js`，根据有没有 id 展示面包屑和底部按钮的文案。

```js
const { id } = useParams()
```

面包屑

```jsx
<Breadcrumb.Item>{id ? '编辑' : '发布'}文章</Breadcrumb.Item>
```

底部按钮

```jsx
<Button type='primary' htmlType='submit'>
    {id ? '编辑' : '发布'}文章
</Button>
```

## 填充文章数据

1. `actions/article.js`，封装请求数据的 action。

```js
export const getArticleInfo = (id) => {
    return async () => {
        const res = await request(`/mp/articles/${id}`)
        return res.data.data
    }
}
```

2. `pages/Publish/index.js`，请求数据。

```js
useEffect(() => {
    if (!id) return
    dispatch(getArticleInfo(id)).then((res) => {
        console.log(res)
    })
}, [id, dispatch])
```

3. `pages/Publish/index.js`，回显数据。

```js
useEffect(() => {
    if (!id) return
    dispatch(getArticleInfo(id)).then((res) => {
        formRef.current.setFieldsValue({
            ...res,
            type: res.cover.type, // !#1 封面单选按钮的回显
        })
        setType(res.cover.type) // !#2 控制 type 的正确数值
        // !#3 图片的回显
        const list = res.cover.images.map((item) => ({
            url: item,
        }))
        setFileList(list)
        // !#4 图片仓库的初始化，不然切换的时候会有问题
        fileRef.current = list
    })
}, [id, dispatch])
```

## 完成编辑功能

1. `actions/article.js`

```js
export const editArticle = (draft = false, data) => {
    return async (dispatch) => {
        await request({
            url: `/mp/articles/${data.id}`,
            method: 'put',
            data,
            params: {
                draft,
            },
        })
    }
}
```

2. `pages/Publish/index.js`

```js
const add = async (draft, values) => {
    const images = fileList.map((item) => {
        if (item.url) {
            return item.url
        } else {
            return item.response.data.url
        }
    })
    const data = {
        ...values,
        cover: {
            type: type,
            images: images,
        },
    }
    if (id) {
        // !修改
        await dispatch(
            editArticle(draft, {
                ...data,
                id,
            })
        )
    } else {
        // !新增
        await dispatch(addArticle(draft, data))
    }
    message.success('添加文章成功')
    history.push('/home/article')
}
```

## 侧边栏高亮

1. `pages/Layout/index.js`

```js
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
    const onConfirm = () => {
        dispatch(logout())
        history.push('/login')
        message.success('退出成功', 1)
    }
    // !#1
    let pathname = location.pathname
    if (pathname.startsWith('/home/publish')) {
        pathname = '/home/publish'
    }
    useEffect(() => dispatch(getUserInfo()), [dispatch])
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
                        {/* //!#2. location.pathname 改成 pathname 变量即可 */}
                        <Menu mode='inline' theme='dark' selectedKeys={[pathname]} style={{ height: '100%', borderRight: 0 }}>
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
                    <Layout style={{ padding: 20, overflow: 'auto' }}>
                        <Switch>
                            <Route exact path='/home' component={Home} />
                            <Route path='/home/article' component={Article} />
                            <Route exact path='/home/publish' component={Publish} />
                            <Route path='/home/publish/:id' component={Publish} />
                        </Switch>
                    </Layout>
                </Layout>
            </Layout>
        </div>
    )
}
```

2. 解决组件复用造成的 Bug。

重现流程：先点击编辑，内容进行了回显，再点击侧边栏的发布按钮，内容还在，解决方案如下。

```js
<Route key='add' exact path='/home/publish' component={Publish} />
<Route key='edit' path='/home/publish/:id' component={Publish} />
```

## 项目打包和预览

### 目标

能够通过命令对项目进行打包和预览。

### 打包

1. 在项目根目录打开终端，输入打包命令：`yarn build`。

2. 等待打包完成，打包后的内容被放在项目根下的 `build` 文件夹中。

3. 注意：保证根目录有 `.env.production` 文件，配置信息如下。

```bash
REACT_APP_URL = 'http://geek.itheima.net/v1_0/'
```

### 预览

1. 全局安装本地服务包：`npm i -g serve`，该包提供了 `serve` 命令，用来启动本地服务。

2. 在项目根目录中执行命令：`serve -s build`，在 build 目录中开启服务器。

3. 在浏览器中访问：`http://localhost:5000/` 预览项目。

## 按需使用 Redux 中间件

### 目标

能够根据是否为生产环境对 Redux 中间件进行优化。

### 代码

`store/index.js`

```js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'
import rootReducer from './reducers'
import { getToken } from '@/utils'
// #1
let middlewares
if (process.env.NODE_ENV === 'production') {
    middlewares = applyMiddleware(thunk)
} else {
    middlewares = composeWithDevTools(applyMiddleware(thunk))
}
export default createStore(
    rootReducer,
    {
        login: {
            token: getToken(),
        },
    },
    // #2
    middlewares
)
```

## 路由懒加载

### 目标

能够对路由进行懒加载实现代码分隔，[参考文档](https://react.docschina.org/docs/code-splitting.html)。

### 步骤

1. 在 App 组件中，导入 Suspense 组件。

2. 在 Router 内部，使用 Suspense 组件包裹组件内容。

3. 为 Suspense 组件提供 fallback 属性，指定 loading 占位内容。

4. 导入 lazy 函数，并修改为懒加载方式导入路由组件。

### 代码

`App.js`

```js
import { lazy, Suspense } from 'react'
import { Route, Switch, Redirect, Router } from 'react-router-dom'
import PrivateRoute from './components/PrivateRoute'
import history from '@/utils/history'
// #1
const Login = lazy(() => import('@/pages/Login'))
const Layout = lazy(() => import('@/pages/Layout'))
const NotFound = lazy(() => import('@/pages/NotFound'))
export default function App() {
    return (
        <Router history={history}>
            <div className='app'>
                {/* #2 */}
                <Suspense
                    fallback={
                        <div
                            style={{
                                textAlign: 'center',
                                marginTop: 200,
                            }}
                        >
                            loading...
                        </div>
                    }
                >
                    <Switch>
                        <Redirect exact from='/' to='/home' />
                        <Route path='/login' component={Login}></Route>
                        <PrivateRoute path='/home' component={Layout} />
                        <Route component={NotFound} />
                    </Switch>
                </Suspense>
            </div>
        </Router>
    )
}
```

`pages/Layout/index.js`

```js
const Home = lazy(() => import('@/pages/Home'))
const Article = lazy(() => import('@/pages/Article'))
const Publish = lazy(() => import('@/pages/Publish'))
```

## 打包体积分析

### 目标

能够分析项目打包体积。

### 步骤

📝 通过分析打包体积，才能知道项目中的哪部分内容体积过大，从而进行针对性的优化。

1. 安装分析打包体积的包：`yarn add source-map-explorer`。

2. 在 `package.json` 中的 scripts 标签中，添加分析打包体积的命令。

```json
{
    "scripts": {
        "analyze": "source-map-explorer 'build/static/js/*.js'"
    }
}
```

3. 对项目打包：`yarn build`（如果已经打过包，可省略这一步）。

4. 运行分析命令：`yarn analyze`。

5. 通过浏览器打开的页面，分析图表中的包体积。

## 配置 CDN

### 目标

能够对第三方包使用 CDN 优化。

### 内容

`craco.config.js`

```js
const path = require('path')
const { whenProd, getPlugin, pluginByName } = require('@craco/craco')
module.exports = {
    webpack: {
        alias: {
            '@': path.join(__dirname, 'src'),
        },
        configure: (webpackConfig) => {
            let cdn = {
                js: [],
                css: [],
            }
            whenProd(() => {
                webpackConfig.externals = {
                    react: 'React',
                    'react-dom': 'ReactDOM',
                    redux: 'Redux',
                    'react-router-dom': 'ReactRouterDOM',
                }
                cdn = {
                    js: [
                        'https://cdn.bootcdn.net/ajax/libs/react/17.0.2/umd/react.production.min.js',
                        'https://cdn.bootcdn.net/ajax/libs/react-dom/17.0.2/umd/react-dom.production.min.js',
                        'https://cdn.bootcdn.net/ajax/libs/redux/4.1.0/redux.min.js',
                        'https://cdn.bootcdn.net/ajax/libs/react-router-dom/5.2.0/react-router-dom.min.js',
                    ],
                    css: [],
                }
            })
            const { isFound, match } = getPlugin(webpackConfig, pluginByName('HtmlWebpackPlugin'))
            if (isFound) {
                match.options.cdn = cdn
            }

            return webpackConfig
        },
    },
}
```

`public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
        <% htmlWebpackPlugin.options.cdn.css.forEach(cdnURL=> { %>
        <link rel="stylesheet" href="<%= cdnURL %>" />
        <% }) %>
    </head>

    <body>
        <div id="root"></div>
        <% htmlWebpackPlugin.options.cdn.js.forEach(cdnURL=> { %>
        <script src="<%= cdnURL %>"></script>
        <% }) %>
    </body>
</html>
```

🧐 最近 create-react-app 升级到了 5 版本，需要把 `craco.config.js` 中挂载 CDN 的代码改成 `match.userOptions.cdn = cdn`，或者使用 `react-scripts@4.0.3` 的版本。
