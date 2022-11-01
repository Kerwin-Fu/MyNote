---
title: 15_极客园 H5
date: 2021-12-24 15:35:08
tags:
---

## 今日目标

✔ 完成项目开发前的准备工作。

<!-- more -->

## 项目介绍

### 目标

了解项目定位和功能。

### 内容

-   [极客园](http://toutiao.itheima.net/) H5 端项目：个人自媒体前台。

-   「极客园」对标 `CSDN`、`博客园` 等竞品，致力成为更加贴近年轻 IT 从业者（学员）的科技资讯类应用。

-   产品关键词：IT、极客、活力、科技、技术分享、前沿动态、内容社交。

-   用户特点：年轻有活力，对 IT 领域前言科技信息充满探索欲和学习热情。

-   项目功能包括短信登录、退出，首页：频道管理、文章列表、更多操作等；文章详情：文章评论、评论回复、点赞、收藏、关注等；个人资料展示和编辑等。

-   技术栈：`create-react-app`、react hooks、redux、`antd-mobile`、`axios`、`react-router-dom` 以及 `history`、`sass`、CSS Modules、TypeScript、lodash、ahooks、websocket 等。

## 项目搭建

### 目标

能够基于脚手架搭建支持 TypeScript 的项目。

### 步骤

1. 使用 React CLI 搭建项目：`npx create-react-app geek-h5 --template typescript`。

2. 进入项目根目录：`cd geek-h5`。

3. 启动项目：`yarn start`。

4. 调整项目目录结构如下。

```tree
src
|-- App.scss            根组件样式文件
|-- App.tsx             根组件
|-- assets              项目资源文件，比如图片等
|-- components          通用组件
|-- pages               页面
|-- index.scss          全局样式
|-- index.tsx           项目入口
|-- react-app-env.d.ts
|-- store               Redux 状态仓库
|-- types               TS 类型，包括：接口、Redux 等类型
`-- utils               工具，比如 token、axios 的封装等
```

安装解析 sass 的包。

```scss
yarn add sass
```

`src/index.tsx`

```js
import ReactDOM from 'react-dom'
import './index.scss'
import App from './App'

ReactDOM.render(<App />, document.getElementById('root'))
```

`src/index.scss`

```scss
* {
    margin: 0;
    padding: 0;
}
html,
body,
#root {
    height: 100%;
}
```

`src/App.tsx`

```js
import './App.scss'

export default function App() {
    return <div className='app'>App</div>
}
```

`src/App.scss`

```scss
.app {
    height: 100%;
}
```

## 托管项目

### 目标

能够将项目推送到 Gitee 远程仓库。

### 步骤

1. 在项目根目录打开终端，并初始化 git 仓库（如果已经有了 git 仓库，无需重复该步），命令：`git init`。

2. 添加项目内容到暂存区：`git add .`。

3. 提交项目内容到仓库区：`git commit -m 项目初始化`。

4. 添加 remote 仓库地址：`git remote add origin [gitee 仓库地址]`。

5. 将项目内容推送到 gitee：`git push origin master -u`。

## 配置路由

### 目标

能够配置登录页面的路由并显示在页面中。

### 步骤

1. 安装路由和类型声明文件。

```bash
yarn add react-router-dom@5.3.0 @types/react-router-dom -D
```

2. 在 pages 目录中创建两个文件夹：Login、Layout。

3. 分别在两个目录中创建 index.tsx 文件，并创建一个简单的组件后导出。

4. 在 App 组件中，导入路由组件以及两个页面组件。

5. 配置 Login 和 Layout 的路由规则。

`pages/Login/index.tsx`

```jsx
export default function Login() {
    return <div>Login</div>
}
```

`pages/Layout/index.tsx`

```jsx
export default function Layout() {
    return <div>布局组件</div>
}
```

`App.tsx`

```jsx
import './App.scss'
import { BrowserRouter as Router, Link, Route, Switch } from 'react-router-dom'
import Login from './pages/Login'
import Layout from './pages/Layout'

