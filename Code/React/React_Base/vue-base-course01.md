---
title: 00_Vue3
date: 2021-11-01 08:50:58
tags:
---

## 今日目标

✔ 了解 Vue3 的变化。

✔ 掌握 Composition API 的用法。

<!-- more -->

## Vue3 基本概述

### 目标

了解 Vue3 现状，以及它的优点，展望它的未来。

### 内容

<img src="/resource/images/onepiece.png" width="150"/>

-   2020 年 9 月 18 日，Vue 发布了 [3.0 版本](https://v3.cn.vuejs.org/)，代号：One Piece[（海贼王）](https://github.com/vuejs/vue-next/releases/tag/v3.0.0)，周边生态原因，当时大多数开发者还处于观望状态。

-   现在主流组件库都已经发布了支持 Vue3.0 的版本，例如 [Element Plus](https://element-plus.gitee.io/zh-CN/)、[Vant](https://vant-contrib.gitee.io/vant/v3/#/zh-CN)、[Vue Use](https://vueuse.org/)，其他生态也在不断地完善中，所以 Vue3 是趋势。

-   2022 年 2 月 7 日开始，[Vue3 也将成为新的默认版本](https://zhuanlan.zhihu.com/p/460055155)。

### 优点

-   <font color=e32d40>**Composition API**</font> ，能够更好的组织、封装、复用代码，[RFCs](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0013-composition-api.md)。

-   性能：打包大小减少 41%、初次渲染快 55%、更新渲染快 133%、内存减少 54%，主要原因在于 Proxy，VNode，[Tree Shaking support](https://v3.cn.vuejs.org/guide/migration/global-api-treeshaking.html#_2-x-%E8%AF%AD%E6%B3%95)。

-   Better TS support，[源码](https://github.com/vuejs/vue-next)。

-   新特性：Fragment、Teleport、Suspense。

-   趋势：未来肯定会有越来越多的企业使用 Vue3.0 + TS 进行大型项目的开发。

-   对于个人来说：适应市场需求，学习流行的技术提升竞争力，加薪！

### 小结

Vue3 有哪些优点？

## Vite 基本使用

### 目标

-   了解 [Vite](https://cn.vitejs.dev/) 是什么？

-   能够使用 Vite 创建 Vue 项目，在此项目的基础上学习 Vue3 的知识。

### 内容

-   [是什么](https://cn.vitejs.dev/)：下一代前端开发与构建工具，热更新、打包构建速度更快，但目前周边生态还不如 Webpack 成熟，所以实际开发中还是建议使用 Webpack。

-   但目前就学习 Vue3 语法来说，我们可以使用更轻量的 [Vite](https://vitejs.cn/guide/#scaffolding-your-first-vite-project)，例如要构建一个 Vite + Vue 项目，如下。

```bash
npm init vite-app <project-name>
cd <project-name>
npm install
npm run dev
```

-   Webpack：将所有的模块提前编译、打包进 bundle 中，不管这个模块是否被用到，随着项目越来越大，打包启动的速度自然越来越慢。

-   Vite：瞬间开启一个服务，并不会先编译所有文件，当浏览器用到某个文件时，Vite 服务会收到请求然后编译后相应到客户端。

<div style="display: flex; justify-content: center;">
    <img src="/resource/images/bundler.37740380.png" width="400"/>
    <img src="/resource/images/esm.3070012d.png" width="400"/>
</div>

### 小结

Vite 是什么？

## 创建 Vue 应用

### 目标

掌握如何创建 Vue3 应用实例。

### 步骤

1. 在 `main.js` 中按需导入 `createApp` 函数。

2. 定义 `App.vue` 根组件，导入到 `main.js`。

3. 使用 `createApp` 函数基于 `App.vue` 根组件创建应用实例。

4. 挂载至 `index.html` 的 `#app` 容器。

`main.js`

```js
// 1. 导入 createApp 函数，不再是曾经的 Vue 了
// 2. 编写一个根组件 App.vue，导入进来
// 3. 基于根组件创建应用实例，类似 Vue2 的 vm，但比 vm 更轻量
// 4. 挂载到 index.html 的 #app 容器
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App)
app.mount('#app')
```

`App.vue`

```html
<template>
    <div class="container">我是根组件</div>
</template>
<script>
    export default {
        name: 'App',
    }
</script>
```

### 提示

<img src="/resource/images/checkjs.png"/>

如果出现上面的错误提示，尝试把 VSCode 中的 Check JS 前面的勾取消掉试下。

<img src="/resource/images/checkjs2.png"/>

### 小结

-   如何创建 Vue 应用实例？

-   通过 createApp 创建的应用实例，以后需要扩展的功能都是要通过 app 进行。

## 安装开发工具

-   VSCode 代码片段插件：Vue VSCode Snippets，使用见[文档](https://github.com/sdras/vue-vscode-snippets)。

-   Vue3 的 Chrome 调试插件也变了，[下载链接](https://chrome.zzzmh.cn/info?token=ljjemllljcmogpfapbkkighbhhppjdbg)，注意安装后需要把之前的 Vue2 Devtools 关闭掉。

## 选项/组合 API

### 目标

理解什么是 Options API 写法，什么是 Composition API 写法。

### 需求

<img src="/resource/images/posandadd1.png"/>

### Vue2

-   优点：易于学习和使用，写代码的位置已经约定好。

-   缺点：数据和业务逻辑分散在同一个文件的 N 个地方，随着业务复杂度的上升，可能会出现动图左侧的代码组织方式，不利于管理和维护。

```html
<template>
    <div class="container">
        <p>X 轴：{{ x }} Y 轴：{{ y }}</p>
        <hr />
        <div>
            <p>{{ count }}</p>
            <button @click="add()">自增</button>
        </div>
    </div>
</template>
<script>
    export default {
        name: 'App',
        data() {
            return {
                // !#Fn1
                x: 0,
                y: 0,
                // ?#Fn2
                count: 0,
            }
        },
        mounted() {
            // !#Fn1
            document.addEventListener('mousemove', this.move)
        },
        methods: {
            // !#Fn1
            move(e) {
                this.x = e.pageX
                this.y = e.pageY
            },
            // ?#Fn2
            add() {
                this.count++
            },
        },
        destroyed() {
            // !#Fn1
            document.removeEventListener('mousemove', this.move)
        },
    }
</script>
```

### Vue3

<img src="/resource/images/ifer_options.gif" width="300"/>

-   优点：可以把同一功能的<font color=e32d40>**数据**</font>和<font color=e32d40>**业务逻辑**</font>组织到一起，方便复用和维护。

-   缺点：需要有良好的代码组织和拆分能力，相对没有 Vue2 容易上手。

-   注意：为了能让大家较好的过渡到 Vue3.0 版本，目前也是支持 Vue2.x 选项 API 的写法。

-   链接：[why-composition-api](https://v3.vuejs.org/guide/composition-api-introduction.html#why-composition-api)、[composition-api-doc](https://v3.cn.vuejs.org/api/composition-api.html)。

```html
<template>
    <div class="container">
        <p>X 轴：{{ x }} Y 轴：{{ y }}</p>
        <hr />
        <div>
            <p>{{ count }}</p>
            <button @click="add()">自增</button>
        </div>
    </div>
</template>
<script>
    import { onMounted, onUnmounted, reactive, ref, toRefs } from 'vue'
    export default {
        name: 'App',
        setup() {
            // !#Fn1
            const mouse = reactive({
                x: 0,
                y: 0,
            })
            const move = (e) => {
                mouse.x = e.pageX
                mouse.y = e.pageY
            }
            onMounted(() => {
                document.addEventListener('mousemove', move)
            })
            onUnmounted(() => {
                document.removeEventListener('mousemove', move)
            })

            // ?Fn2
            const count = ref(0)
            const add = () => {
                count.value++
            }

            // 统一返回数据供模板使用
            return {
                ...toRefs(mouse),
                count,
                add,
            }
        },
    }
</script>
```

### 小结

Vue3 Composition API 可以把 \_\_ 和 \_\_ 组合到一起？

## setup 入口函数

### 目标

掌握 setup 函数的基本使用。

### 内容

-   是什么：`setup` 是 Vue3 中新增的组件配置项，作为组合 API 的入口函数。

-   执行时机：实例创建前调用，甚至早于 Vue2 中的 beforeCreate。

-   注意点：由于执行 setup 的时候实例还没有 created，所以在 setup 中是不能直接使用 data 和 methods 中的数据的，所以 Vue3 干脆把 setup 中的 this 绑定了 undefined，防止乱用！

-   虽然 Vue2 中的 data 和 methods 配置项虽然在 Vue3 中也能使用，但不建议了，建议数据和方法都写在 setup 函数中，并<font color=e32d40>**通过 return 进行返回可在模版中直接使用**</font>（一般情况下 setup 不能为异步函数）。

```html
<template>
    <h1 @click="say()">{{ msg }}</h1>
</template>
<script>
    export default {
        setup() {
            const msg = 'Hello Vue3'
            const say = () => {
                console.log(msg)
            }
            return { msg, say }
        },
    }
</script>
```

-   了解：setup 也可以返回一个渲染函数（面试问，setup 中的 return 一定只能返回一个对象吗）。

```html
<script>
    import { h } from 'vue'
    export default {
        name: 'App',
        setup() {
            return () => h('h2', 'Hello Vue3')
        },
    }
</script>
```

### 小结

-   setup 的执行时机是什么？

-   setup 中的 this 指向是什么？

-   想在模板中使用 setup 中定义的数据，该怎么做？

## reactive 包装数组

### 目标

掌握使用 reactive 函数包装数组为响应式数据。

### 内容

[reactive](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 是一个函数，用来将普通对象/数组包装成响应式式数据使用（基于 Proxy），无法直接处理基本数据类型！

#### 需求

📝 点击删除当前行信息。

<img src="/resource/images/ifer_del.png"/>

```html
<template>
    <ul>
        <li v-for="(item, index) in arr" :key="item" @click="removeItem(index)">{{ item }}</li>
    </ul>
</template>

<script>
    export default {
        name: 'App',
        setup() {
            const arr = ['a', 'b', 'c']
            const removeItem = (index) => {
                arr.splice(index, 1)
            }
            return {
                arr,
                removeItem,
            }
        },
    }
</script>
```

#### 问题

数据确实是删了，但视图没有更新（不是响应式的）！

#### 解决

使用 reactive 包装数组使变成响应式数据。

```html
<template>
    <ul>
        <li v-for="(item, index) in arr" :key="item" @click="removeItem(index)">{{ item }}</li>
    </ul>
</template>

<script>
    import { reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const arr = reactive(['a', 'b', 'c'])
            const removeItem = (index) => {
                arr.splice(index, 1)
            }
            return {
                arr,
                removeItem,
            }
        },
    }
</script>
```

#### 注意

Vue3 中是可以通过索引去修改数组里面的内容，并且是响应式的。

```html
<template>
    <ul>
        <li v-for="(item, index) in arr" :key="item" @click="removeItem(index)">{{ item }}</li>
    </ul>
</template>

<script>
    import { reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            let arr = reactive(['a', 'b', 'c'])
            const removeItem = (index) => {
                // 可以通过索引去修改里面的内容，并且是响应式的
                arr[index] = 'xxx'
                // !注意对 arr 本身的修改是不会触发视图更新的
                // arr = ['xxx', 'yyy', 'zzz']
            }
            return {
                arr,
                removeItem,
            }
        },
    }
</script>
```

### 小结

-   reactive 的作用是什么？

-   Vue3 中通过索引修改数组中的内容是响应式的吗？

## reactive 包装对象

### 目标

掌握使用 reactive 函数包装对象为响应式数据。

### 需求

📝 列表渲染、删除功能、添加功能。

<img src="/resource/images/v3reactive.png"/>

### 列表删除

```html
<template>
    <ul>
        <li v-for="(item, index) in state.arr" :key="item.id" @click="removeItem(index)">{{ item.name }}</li>
    </ul>
</template>

<script>
    import { reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const state = reactive({
                arr: [
                    {
                        id: 0,
                        name: 'ifer',
                    },
                    {
                        id: 1,
                        name: 'elser',
                    },
                    {
                        id: 2,
                        name: 'xxx',
                    },
                ],
            })
            const removeItem = (index) => {
                // 默认是递归监听的，对象里面任何一个数据的变化都是响应式的
                state.arr.splice(index, 1)
            }
            return {
                state,
                removeItem,
            }
        },
    }
</script>
```

### 抽离函数

优化：将同一功能的数据和业务逻辑抽离为一个函数，代码更易读，更容易**复用**。

```html
<template>
    <ul>
        <li v-for="(item, index) in state.arr" :key="item.id" @click="removeItem(index)">{{ item.name }}</li>
    </ul>
</template>
<script>
    import { reactive } from 'vue'

    function useRemoveItem() {
        const state = reactive({
            arr: [
                {
                    id: 0,
                    name: 'ifer',
                },
                {
                    id: 1,
                    name: 'elser',
                },
                {
                    id: 2,
                    name: 'xxx',
                },
            ],
        })
        const removeItem = (index) => {
            state.arr.splice(index, 1)
        }
        return { state, removeItem }
    }

    export default {
        name: 'App',
        setup() {
            const { state, removeItem } = useRemoveItem()
            return {
                state,
                removeItem,
            }
        },
    }
</script>
```

### 添加功能

1. 错误写法一：user 对象没有用 reactive 进行包裹。

```html
<template>
    <form @submit.prevent="handleSubmit">
        <input type="text" v-model="user.id" />
        <input type="text" v-model="user.name" />
        <input type="submit" />
    </form>
    <ul>
        <li v-for="(item, index) in state.arr" :key="item.id" @click="removeItem(index)">{{ item.name }}</li>
    </ul>
</template>

<script>
    import { reactive } from 'vue'

    function useRemoveItem() {
        const state = reactive({
            arr: [
                {
                    id: 0,
                    name: 'ifer',
                },
                {
                    id: 1,
                    name: 'elser',
                },
                {
                    id: 2,
                    name: 'xxx',
                },
            ],
        })
        const removeItem = (index) => {
            state.arr.splice(index, 1)
        }
        return { state, removeItem }
    }
    function useAddItem(state) {
        // user 没有用 reactive 包裹
        const user = {
            id: '',
            name: '',
        }
        const handleSubmit = () => {
            state.arr.push({
                id: user.id,
                name: user.name,
            })
            // 思考：由于 user 没有用 reactive 进行包裹，按理来说对 user 的修改视图是不会响应的，但这里为什么表现正常呢？
            user.id = ''
            user.name = ''
        }
        return {
            user,
            handleSubmit,
        }
    }

    export default {
        name: 'App',
        setup() {
            const { state, removeItem } = useRemoveItem()
            const { user, handleSubmit } = useAddItem(state)
            return {
                state,
                removeItem,
                user,
                handleSubmit,
            }
        },
    }
</script>
```

2. 错误写法二：直接 push 了原对象，导致会相互影响。

```js
const handleSubmit = () => {
    // !这里直接添加了 user 到 arr，后续对 user 的操作会影响添加到 arr 中的数据
    state.arr.push(user)
    user.id = ''
    user.name = ''
}
```

3. 解决方法如下。

```js
const handleSubmit = () => {
    // 方法1
    /* state.arr.push({
      id: user.id,
      name: user.name,
    }) */
    // 方法2
    /* state.arr.push({
      ...user,
    }) */
    // 方法3
    const userCopy = Object.assign({}, user)
    state.arr.push(userCopy)
    user.id = ''
    user.name = ''
}
```

### 拆分文件

`remove.js`

```js
import { reactive } from 'vue'
export default function userRemoveItem() {
    const state = reactive({
        arr: [
            {
                id: 0,
                name: 'ifer',
            },
            {
                id: 1,
                name: 'elser',
            },
            {
                id: 2,
                name: 'xxx',
            },
        ],
    })
    const removeItem = (index) => {
        state.arr.splice(index, 1)
    }
    return { state, removeItem }
}
```

`add.js`

```js
import { reactive } from 'vue'
export default function useAddItem(state) {
    const user = reactive({
        id: '',
        name: '',
    })
    const handleSubmit = () => {
        const userCopy = Object.assign({}, user)
        state.arr.push(userCopy)
        user.id = ''
        user.name = ''
    }
    return {
        user,
        handleSubmit,
    }
}
```

`App.vue`

```html
<template>
    <form @submit.prevent="handleSubmit">
        <input type="text" v-model="user.id" />
        <input type="text" v-model="user.name" />
        <input type="submit" />
    </form>
    <ul>
        <li v-for="(item, index) in state.arr" :key="item.id" @click="removeItem(index)">{{ item.name }}</li>
    </ul>
</template>

<script>
    import userRemoveItem from './hooks/remove'
    import useAddItem from './hooks/add'
    export default {
        name: 'App',
        setup() {
            const { state, removeItem } = userRemoveItem()
            const { user, handleSubmit } = useAddItem(state)
            return {
                state,
                removeItem,
                user,
                handleSubmit,
            }
        },
    }
</script>
```

## Vue3 生命周期

### 目标

掌握组合 API 中生命周期钩子函数的写法。

### 内容

-   [组合 API](https://v3.cn.vuejs.org/api/composition-api.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)生命周期写法，其实 [选项 API](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#beforecreate) 的写法在 Vue3 中也是支持。

-   Vue3（组合 API）常用的生命周期钩子有 7 个，<font color=e32d40>**可以多次使用同一个钩子**</font>，执行顺序和书写顺序相同。

-   setup、onBeforeMount、onMounted、onBeforeUpdate、onUpdated、onBeforeUnmount、onUnmounted。

<img src="/resource/images/vuelife.png"/>

`App.vue`

```html
<template>
    <hello-world v-if="state.bBar" />
    <button @click="state.bBar = !state.bBar">destroy cmp</button>
</template>

<script>
    import HelloWorld from './components/HelloWorld.vue'
    import { reactive } from 'vue'
    export default {
        name: 'App',
        components: {
            HelloWorld,
        },
        setup() {
            const state = reactive({
                bBar: true,
            })
            return {
                state,
            }
        },
    }
</script>
```

`HelloWorld.vue`

```html
<template>
    <p>{{ state.msg }}</p>
    <button @click="state.msg = 'xxx'">update msg</button>
</template>

<script>
    import { onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted, reactive } from 'vue'
    export default {
        name: 'HelloWorld',
        setup() {
            const state = reactive({
                msg: 'Hello World',
            })

            onBeforeMount(() => {
                console.log('onBeforeMount')
            })
            onMounted(() => {
                console.log('onMounted')
            })
            onBeforeUpdate(() => {
                console.log('onBeforeUpdate')
            })
            onUpdated(() => {
                console.log('onUpdated')
            })
            onBeforeUnmount(() => {
                console.log('onBeforeUnmount')
            })
            onUnmounted(() => {
                console.log('onUnmounted')
            })
            return {
                state,
            }
        },
    }
</script>
```

### 小结

Vue3 把 Vue2 中的哪两个钩子换成了 setup？

## 记录鼠标位置

1. 定义一个响应式数据对象，包含 x 和 y 属性。

2. 在组件渲染完毕后，监听 document 的鼠标移动事件。

3. 指定 move 函数为事件对应回调，在函数中修改坐标。

4. 组件销毁时，解绑事件。

5. setup 中返回数据，并在模版中使用。

```html
<template>
    <div>x: {{ mouse.x }} y: {{ mouse.y }}</div>
</template>

<script>
    import { onMounted, onUnmounted, reactive } from 'vue'
    // Hook：本质是一个函数，把 setup 中使用的 composition API 逻辑进行了提取/封装，类似于 Vue2 中的 mixin。
    // 优势：利于代码复用，使 setup 中的逻辑更加清晰。
    const useMouse = () => {
        const mouse = reactive({
            x: 0,
            y: 0,
        })
        const move = (e) => {
            mouse.x = e.pageX
            mouse.y = e.pageY
        }
        onMounted(() => {
            document.addEventListener('mousemove', move)
        })
        onUnmounted(() => {
            document.removeEventListener('mousemove', move)
        })
        return mouse
    }
    export default {
        name: 'App',
        setup() {
            const mouse = useMouse()
            return {
                mouse,
            }
        },
    }
</script>
```

## toRef

### 目标

掌握 toRef 函数的使用。

### 内容

toRef 函数的作用：转换<font color=e32d40>**响应式对象**</font>中某个属性为单独响应式数据，并且转换后的值和之前是关联的（ref 函数也可以转换，但值非关联，后面详讲 ref 函数）。

### 需求

📝 需求：模板中只需要用到响应式对象中的 name 属性。

```html
<template>
    <div class="container">
        <h2>{{ name }}</h2>
        <button @click="updateName">修改数据</button>
    </div>
</template>
<script>
    import { reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                name: 'ifer',
                age: 10,
            })
            const updateName = () => {
                obj.name = 'xxx'
            }
            return { name: obj.name, updateName }
        },
    }
</script>
```

### 问题

修改数据，发现视图并没有更新，也就是上面的操作导致数据丢失了响应式，丢失响应式的操作，常见的还有解构赋值等，如下。

```html
<template>
    <div class="container">
        <h2>{{ name }}</h2>
        <button @click="updateName">修改数据</button>
    </div>
</template>
<script>
    import { reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                name: 'ifer',
                age: 10,
            })
            // !解构出简单数据类型会失去响应式
            let { name } = obj
            const updateName = () => {
                // obj.name = 'xxx' // 不响应
                name = 'xxx' // 不响应
            }
            return { name, updateName }
        },
    }
</script>
```

### 解决

```html
<template>
    <div class="container">
        <h2>{{ name }}</h2>
        <button @click="updateName">修改数据</button>
    </div>
</template>
<script>
    import { reactive, toRef } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                name: 'ifer',
                age: 10,
            })
            const name = toRef(obj, 'name')
            const updateName = () => {
                // 注意：需要使用 name.value 进行修改
                name.value = 'xxx'
                // 对 obj.name 的修改也会影响视图的变化，即值是关联的
                // obj.name = 'xxx' // ok
            }
            return { name, updateName }
        },
    }
</script>
```

## toRefs

### 目标

掌握 toRefs 函数的使用。

### 内容

toRefs 函数的作用：转换<font color=e32d40>**响应式对象**</font>中所有属性为单独响应式数据，并且转换后的值和之前是关联的。

### 需求

📝 模板中需要写 obj.name、obj.age ...很麻烦，期望能够直接能使用 name、age 属性。

```html
<template>
    <div class="container">
        <h2>{{ name }} {{ age }}</h2>
        <button @click="updateName">修改数据</button>
    </div>
</template>
<script>
    import { reactive, toRefs } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                name: 'ifer',
                age: 10,
            })
            const updateName = () => {
                obj.name = 'xxx'
                obj.age = 18
            }
            return { ...toRefs(obj), updateName }
        },
    }
</script>
```

## ref 函数

### 目标

掌握 ref 函数的使用。

### 基本使用

ref 函数，常用于把简单数据类型包裹为响应式数据，注意 JS 中操作值的时候，需要加 `.value` 属性，模板中正常使用即可。

```html
<template>
    <div class="container">
        <div>{{ name }}</div>
        <button @click="updateName">修改数据</button>
    </div>
</template>
<script>
    import { ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const name = ref('ifer')
            const updateName = () => {
                name.value = 'xxx'
            }
            return { name, updateName }
        },
    }
</script>
```

### 点击计数

1. 定义一个简单数据类型的响应式数据。

2. 定义一个修改数字的方法。

3. 在 setup 返回数据和函数，供模板中使用。

```html
<template>
    <h3>{{ count }}</h3>
    <button @click="add">累加1</button>
</template>
<script>
    import { ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const count = ref(0)
            const add = () => {
                count.value++
            }
            return { count, add }
        },
    }
</script>
```

### 包装复杂数据类型

注意：ref 其实也可以包裹复杂数据类型为响应式数据，一般对于数据类型未确定的情况下推荐使用 ref。

```html
<template>
    <div class="container">
        <div>{{ data?.name }}</div>
        <button @click="updateName">修改数据</button>
    </div>
</template>
<script>
    import { ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            // 初始值是 null
            const data = ref(null)
            setTimeout(() => {
                // 右边的对象可能是后端返回的
                data.value = {
                    name: 'ifer',
                }
            }, 1000)
            const updateName = () => {
                data.value.name = 'xxx'
            }
            return { data, updateName }
        },
    }
</script>
```

### 小结

当你明确知道需要包裹的是一个对象，那么推荐使用 reactive，其他情况使用 ref 即可。

## ref 属性

### 目标

能够通过 ref 属性获取 DOM 或组件。

### 内容

获取单个 DOM 或者组件。

```html
<template>
    <!-- #3 -->
    <div ref="dom">我是box</div>
</template>
<script>
    import { onMounted, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            // #1
            const dom = ref(null)
            onMounted(() => {
                // #4
                console.log(dom.value)
            })
            // #2
            return { dom }
        },
    }
</script>
```

配合 v-for 循环可以获取一组 DOM 或者组件。

```html
<template>
    <ul>
        <!-- #4 -->
        <li v-for="i in 4" :key="i" :ref="setDom">第 {{ i }} li</li>
    </ul>
</template>
<script>
    import { onMounted } from 'vue'
    export default {
        name: 'App',
        setup() {
            // #1
            const domList = []
            // #2
            const setDom = (el) => {
                domList.push(el)
            }
            onMounted(() => {
                // #5
                console.log(domList)
            })
            // #3
            return { setDom }
        },
    }
</script>
```

问题：有数据更新的时候，domList 会越来越多。

```html
<template>
    <ul>
        <li v-for="i in 4" :key="i" :ref="setDom">第 {{ i }} li</li>
    </ul>
    <div>
        <h3>{{ num }}</h3>
        <button @click="handleClick">+1</button>
    </div>
</template>
<script>
    import { onMounted, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const domList = []
            const setDom = (el) => {
                domList.push(el)
            }
            onMounted(() => {
                console.log(domList)
            })

            // 点击计数
            const num = ref(1)
            const handleClick = () => {
                num.value++
                console.log(domList)
            }
            return { setDom, num, handleClick }
        },
    }
</script>
```

解决：onBeforeUpdate 的时候清空 domList 即可。

```html
<template>
    <ul>
        <li v-for="i in 4" :key="i" :ref="setDom">第 {{ i }} li</li>
    </ul>
    <hr />
    <h3>{{ num }}</h3>
    <button @click="handleClick">+1</button>
</template>
<script>
    import { onMounted, ref, onBeforeUpdate } from 'vue'
    export default {
        name: 'App',
        setup() {
            let domList = []
            const setDom = (el) => {
                domList.push(el)
            }
            onMounted(() => {
                console.log(domList)
            })

            onBeforeUpdate(() => (domList = []))
            // 点击计数
            const num = ref(1)
            const handleClick = () => {
                num.value++
                console.log(domList)
            }
            return { setDom, num, handleClick }
        },
    }
</script>
```

🧐 <font color=909090>ref 处理基本数据类型用的是 Object.defineProperty 进行数据劫持，处理复杂数据类型用的是 Proxy（内部借助了 reactive 函数）。</font>

### 小结

根据 ref 获取 v-for 循环的元素时，得到的结果是所有元素还是某一个？

## 🧐 customRef

作用：创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显示控制，[文档](https://v3.cn.vuejs.org/api/refs-api.html#customref)。

<img src="/resource/images/ifer_customref.png"/>

1. 使用 ref 完成双向数据绑定的效果。

```html
<template>
    <input type="text" v-model="keyword" />
    <p>{{ keyword }}</p>
</template>
<script>
    import { ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            let keyword = ref('vue')
            return {
                keyword,
            }
        },
    }
</script>
```

2. customRef 的基本语法。

```html
<template>
    <input type="text" v-model="keyword" />
    <p>{{ keyword }}</p>
</template>
<script>
    import { customRef } from 'vue'
    // 本质是函数，毛坯房、手动挡
    function myRef(value) {
        return customRef(() => {
            return {
                get() {
                    // 读的时候触发，模板中读了 2 次
                    console.log(1)
                    return value
                },
                set(newValue) {
                    // 有人改会触发
                    console.log(newValue)
                },
            }
        })
    }
    export default {
        name: 'App',
        setup() {
            let keyword = myRef('vue')
            return {
                keyword,
            }
        },
    }
</script>
```

3. get/set 的使用。

```html
<template>
    <input type="text" v-model="keyword" />
    <p>{{ keyword }}</p>
</template>
<script>
    import { customRef } from 'vue'
    // 本质是函数，毛坯房
    function myRef(value) {
        return customRef(() => {
            return {
                get() {
                    // 读的时候触发，模板中读了 2 次
                    console.log(1)
                    return value
                },
                set(newValue) {
                    // 有人改会触发
                    // console.log(newValue)
                    // 改了是改了，get 并没有触发
                    value = newValue
                },
            }
        })
    }
    export default {
        name: 'App',
        setup() {
            let keyword = myRef('vue')
            return {
                keyword,
            }
        },
    }
</script>
```

4. track 追踪数据的变化和 trigger 触发视图更新。

```html
<template>
    <input type="text" v-model="keyword" />
    <p>{{ keyword }}</p>
</template>
<script>
    import { customRef } from 'vue'
    // 本质是函数，毛坯房
    function myRef(value) {
        return customRef((track, trigger) => {
            return {
                get() {
                    // 读的时候触发，模板中读了 2 次
                    console.log(1)
                    // 追踪此数据的变化
                    track()
                    return value
                },
                set(newValue) {
                    // 有人改会触发
                    // console.log(newValue)
                    // 改了是改了，get 并没有触发
                    value = newValue
                    // 通知 Vue 重新解析模板
                    trigger()
                },
            }
        })
    }
    export default {
        name: 'App',
        setup() {
            let keyword = myRef('vue')
            return {
                keyword,
            }
        },
    }
</script>
```

5. 进行防抖的处理。

```html
<template>
    <input type="text" v-model="keyword" />
    <p>{{ keyword }}</p>
</template>
<script>
    import { customRef } from 'vue'
    // 本质是函数，毛坯房
    function myRef(value, delay) {
        let timer = null
        return customRef((track, trigger) => {
            return {
                get() {
                    // 读的时候触发，模板中读了 2 次
                    console.log(1)
                    // 追踪此数据的变化
                    track()
                    return value
                },
                set(newValue) {
                    clearTimeout(timer)
                    timer = setTimeout(() => {
                        // 有人改会触发
                        // console.log(newValue)
                        // 改了是改了，get 并没有触发
                        value = newValue
                        // 通知 Vue 重新解析模板
                        trigger()
                    }, delay)
                },
            }
        })
    }
    export default {
        name: 'App',
        setup() {
            let keyword = myRef('vue', 300)
            return {
                keyword,
            }
        },
    }
</script>
```

## 🧐 shallowReactive 和 shallowRef

通过 reactive 和 ref 创建出来的数据都是递归劫持的，如果只想劫持第一层的变化可以使用 shallowReactive 或 shallowRef。

shallowReactive

```html
<template>
    <p>{{ state.age }}</p>
    <p>{{ state.a.b.c.d }}</p>
    <button @click="handleChange">change</button>
</template>

<script>
    import { shallowReactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const state = shallowReactive({
                age: 18,
                a: {
                    b: {
                        c: {
                            d: 'Hello World',
                        },
                    },
                },
            })
            const handleChange = () => {
                // 只有第一层是响应式的，可以通过打印观察到
                // console.log(state);
                // 第一层的更新会影响到后面（注意 state.age 必须在模板当中使用才会触发更新）
                state.age = 19
                // 如果没有上面的代码直接下面这样写界面是不会更新的
                state.a.b.c.d = 'xxx'
            }
            return { state, handleChange }
        },
    }
</script>
```

shallowRef: 如果传入的是基本类型和 ref 没区别，传入的是对象则不是响应式的（不会再借助 reactive 函数了）。

```html
<template>
    <p>{{ state.age }}</p>
    <button @click="handleChange">change</button>
</template>

<script>
    import { shallowRef } from 'vue'
    export default {
        name: 'App',
        setup() {
            const state = shallowRef({
                age: 18,
            })
            const handleChange = () => {
                // state.value.age = 19 // 非响应
                state.value = { age: 19 } // 响应
                // triggerRef(state) // 也可以通过 triggerRef 来主动触发视图更新
            }
            return { state, handleChange }
        },
    }
</script>
```

## 🧐 readonly 和 shallowReadonly

readonly

```html
<template>
    <p>{{ state.name }}</p>
    <button @click="handleClick">click</button>
</template>

<script>
    import { readonly } from 'vue'

    export default {
        name: 'App',
        setup() {
            const origin = {
                name: 'ifer',
            }
            const state = readonly(origin)
            const handleClick = () => {
                state.name = 'xxx'
                console.log(state.name) // 'ifer'
                // 思考和 const 的差异？
                // const 内容可以改，readonly 内容都不可以改
            }
            return { state, handleClick }
        },
    }
</script>
```

## 🧐 toRaw 和 markRaw

### toRaw

返回 reactive 或 readonly 代理的原始对象，对这个原始对象的修改不会引起页面更新。

```html
<template>Hello World</template>
<script>
    import { reactive, readonly, toRaw } from 'vue'
    export default {
        name: 'App',
        setup() {
            const origin = {
                name: 'ifer',
            }
            // origin 和 state1 的关系：state1 是根据 origin 生成的响应式对象，两者的修改会相互影响，但对 origin 的修改不是响应式的
            const state1 = reactive(origin)
            const state2 = readonly(origin)

            console.log(toRaw(state1) === toRaw(state2)) // true
            console.log(toRaw(state1) === origin) // true
        },
    }
</script>
```

### markRaw

readonly 是改都没改，这 markRaw 是改了没有响应式。

a，作用：标记一个对象，使其永远不会再成为响应式对象。

b，场景：有些值不应被设置为响应式的，例如复杂的第三方类库等；当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。

```html
<template>
    <p>{{ state }}</p>
    <button @click="handleClick">click</button>
</template>

<script>
    import { reactive, markRaw } from 'vue'
    export default {
        name: 'App',
        setup() {
            let obj = {
                name: 'ifer',
                age: 18,
            }
            // obj 将不被追踪，无法成为响应式数据
            // obj = markRaw(obj);
            markRaw(obj)
            const state = reactive(obj)
            const handleClick = () => {
                state.name = 'xxx'
            }
            return {
                state,
                handleClick,
            }
        },
    }
</script>
```

## computed

### 目标

掌握 computed 函数的使用。

### 基本

作用：computed 函数用来定义计算属性。

```html
<template>
    <p>firstName: {{ person.firstName }}</p>
    <p>lastName: {{ person.lastName }}</p>
    <p>fullName: {{ person.fullName }}</p>
</template>
<script>
    import { computed, reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const person = reactive({
                firstName: '朱',
                lastName: '逸之',
            })
            person.fullName = computed(() => {
                return person.firstName + ' ' + person.lastName
            })
            // 也可以传入对象，目前和上面等价
            /* person.fullName = computed({
                get() {
                    return person.firstName + ' ' + person.lastName
                },
            }) */
            return {
                person,
            }
        },
    }
</script>
```

### 高级

```html
<template>
    <p>firstName: {{ person.firstName }}</p>
    <p>lastName: {{ person.lastName }}</p>
    <input type="text" v-model="person.fullName" />
</template>
<script>
    import { computed, reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const person = reactive({
                firstName: '朱',
                lastName: '逸之',
            })
            // 也可以传入对象，目前和上面等价
            person.fullName = computed({
                get() {
                    return person.firstName + ' ' + person.lastName
                },
                set(value) {
                    const newArr = value.split(' ')
                    person.firstName = newArr[0]
                    person.lastName = newArr[1]
                },
            })
            return {
                person,
            }
        },
    }
</script>
```

### 小结

-   给 computed 传入函数，返回值就是计算属性的值。

-   给 computed 传入对象，get 获取计算属性的值，set 监听计算属性改变。

## watch

### 目标

掌握 watch 函数的使用。

### 监听一个 ref 数据

```html
<template>
    <p>{{ age }}</p>
    <button @click="age++">click</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const age = ref(18)
            // 监听 ref 数据 age，会触发后面的回调，不需要 .value
            watch(age, (newValue, oldValue) => {
                console.log(newValue, oldValue)
            })

            return { age }
        },
    }
</script>
```

### 监听多个 ref 数据

```html
<template>
    <p>age: {{ age }} num: {{ num }}</p>
    <button @click="handleClick">click</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const age = ref(18)
            const num = ref(0)

            const handleClick = () => {
                age.value++
                num.value++
            }
            // 数组里面是 ref 数据
            watch([age, num], (newValue, oldValue) => {
                console.log(newValue, oldValue)
            })

            return { age, num, handleClick }
        },
    }
</script>
```

### 立即触发监听

```html
<template>
    <p>{{ age }}</p>
    <button @click="handleClick">click</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const age = ref(18)

            const handleClick = () => {
                age.value++
            }

            watch(
                age,
                (newValue, oldValue) => {
                    console.log(newValue, oldValue) // 18 undefined
                },
                {
                    immediate: true,
                }
            )

            return { age, handleClick }
        },
    }
</script>
```

### 开启深度监听

问题：修改 ref 对象里面的数据并不会触发监听，说明 ref 并不是默认开启 deep 的。

```html
<template>
    <p>{{ obj.hobby.eat }}</p>
    <button @click="obj.hobby.eat = '面条'">修改 obj.hobby.eat</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = ref({
                hobby: {
                    eat: '西瓜',
                },
            })
            watch(obj, (newValue, oldValue) => {
                console.log(newValue === oldValue)
            })

            return { obj }
        },
    }
</script>
```

1. 解决：当然直接修改整个对象的话肯定是会被监听到的（注意模板中对 obj 的修改，相当于修改的是 obj.value）。

```html
<template>
    <p>{{ obj.hobby.eat }}</p>
    <button @click="obj = { hobby: { eat: '面条' } }">修改 obj</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = ref({
                hobby: {
                    eat: '西瓜',
                },
            })
            watch(obj, (newValue, oldValue) => {
                console.log(newValue, oldValue)
                console.log(newValue === oldValue)
            })

            return { obj }
        },
    }
</script>
```

2. 解决：开启深度监听 ref 数据。

```js
watch(
    obj,
    (newValue, oldValue) => {
        console.log(newValue, oldValue)
        console.log(newValue === oldValue)
    },
    {
        deep: true,
    }
)
```

### 监听 reactive 数据

#### 基本操作

注意：监听 reactive 数据时，强制开启了深度监听，配置无效；监听对象的时候 newValue 和 oldValue 是全等的。

```html
<template>
    <p>{{ obj.hobby.eat }}</p>
    <button @click="obj.hobby.eat = '面条'">click</button>
</template>

<script>
    import { watch, reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                name: 'ifer',
                hobby: {
                    eat: '西瓜',
                },
            })
            watch(obj, (newValue, oldValue) => {
                // 注意1：监听对象的时候，新旧值是相等的
                // 注意2：强制开启深度监听，配置无效
                console.log(newValue === oldValue) // true
            })

            return { obj }
        },
    }
</script>
```

#### 知识补充

-   想让 ref 内部数据的修改被观测到，除了前面学习的开启深度监听，还可以通过监听 ref.value 来实现同样的效果。

-   因为 ref.value 是一个 reactive，可以通过 isReactive 方法来证明。

```html
<template>
    <p>{{ obj.hobby.eat }}</p>
    <button @click="obj.hobby.eat = '面条'">修改 obj</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = ref({
                hobby: {
                    eat: '西瓜',
                },
            })
            watch(obj.value, (newValue, oldValue) => {
                console.log(newValue, oldValue)
                console.log(newValue === oldValue)
            })

            return { obj }
        },
    }
</script>
```

### 监听普通数据

1. 监听响应式对象中的某一个普通属性值，要通过函数返回的方式进行（如果返回的是对象/响应式对象，修改内部的数据需要开启深度监听）。

```html
<template>
    <p>{{ obj.hobby.eat }}</p>
    <button @click="obj.hobby.eat = '面条'">修改 obj</button>
</template>

<script>
    import { watch, reactive } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                hobby: {
                    eat: '西瓜',
                },
            })
            // 不叫普通属性值，是一个 reactive
            /* watch(obj.hobby, (newValue, oldValue) => {
                console.log(newValue, oldValue)
                console.log(newValue === oldValue)
            }) */
            // 叫普通属性值
            watch(
                () => obj.hobby.eat,
                (newValue, oldValue) => {
                    console.log(newValue, oldValue)
                    console.log(newValue === oldValue)
                }
            )

            return { obj }
        },
    }
</script>
```

2. 监听 ref 数据的另一种写法。

```html
<template>
    <p>{{ age }}</p>
    <button @click="age++">click</button>
</template>

<script>
    import { watch, ref } from 'vue'
    export default {
        name: 'App',
        setup() {
            const age = ref(18)
            // 监听 ref 数据 age，会触发后面的回调，不需要 .value
            /* watch(age, (newValue, oldValue) => {
                console.log(newValue, oldValue);
            }); */
            // 另一种写法，函数返回一个普通值
            watch(
                () => age.value,
                (newValue, oldValue) => {
                    console.log(newValue, oldValue)
                }
            )

            return { age }
        },
    }
</script>
```

### 小结

掌握 watch 的各种用法。

## watchEffect

```html
<template>
    <p>{{ obj.hobby.eat }}</p>
    <button @click="obj.hobby.eat = '面条'">修改 obj</button>
</template>

<script>
    import { reactive, watchEffect } from 'vue'
    export default {
        name: 'App',
        setup() {
            const obj = reactive({
                hobby: {
                    eat: '西瓜',
                },
            })
            // 叫普通属性值
            /* watch(obj, (newValue, oldValue) => {
                console.log(newValue, oldValue)
                console.log(newValue === oldValue)
            }) */

            watchEffect(() => {
                // 1. 不指定监视哪一个，这里面用到了谁就监听谁
                // 2. 第一次的时候肯定会执行
                // 例如对 obj.hobby.eat 的修改，由于这里用到了 obj.hobby.eat，则会执行
                // !注意如果这里用的是 obj 则不会被执行
                console.log(obj.hobby.eat)
            })

            return { obj }
        },
    }
</script>
```

## provide/inject

### 目标

掌握使用 provide 函数和 inject 函数完成跨层级组件通讯。

### 内容

<img src="/resource/images/ifer_provider.png" width="300"/>

📝 把 App.vue 中的数据传递给孙组件，Child.vue。

`App.vue`

```html
<template>
    <div class="container">
        <h2>App {{ money }}</h2>
        <button @click="money = 1000">发钱</button>
        <hr />
        <Parent />
    </div>
</template>
<script>
    import { provide, ref } from 'vue'
    import Parent from './Parent.vue'
    export default {
        name: 'App',
        components: {
            Parent,
        },
        setup() {
            // 提供数据
            const money = ref(100)
            provide('money', money)
            // 提供修改数据的方法
            const changeMoney = (m) => (money.value -= m)
            provide('changeMoney', changeMoney)
            return { money }
        },
    }
</script>
```

`Parent.vue`

```html
<template>
    <div>
        Parent
        <hr />
        <Child />
    </div>
</template>

<script>
    import Child from './Child.vue'
    export default {
        components: {
            Child,
        },
    }
</script>
```

`Child.vue`

```html
<template>
    <div>
        Child
        <p>{{ money }}</p>
        <button @click="changeMoney(1)">花 1 块钱</button>
    </div>
</template>

<script>
    import { inject } from 'vue'
    export default {
        setup() {
            const money = inject('money')
            const changeMoney = inject('changeMoney')
            return { money, changeMoney }
        },
    }
</script>
```

### 小结

## 响应式数据的判断

-   isRef: 检查一个值是否为 ref 对象。

-   isReactive: 检查一个对象是否是由 reactive 创建的响应式代理。

-   isReadonly: 检查一个对象是否是由 readonly 创建的只读代理。

-   isProxy: 检查一个对象是否是由 reactive 或者 readonly 方法创建的代理。

```html
<template>
    <div>name: {{ name }}</div>
    <div>age: {{ age }}</div>
</template>

<script>
    import { reactive, readonly, ref, toRefs, isRef, isReactive, isReadonly, isProxy } from 'vue'
    export default {
        setup() {
            const person = reactive({ name: 'xxx', age: 18 })
            const num = ref(0)
            const readonlyPerson = readonly(person)
            console.log(isRef(num))
            console.log(isReactive(person))
            console.log(isReadonly(readonlyPerson))
            console.log(isProxy(person)) // true
            console.log(isProxy(readonlyPerson)) // true
            return {
                ...toRefs(person),
            }
        },
    }
</script>
```

## setup 函数参数

### 目标

掌握 setup 中参数的使用。

### 需求

<img src="/resource/images/ifer_props2.png"/>

### 父传子

`App.vue`

```html
<template>
    <h1>父组件</h1>
    <p>{{ money }}</p>
    <hr />
    <!-- 1. 父组件通过自定义属性提供数据 -->
    <Son :money="money" />
</template>
<script>
    import { ref } from 'vue'
    import Son from './Son.vue'
    export default {
        name: 'App',
        components: {
            Son,
        },
        setup() {
            const money = ref(100)
            return { money }
        },
    }
</script>
```

`Son.vue`

```html
<template>
    <h1>子组件</h1>
    <p>{{ money }}</p>
</template>
<script>
    export default {
        name: 'Son',
        // 2. 子组件通过 props 进行接收，在模板中就可以使用啦
        props: {
            money: {
                type: Number,
                default: 0,
            },
        },
        setup(props) {
            // 3. setup 中也可以通过形参 props 来获取传递的数据
            console.log(props.money)
        },
    }
</script>
```

### 子传父

<img src="/resource/images/ifer_props3.png"/>

`App.vue`

```html
<template>
    <h1>父组件</h1>
    <p>{{ money }}</p>
    <hr />
    <Son :money="money" @change-money="updateMoney" />
</template>
<script>
    import { ref } from 'vue'
    import Son from './Son.vue'
    export default {
        name: 'App',
        components: {
            Son,
        },
        setup() {
            const money = ref(100)
            // #1 父组件准备修改数据的方法并提供给子组件
            const updateMoney = (newMoney) => {
                money.value -= newMoney
            }
            return { money, updateMoney }
        },
    }
</script>
```

`Son.vue`

```html
<template>
    <h1>子组件</h1>
    <p>{{ money }}</p>
    <button @click="changeMoney(1)">花 1 元</button>
</template>
<script>
    export default {
        name: 'Son',
        props: {
            money: {
                type: Number,
                default: 0,
            },
        },
        emits: ['change-money'],
        setup(props, { emit }) {
            // attrs 捡漏、slots 插槽
            const changeMoney = (m) => {
                // #2 子组件通过 emit 进行触发
                emit('change-money', m)
            }
            return { changeMoney }
        },
    }
</script>
```

### 小结

-   setup 第一个参数的是什么？

-   第二个参数 context 中包含什么信息？

## v-model

### 目标

掌握 Vue3 中 v-model 的用法。

### 基本操作

在 Vue2 中 v-mode 语法糖简写的代码。

```html
<Son :value="msg" @input="msg=$event" />
```

在 Vue3 中 v-model 语法糖有所调整。

```html
<Son :modelValue="msg" @update:modelValue="msg=$event" />
```

`App.vue`

```html
<template>
    <h2>count: {{ count }}</h2>
    <hr />
    <Son :modelValue="count" @update:modelValue="count = $event" />
    <!-- <Son v-model="count" /> -->
</template>
<script>
    import { ref } from 'vue'
    import Son from './Son.vue'
    export default {
        name: 'App',
        components: {
            Son,
        },
        setup() {
            const count = ref(10)
            return { count }
        },
    }
</script>
```

`Son.vue`

```html
<template>
    <h2>子组件 {{ modelValue }}</h2>
    <button @click="$emit('update:modelValue', 100)">改变 count</button>
</template>
<script>
    export default {
        name: 'Son',
        props: {
            modelValue: {
                type: Number,
                default: 0,
            },
        },
    }
</script>
```

### 传递多个

`App.vue`

```html
<template>
    <h2>count: {{ count }} age: {{ age }}</h2>
    <hr />
    <Son v-model="count" v-model:age="age" />
</template>
<script>
    import { ref } from 'vue'
    import Son from './Son.vue'
    export default {
        name: 'App',
        components: {
            Son,
        },
        setup() {
            const count = ref(10)
            const age = ref(18)
            return { count, age }
        },
    }
</script>
```

```html
<template>
    <h2>子组件 {{ modelValue }} {{ age }}</h2>
    <button @click="$emit('update:modelValue', 100)">改变 count</button>
    <button @click="$emit('update:age', 19)">改变 age</button>
</template>
<script>
    export default {
        name: 'Son',
        props: {
            modelValue: {
                type: Number,
                default: 0,
            },
            age: {
                type: Number,
                default: 18,
            },
        },
    }
</script>
```

### 小结

## Fragment

-   Vue2 中组件必须有一个跟标签。

-   Vue3 中组件可以没有根标签，其内部会将多个标签包含在一个 Fragment 虚拟元素中。

-   好处：减少标签层级和内存占用。

## Teleport

### 作用

传送，能将特定的 HTML 结构（一般是嵌套很深的）移动到指定的位置，解决 HTML 结构嵌套过深造成的样式影响或不好控制的问题。

### 需求

在 Child 组件点击按钮进行弹框。

<img src="/resource/images/teleport.png" width="300"/>

```html
<template>
    <div class="child">
        <dialog v-if="bBar" />
        <button @click="handleDialog">显示弹框</button>
    </div>
</template>

<script>
    import { ref } from 'vue'
    import Dialog from './Dialog.vue'
    export default {
        name: 'Child',
        components: {
            Dialog,
        },
        setup() {
            const bBar = ref(false)
            const handleDialog = () => {
                bBar.value = !bBar.value
            }
            return {
                bBar,
                handleDialog,
            }
        },
    }
</script>
```

### 解决

```html
<template>
    <div class="child">
        <teleport to="body">
            <dialog v-if="bBar" />
        </teleport>
        <button @click="handleDialog">显示弹框</button>
    </div>
</template>
```

## Suspense

异步组件加载期间，可以使用此组件渲染一些额外的内容，增强用户体验。

### 异步组件

```html
<template>
    <div class="app">
        App
        <Test />
    </div>
</template>

<script>
    // 静态引入 => 等待所有子组件加载完再统一渲染
    // import Test from './Test.vue'
    // 动态/异步引入
    import { defineAsyncComponent } from 'vue'
    const Test = defineAsyncComponent(() => import('./Test.vue'))
    export default {
        name: 'App',
        components: {
            Test,
        },
    }
</script>
```

### 优化代码

```html
<template>
    <div class="app">
        App
        <Suspense>
            <template v-slot:default>
                <Test />
            </template>
            <template v-slot:fallback>
                <div>loading...</div>
            </template>
        </Suspense>
    </div>
</template>

<script>
    // 静态引入 => 等待所有子组件加载完再统一渲染
    // import Test from './Test.vue'
    // 动态/异步引入
    import { defineAsyncComponent } from 'vue'
    const Test = defineAsyncComponent(() => import('./Test.vue'))
    export default {
        name: 'App',
        components: {
            Test,
        },
    }
</script>
```

### 一个细节

setup 也可以返回一个 Promise 实例，但要异步引入此组件并配合 Suspense 使用。

```html
<template>
    <div class="test">Test</div>
</template>

<script>
    import { ref } from 'vue'
    export default {
        name: 'Test',
        /* setup() {
            const count = ref(0)
            // 也可以返回 Promise 实例，但要异步引入此组件并配合 Suspense 使用
            return new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve({ count })
            }, 3000)
            })
        }, */
        async setup() {
            const count = ref(0)
            return await new Promise((resolve, reject) => {
                setTimeout(() => {
                    resolve({ count })
                }, 3000)
            })
        },
    }
</script>
```

## script setup

[文档](https://v3.cn.vuejs.org/api/sfc-script-setup.html#%E5%9F%BA%E6%9C%AC%E8%AF%AD%E6%B3%95)

### data

```html
<script setup>
    import { reactive, toRefs } from 'vue'
    const state = reactive({
        name: 'ifer',
        age: 18,
    })
    const { name, age } = toRefs(state)
</script>
<template>
    <h1>name: {{ name }} age: {{ age }}</h1>
</template>
```

### method

```html
<script setup>
    import { reactive, toRefs } from 'vue'
    const state = reactive({
        name: 'ifer',
        age: 18,
    })
    const { name, age } = toRefs(state)

    // 修改名字
    const changeName = () => {
        name.value = 'xxx'
    }
</script>
<template>
    <h1>name: {{ name }} age: {{ age }}</h1>
    <button @click="changeName">修改名字</button>
</template>
```

### computed

```html
<script setup>
    import { reactive, computed, isRef } from 'vue'
    const state = reactive({
        firstName: '热',
        lastName: '巴',
    })
    const fullName = computed(() => state.firstName + state.lastName)
    console.log(isRef(fullName)) // true
</script>
<template>
    <h1>fullName: {{ fullName }}</h1>
</template>
```

### watch

```html
<script setup>
    import { ref, watch } from 'vue'
    const count = ref(0)
    watch(count, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
<template>
    <h1>count: {{ count }}</h1>
    <button @click="count++">+1</button>
</template>
```

### props

父组件

```html
<script setup>
    import { reactive } from 'vue'
    import Hello from './Hello.vue'
    const person = reactive({
        name: 'ifer',
        age: 18,
    })
</script>
<template>
    <Hello v-bind="person" />
</template>
```

子组件

```html
<script setup>
    // defineProps 无需引用，可以在 script setup 中直接使用
    const props = defineProps({
        name: String,
        age: Number,
    })
</script>
<template>
    <div>name: {{ props.name }} age: {{ age }}</div>
</template>
```

### emit

父组件

```html
<script setup>
    import { reactive } from 'vue'
    import Hello from './Hello.vue'
    const person = reactive({
        name: 'ifer',
        age: 18,
    })
    const updateAge = () => {
        person.age++
    }
</script>
<template>
    <Hello v-bind="person" @updateAge="updateAge" />
</template>
```

子组件

```html
<script setup>
    // defineProps 无需引用，可以在 script setup 中直接使用
    const props = defineProps({
        name: String,
        age: Number,
    })
    const emit = defineEmits(['updateAge'])

    const updateAge = () => {
        emit('updateAge')
    }
</script>
<template>
    <div>name: {{ props.name }} age: {{ age }}</div>
    <button @click="emit('updateAge')">update name</button>
    <button @click="$emit('updateAge')">update name</button>
    <button @click="updateAge">update name</button>
</template>
```

### v-model

父组件

```html
<script setup>
    import { reactive } from 'vue'
    import Hello from './Hello.vue'
    const person = reactive({
        name: 'ifer',
        age: 18,
    })
</script>
<template>
    <Hello v-model="person.name" v-model:age="person.age" />
</template>
```

子组件

```html
<script setup>
    // defineProps 无需引用，可以在 script setup 中直接使用
    const props = defineProps({
        modelValue: String,
        age: Number,
    })
    const emit = defineEmits(['update:modelValue', 'update:age'])

    const updateName = () => {
        emit('update:modelValue', 'xxx')
    }
    const updateAge = () => {
        emit('update:age', 20)
    }
</script>
<template>
    <div>name: {{ props.modelValue }} age: {{ age }}</div>
    <button @click="updateName">update name</button>
    <button @click="updateAge">update age</button>
</template>
```

### defineExpose

-   标准组件写法中，父组件通过 ref 拿到子组件实例，并可以直接访问子组件中的 data 和 method。

-   script-setup 模式下，data 和 method 默认只能给当前组件的 template 使用，外界通过 ref 无法访问到。

-   解决：需要手动的通过 defineExpose 进行暴露。

父组件

```html
<script setup>
    import { ref, nextTick } from 'vue'
    import Hello from './Hello.vue'
    const childRef = ref(null)
    nextTick(() => {
        childRef.value.updatePerson('xxx', 20)
    })
</script>
<template>
    <Hello ref="childRef" />
</template>
```

子组件

```html
<script setup>
    import { reactive } from 'vue'
    const person = reactive({
        name: 'ifer',
        age: 18,
    })
    const updatePerson = (name, age) => {
        person.name = name
        person.age = age
    }
    // 注意是 defineExpose，不要打成 defineProps 了
    defineExpose({
        updatePerson,
    })
</script>
<template>
    <h2>name: {{ person.name }} age: {{ person.age }}</h2>
</template>
```

### slot

父组件

```html
<script setup>
    import Hello from './Hello.vue'
</script>
<template>
    <Hello>
        <!-- 默认插槽 -->
        <h2>默认插槽</h2>
        <!-- 具名插槽 -->
        <template #title>
            <h2>具名插槽</h2>
        </template>
        <!-- 作用域插槽 -->
        <template #footer="{ person }">
            <h2>通过作用域插槽获取到的数据：{{ person.name }}</h2>
        </template>
    </Hello>
</template>
```

子组件

```html
<script setup>
    import { reactive, useSlots } from 'vue'
    const slots = useSlots()
    const person = reactive({
        name: 'ifer',
        age: 18,
    })
    // 可以拿到插槽相关的信息
    console.log(slots)
</script>
<template>
    <slot />
    <slot name="title" />
    <slot name="footer" :person="person" />
</template>
```

### CSS 变量注入

```html
<script setup>
    import { reactive } from 'vue'
    const state = reactive({
        color: 'pink',
    })
</script>
<template>
    <h2>Hello Vue3</h2>
</template>
<style scoped>
    h2 {
        /* 可以使用 v-bind 绑定变量 */
        color: v-bind('state.color');
    }
</style>
```

### 原型绑定与组件使用

main.js

```js
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App)
app.config.globalProperties.year = '再见 2021，你好 2022~~'
app.mount('#app')
```

`App.vue`

```html
<script setup>
    import { getCurrentInstance } from 'vue'
    const { proxy } = getCurrentInstance()
</script>
<template>
    <h1>{{ proxy.year }}</h1>
</template>
```

### 对 await 支持

```html
<script setup>
    const r = await fetch('https://autumnfish.cn/api/joke')
    const d = await r.text()
    console.log(d)
</script>
<template>
    <h1>{{ proxy.year }}</h1>
</template>
```

### 定义组件的 name

```html
<template>
    <div>Hello</div>
</template>
<script setup></script>

<script>
    export default {
        name: 'HelloCmp',
    }
</script>
```

### mixins

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能，一个混入对象可以包含任意组件选项，当组件使用混入对象时，所有混入对象的选项将被“混合”进该组件本身。

```js
// Vue2 写法
Vue.mixin({})
```

```js
// Vue3 写法
app.mixin({})
```

`follow.js`

```js
export const follow = {
    data() {
        return {
            loading: false,
        }
    },
    methods: {
        followFn() {
            this.loading = true
            // 模拟请求
            setTimeout(() => {
                // 省略请求代码
                this.loading = false
            }, 2000)
        },
    },
}
```

`App.vue`

```html
<template>
    <a href="javascript:;" @click="followFn">{{ loading ? '请求中...' : '关注' }}</a>
    <Son />
</template>
<script>
    import Son from './Son.vue'
    import { follow } from './follow'
    export default {
        name: 'App',
        components: {
            Son,
        },
        mixins: [follow],
    }
</script>
```

`Son.vue`

```html
<template>
    <a href="javascript:;" @click="followFn">{{ loading ? '请求中...' : '关注' }}</a>
</template>
<script>
    import { follow } from './follow'
    export default {
        name: 'Son',
        mixins: [follow],
    }
</script>
```

### 小结

## 其他变更

[参考 Vue3 迁移指南](https://v3.cn.vuejs.org/guide/migration/introduction.html#%E6%A6%82%E8%A7%88)

1. 全局 API 的变更，[链接](https://v3.cn.vuejs.org/guide/migration/introduction.html#%E5%85%A8%E5%B1%80-api)。

2. data 只能是函数，[链接](https://v3.cn.vuejs.org/guide/migration/data-option.html#%E6%A6%82%E8%A7%88)。

3. 自定义指令 API 和组件保持一致，[链接](https://v3.cn.vuejs.org/guide/migration/custom-directives.html#_2-x-%E8%AF%AD%E6%B3%95)。

4. keyCode 作为 v-on 修饰符被移除、移除 v-on.native 修饰符、filters 被移除，[链接](https://v3.cn.vuejs.org/guide/migration/introduction.html#%E7%A7%BB%E9%99%A4-api)。

5. \$on、\$off、\$once 被移除，[链接](https://v3.cn.vuejs.org/guide/migration/events-api.html#%E6%A6%82%E8%A7%88)。

6. 过渡类名的更改，[链接](https://v3.cn.vuejs.org/guide/migration/transition.html#%E6%A6%82%E8%A7%88)。

7. ...