export default function App() {
    return (
        <Router>
            <div className='app'>
                <ul>
                    <li>
                        <Link to='/login'>登录</Link>
                    </li>
                    <li>
                        <Link to='/home'>首页</Link>
                    </li>
                </ul>
                <Switch>
                    <Route path='/login' component={Login} />
                    <Route path='/home' component={Layout} />
                </Switch>
            </div>
        </Router>
    )
}
```

## 路由重定向

### 目标

能够在打开页面时就展示首页内容。

### 分析

匹配默认路由，通过 Route 组件的 `render` 属性配合 Redirect 组件进行重定向，[Route render prop](https://reactrouter.com/web/api/Route/render-func)。

### 步骤

1. 在 App.tsx 中添加一个新的 Route，用来匹配默认路由。

2. 为 Route 组件添加 `render` 属性，用来渲染自定义内容。

3. 在 render 中，渲染 Redirect 实现路由重定向。

### 代码

`App.tsx`

```jsx
<Switch>
    {/* 注意：一般使用 Redirect 时必须包裹到 Switch 里面，否则 from 属性会失效 */}
    {/* <Redirect exact from="/" to="/home"/> */}
    {/* Redirect 也可以脱离 Switch 去使用，需要配合 Route 组件的 render 属性 */}
    <Route exact path='/' render={() => <Redirect to='/home' />} />
    <Route path='/login' component={Login} />
    <Route path='/home' component={Layout} />
</Switch>
```

### 总结

-   通过哪个组件来实现路由重定向？

-   Route 通过哪个属性来渲染自定义内容？

## AntD Mobile

[AntD Mobile](https://mobile.ant.design/) 是 Ant Design 的移动规范的 React 实现，服务于蚂蚁及口碑无线业务。

### 目标

能够使用 antd-mobile 的 Button 组件渲染按钮。

### 步骤

1. 安装 antd 组件库：`yarn add antd-mobile@next`。

2. 导入 Button 组件。

3. 在 Login 页面渲染 Button 组件。

### 代码

`pages/Login/index.tsx`

```js
import { Button } from 'antd-mobile'
export default function Login() {
    return (
        <div>
            <Button color='primary'>Button</Button>
        </div>
    )
}
```

## 原生 CSS 变量

### 目标

能够掌握原生 CSS 变量的使用。

### 内容

CSS 自定义属性，通常称为 [CSS 变量](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_custom_properties)，类似于 JS 中声明的变量，可以复用。比如，项目中多次使用某一个颜色值，原来需要重复写多次，如下。

```css
.list-item-active {
    color: #fc6627;
}

.tabs-item-active {
    color: #fc6627;
}
```

使用 CSS 变量可以实现复用，简化了代码，如下。

```css
/* 1 创建全局 CSS 变量 --geek-color-primary */
:root {
    --geek-color-primary: #fc6627;
}

/* 2 复用 */
.list-item-active {
    color: var(--geek-color-primary);
}

.tabs-item-active {
    color: var(--geek-color-primary);
}
```

-   特点。

    a，可复用。

    b，语义化，`--geek-color-primary` 比 `#fc6627` 更容易让人理解。

-   根据 CSS 变量的作用域，分为两种。

    a，全局 CSS 变量，全局有效。

    b，局部 CSS 变量，只在某个作用域内（比如，某个类名中）有效。

### 全局变量

1. 使用 `:root` 这个 CSS 伪类可以匹配文档树的根元素 html，在里面定义的变量相当于 JS 中的全局变量，在任意位置都可以使用该变量。

2. 定义 CSS 变量通过两个减号（--）开头，多个单词之间推荐使用 - 链接，CSS 变量名可以是任意变量名。

```css
:root {
    --geek-color-primary: #fc6627;
}
.tabs-item-active {
    color: var(--geek-color-primary);
}
.list-item-active {
    color: var(--geek-color-primary);
}
```

3. 在 html 中声明全局变量。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
        <style>
            :root {
                --active-color: green;
            }

            html {
                /* 处于 html 内部的元素都能使用此变量，类似于 :root，权重比 :root 低 */
                --active-color: pink;
            }

            .title,
            h3 {
                color: var(--active-color);
            }
        </style>
    </head>

    <body>
        <header class="title">标题</header>
        <h3>2022~~你好</h3>
    </body>
</html>
```

### 局部变量

```css
.list {
    --active-color: #1677ff;
    /* 在该 类 内部使用改变量 */
    color: var(--active-color);
}
.test {
    /* 错误演示：无效！ */
    color: var(--active-color);
}
```

## 自定义主题

### 目标

能够使用原生 CSS 变量来定制极客园项目的主题，[文档](https://mobile.ant.design/zh/guide/theming)。

### 代码

`src/index.scss`

```scss
:root:root {
    --adm-color-primary: #fc6627;
    --adm-color-success: #00b578;
    --adm-color-warning: #ff8f1f;
    --adm-color-danger: #ff3141;
    --adm-color-white: #ffffff;
    --adm-color-weak: #999999;
    --adm-color-light: #cccccc;
    --adm-border-color: #eeeeee;
    --adm-font-size-main: 13px;
    --adm-color-text: #333333;
}
* {
    margin: 0;
    padding: 0;
}
html,
body,
#root {
    height: 100%;
}
```

## 配置路径别名

[自定义 CRA 的默认配置](https://ant.design/docs/react/use-with-create-react-app-cn#%E9%AB%98%E7%BA%A7%E9%85%8D%E7%BD%AE)，[craco 配置文档](https://github.com/gsoft-inc/craco/blob/master/packages/craco/README.md#configuration)。

### 目标

能够配置@路径别名简化路径处理

### 步骤

1. 安装修改 CRA 配置的包：`yarn add -D @craco/craco`。

2. 在项目根目录中创建 craco 的配置文件：`craco.config.js`，并在配置文件中配置路径别名。

3. 修改 `package.json` 中的脚本命令。

4. 在代码中，就可以通过 `@` 来表示 src 目录的绝对路径。

5. 重启项目，让配置生效。

### 代码

`/craco.config.js`

```js
const path = require('path')

module.exports = {
    // webpack 配置
    webpack: {
        // 配置别名
        alias: {
            // 约定：使用 @ 表示 src 文件所在路径
            '@': path.resolve(__dirname, 'src'),
            '@scss': path.resolve(__dirname, 'src', 'assets', 'styles'),
        },
    },
}
```

`package.json`

```json
// 将 start/build/test 三个命令修改为 craco 方式
{
    "scripts": {
        "start": "craco start",
        "build": "craco build",
        "test": "craco test",
        "eject": "react-scripts eject"
    }
}
```

## 配置路径提示

### 目标

能够让 VSCode 识别 @ 路径并给出路径提示。

### 分析

实际上 `jsconfig.json` 是参考 `tsconfig.json` 的，由于 TS 项目自带此配置文件，因此就不再需要使用 `jsconfig.json` 了。

### 步骤

1. 创建 `path.tsconfig.json` 配置文件，在该配置文件中添加以下配置。

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

2. 在 `tsconfig.json` 中导入该配置文件，让配置生效。

```json
{
    // 导入配置文件
    "extends": "./path.tsconfig.json"
}
```

## 移动端适配

### 目标

能跟通过配置实现自动适配移动端项目。

### 分析

-   为什么要适配？

    为了让我们开发的移动端项目页面，在不同尺寸大小的移动端设备（手机）中，保持相同的比例。

-   适配原理。

    选择某个手机的尺寸大小作为基准，其他手机进行**等比例缩放**，一般选择 `iPhone 6`（2 倍屏幕），屏幕宽度为：`375px`。

-   适配方式。

    a，rem：动态修改 `html` 元素的 `font-size`；额外设置 `body` 元素的字体大小为正常值。

    b，vw：`1vw` 就等于屏幕宽度的 `1%`。

### 步骤

[postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport)

1. 安装 px 转 vw 的包：`yarn add -D postcss-px-to-viewport`。

2. 包的作用：将 `px` 转化为 `vw`，所以有了该工具，只需要在代码中写 `px` 即可。

3. 在 `craco.config.js` 添加相应配置。

4. 重启项目，让配置生效。

### 代码

`/craco.config.js`

```js
const path = require('path')
// #1
const pxToViewport = require('postcss-px-to-viewport')
// #2
const vw = pxToViewport({
    viewportWidth: 375,
})
module.exports = {
    webpack: {
        alias: {
            '@': path.resolve(__dirname, 'src'),
            '@scss': path.resolve(__dirname, 'src', 'assets', 'styles'),
        },
    },
    // #3
    style: {
        postcss: {
            plugins: [vw],
        },
    },
}
```

## 一像素问题

### 目标

能够展示 1px 像素的边框，[参考 antd-mobile 的实现](https://github.com/ant-design/ant-design-mobile/blob/v2/components/style/mixins/hairline.less)。

### 分析

实现原理：**伪元素 + transform 缩放**，伪元素 `::after` 或 `::before` 独立于当前元素，可以单独对其缩放而不影响元素本身的缩放。

### 代码

`src/assets/styles/hairline.scss`

```scss
// 定义函数：@mixin 函数名() {}
// 使用函数：@include 函数名()
@mixin scale-hairline-common($color, $top, $right, $bottom, $left) {
    content: '';
    position: absolute;
    display: block;
    z-index: 1;
    top: $top;
    right: $right;
    bottom: $bottom;
    left: $left;
    background-color: $color;
}

@mixin hairline($direction, $color: #000, $radius: 0) {
    // 这儿额外补充了 relative
    position: relative;
    @if $direction == top {
        border-top: 1px solid $color;

        // min-resolution 用来检测设备的最小像素密度
        @media (min-resolution: 2dppx) {
            border-top: none;

            &::before {
                @include scale-hairline-common($color, 0, auto, auto, 0);
                width: 100%;
                height: 1px;
                transform-origin: 50% 50%;
                transform: scaleY(0.5);

                @media (min-resolution: 3dppx) {
                    transform: scaleY(0.33);
                }
            }
        }
    } @else if $direction == right {
        border-right: 1px solid $color;

        @media (min-resolution: 2dppx) {
            border-right: none;

            &::after {
                @include scale-hairline-common($color, 0, 0, auto, auto);
                width: 1px;
                height: 100%;
                background: $color;
                transform-origin: 100% 50%;
                transform: scaleX(0.5);

                @media (min-resolution: 3dppx) {
                    transform: scaleX(0.33);
                }
            }
        }
    } @else if $direction == bottom {
        border-bottom: 1px solid $color;

        @media (min-resolution: 2dppx) {
            border-bottom: none;

            &::after {
                @include scale-hairline-common($color, auto, auto, 0, 0);
                width: 100%;
                height: 1px;
                transform-origin: 50% 100%;
                transform: scaleY(0.5);

                @media (min-resolution: 3dppx) {
                    transform: scaleY(0.33);
                }
            }
        }
    } @else if $direction == left {
        border-left: 1px solid $color;

        @media (min-resolution: 2dppx) {
            border-left: none;

            &::before {
                @include scale-hairline-common($color, 0, auto, auto, 0);
                width: 1px;
                height: 100%;
                transform-origin: 100% 50%;
                transform: scaleX(0.5);

                @media (min-resolution: 3dppx) {
                    transform: scaleX(0.33);
                }
            }
        }
    } @else if $direction == all {
        border: 1px solid $color;
        border-radius: $radius;

        @media (min-resolution: 2dppx) {
            position: relative;
            border: none;

            &::before {
                content: '';
                position: absolute;
                left: 0;
                top: 0;
                width: 200%;
                height: 200%;
                border: 1px solid $color;
                border-radius: $radius * 2;
                transform-origin: 0 0;
                transform: scale(0.5);
                box-sizing: border-box;
                pointer-events: none;
            }
        }
    }
}

// 移除边框
@mixin hairline-remove($position: all) {
    @if $position == left {
        border-left: 0;
        &::before {
            display: none !important;
        }
    } @else if $position == right {
        border-right: 0;
        &::after {
            display: none !important;
        }
    } @else if $position == top {
        border-top: 0;
        &::before {
            display: none !important;
        }
    } @else if $position == bottom {
        border-bottom: 0;
        &::after {
            display: none !important;
        }
    } @else if $position == all {
        border: 0;
        &::before {
            display: none !important;
        }
        &::after {
            display: none !important;
        }
    }
}
```

-   使用说明

```scss
@import '@scss/hairline.scss';

// 添加边框
@include hairline(all, #000);
@include hairline(top, #000);
@include hairline(left, #000);
@include hairline(bottom, #000);
@include hairline(right, #000);
// 移除边框
@include hairline-remove(all);
@include hairline-remove(left);
@include hairline-remove(right);
@include hairline-remove(bottom);
@include hairline-remove(top);
```

## 使用字体图标

### 目标

能够在项目中使用字体图标，[彩色图标的使用](https://www.iconfont.cn/help/detail?spm=a313x.7781069.1998910419.d8cf4382a&helptype=code)。

1. 在 public 下 index.html 中引入该文件。

```html
<script src="//at.alicdn.com/t/font_2503709_f4q9dl3hktl.js"></script>
```

2. 在 `index.scss` 中添加通过 css 代码。

```css
.icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
}
```

3. 在组件中，使用如下。

```jsx
<svg className='icon' aria-hidden='true' fontSize={30}>
    <use xlinkHref='#iconbtn_readingtime'></use>
</svg>
```

## 封装图标组件

### 目标

能够封装 Icon 图标通用组件。

### 步骤

1. 在 components 目录中，创建 Icon/index.tsx 文件。

2. 创建 Icon 组件，并指定 props 类型。

3. 安装 classnames 包，处理类名。

### 代码

`components/Icon/index.tsx`

```ts
import classnames from 'classnames'

// 组件的 props 类型
type Props = {
    type: string // icon 的类型
    className?: string // icon 的自定义样式
    onClick?: () => void // 点击事件
}

const Icon = ({ type, className, onClick }: Props) => {
    return (
        <svg className={classnames('icon', className)} aria-hidden='true' onClick={onClick}>
            <use xlinkHref={`#${type}`}></use>
        </svg>
    )
}

export default Icon
```

`pages/Login/index.tsx`

```ts
import Icon from '@/components/Icon'
export default function Login() {
    return (
        <div>
            {/* <svg className='icon' aria-hidden='true' fontSize={30}>
                <use xlinkHref='#iconbtn_readingtime'></use>
            </svg> */}
            <Icon type='iconbtn_readingtime' />
        </div>
    )
}
```
