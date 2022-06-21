# pinia文档通读

![../../assets/Code/Vue/pinia.jpg](../../assets/Code/Vue/pinia.jpg)

**吃菠萝**

翻译解读官网中的指南文档。

所有代码片段均引自官网： [https://pinia.vuejs.org](https://pinia.vuejs.org/)

# 如何创建一个小菠萝

使用小菠萝本体提供的defineStore方法来创建实例仓库

方法的第一个参数，你需要给你的仓库取一个独一无二的名字，它在后面会发挥某些作用。

约定俗成的规则是：定义的仓库通常用use...开头命名

```jsx
import { defineStore } from 'pinia'

// useStore could be anything like useUser, useCart
// the first argument is a unique id of the store across your application
export const useStore = defineStore('main', {
  // other options...
})
```

定义一个仓库后只有在setup中引用了才会被创建

```jsx
import { useStore } from '@/stores/counter'

export default {
  setup() {
    const store = useStore()

    return {
      // you can return the whole store instance to use it in the template
      store,
    }
  },
}
```

和setup中的props一样，小菠萝的store中储存的数据同样不能直接解构使用

详情见 [https://staging-cn.vuejs.org/api/composition-api-setup.html#accessing-props](https://staging-cn.vuejs.org/api/composition-api-setup.html#accessing-props)

```jsx
export default defineComponent({
  setup() {
    const store = useStore()
    // ❌ This won't work because it breaks reactivity
    // it's the same as destructuring from `props`
    const { name, doubleCount } = store

    name // "eduardo"
    doubleCount // 2

    return {
      // will always be "eduardo"
      name,
      // will always be 2
      doubleCount,
      // this one will be reactive
      doubleValue: computed(() => store.doubleCount),
      }
  },
})
```

好在官方提供了一个方法，用它包裹一下仓库的实例对象后，就可以正常解构使用数据，并时常保持响应性

仓库中的action定义的方法解构不受限制，官网是这么说的： Note you can destructure actions directly from the store as they are bound to the store itself too

```jsx
import { storeToRefs } from 'pinia'

export default defineComponent({
  setup() {
    const store = useStore()
    // `name` and `doubleCount` are reactive refs
    // This will also create refs for properties added by plugins
    // but skip any action or non reactive (non ref/reactive) property
    const { name, doubleCount } = storeToRefs(store)
    // the increment action can be just extracted
    const { increment } = store

    return {
      name,
      doubleCount
      increment,
    }
  },
})
```

# 小菠萝state

一般state定义为一个函数。

```jsx
import { defineStore } from 'pinia'

const useStore = defineStore('storeId', {
  // arrow function recommended for full type inference
  state: () => {
    return {
      // all these properties will have their type inferred automatically
      counter: 0,
      name: 'Eduardo',
      isAdmin: true,
    }
  },
})
```

小菠萝支持直接在页面操作仓库实例上的属性从而修改state中相应的值。👍

```jsx
const store = useStore()
// it's legal !!!!
store.counter++
```

# 有无setup() 使用pinia的方法

小菠萝官方用两个例子阐述了在vue3的 setup及vue2的选项式组件中如何使用仓库 （暗（ming）示了vue3为正统）

总体来说组合式api中使用起来顺手得多，但是如果因为某些不可抗拒的原因一定要在选项式组件使用小菠萝，它也提供了丰富的类vuex钩子供你使用。

```jsx
import { useCounterStore } from '../stores/counterStore'

export default {
  setup() {
    const counterStore = useCounterStore()

    return { counterStore }
  },
  computed: {
    tripleCounter() {
      return this.counterStore.counter * 3
    },
  },
}
```

```jsx
import { mapState } from 'pinia'
import { useCounterStore } from '../stores/counterStore'

export default {
  computed: {
    // gives access to this.counter inside the component
    // same as reading from store.counter
    ...mapState(useCounterStore, ['counter'])
    // same as above but registers it as this.myOwnName
    ...mapState(useCounterStore, {
      myOwnName: 'counter',
      // you can also write a function that gets access to the store
      double: store => store.counter * 2,
      // it can have access to `this` but it won't be typed correctly...
      magicValue(store) {
        return store.someGetter + this.counter + this.double
      },
    }),
  },
}
```

官方还写了一个有关without setup能直接写入改变state值的奇技淫巧，这里就不引入了。

# 使用$patch()修改state

除了直接修改仓库里的值，小菠萝还提供了专用的方法来修改值，并且支持一次传入多个值同时修改

```jsx
store.$patch({
  counter: store.counter + 1,
  name: 'Abalam',
})
```

同时，有些值的修改涉及复杂的运算，并不适合使用对象的形式传递。这个方法也支持使用回调函数传递修改数值。

官方信誓旦旦的保证这个方法是经过优化的，至少清楚的是，利用这种方法传递多个属性值的修改，最终只会在仓库端触发一次修改记录。（相比直接写，有多少数据修改了就有多少次记录）

```jsx
cartStore.$patch((state) => {
  state.items.push({ name: 'shoes', quantity: 1 })
  state.hasChanged = true
})
```

# 监听state的变化

官方也提供了一个方法来监听小菠萝仓库数据的变化，并且特别说明，相较于普通的watch侦听器，这个特供方法在使用$patch方法传递修改后只会触发一次。

```jsx
cartStore.$subscribe((mutation, state) => {
  // import { MutationType } from 'pinia'
  mutation.type // 'direct' | 'patch object' | 'patch function'
  // same as cartStore.$id
  mutation.storeId // 'cart'
  // only available with mutation.type === 'patch object'
  mutation.payload // patch object passed to cartStore.$patch()

  // persist the whole state to the local storage whenever it changes
  localStorage.setItem('cart', JSON.stringify(state))
})
```

# 小菠萝getters

getters作为小菠萝仓库的第二个重要组成部分，其使用方式与vue中的计算属性十分相似。

一般来说，getter依赖于一个state中的值来做计算。

```jsx
export const useStore = defineStore('main', {
  state: () => ({
    counter: 0,
  }),
  getters: {
    doubleCount: (state) => state.counter * 2,
  },
})
```

# getters数据推断

一般讲，getter依赖state中的值来进行计算，那么它的结果数据类型是能被自动推断出来的。

如果你有使用ts的话，在一个计算属性依赖了另一个计算属性时，需要你手动为其添加数据类型。

当然如果没有使用ts，利用js的@param注解也能使计算属性自动推断数据类型

```jsx
export const useStore = defineStore('main', {
  state: () => ({
    counter: 0,
  }),
  getters: {
    // automatically infers the return type as a number
    doubleCount(state) {
      return state.counter * 2
    },
    // the return type **must** be explicitly set
    doublePlusOne(): number {
      // autocompletion and typings for the whole store ✨
      return this.doubleCount + 1
    },
  },
})
```

# 使用getters时传入参数

一般来说计算属性时不能够接收参数的（state除外）。但是如果某个奇特的需求需要引用计算属性值且该值依赖使用方传入的一个参数的话，也是有办法的。

同vue的cpmuted一样，在计算属性返回数据时改成返回一个函数，这个函数可以接收任何你传入的参数，在函数类进行你的处理最终返回出需要的计算值。

```jsx
export const useStore = defineStore('main', {
  getters: {
    getUserById: (state) => {
      return (userId) => state.users.find((user) => user.id === userId)
    },
  },
})
```

一旦getters返回一个函数后，它将失去缓存的功能，成为一个普普通通的计算得出数据的函数。

但是你仍然可以在getter之中缓存其他结果（在return函数之前）。

你的函数可以依赖这个结果进行计算。

```jsx
export const useStore = defineStore('main', {
  getters: {
    getActiveUserById(state) {
      const activeUsers = state.users.filter((user) => user.active)
      return (userId) => activeUsers.find((user) => user.id === userId)
    },
  },
})
```

# 使用其他仓库的getters

使用其他仓库的getters时也很便捷。就如同在组件中使用一样，直接引入其他仓库，实例化后就能调用了。

```jsx
import { useOtherStore } from './other-store'

export const useStore = defineStore('main', {
  state: () => ({
    // ...
  }),
  getters: {
    otherGetter(state) {
      const otherStore = useOtherStore()
      return state.localData + otherStore.data
    },
  },
})
```

最后小菠萝官方同样对比了有无setup()使用仓库的方法。和state基本一样这里就不阐述了。

值得注意的是，小菠萝使用useState()这个钩子同时处理state与getters中的数据，没有vuex中专门为计算属性设计的mapGetters了。（其实发现state和getter的本质没什么区别🍍）

# 小菠萝actions

actions就等同于组件中的methods，用来存放一些处理赋值业务的函数方法。

actions和getters一样，可以通过 this 访问当前仓库的实例来获取完整的数据类型推断支持

同时，小菠萝放弃了Mutations，这说明任何同步异步的方法都将在actions中创建。（vuex中actions就能同时支持同异步了，我也是无法理解为什么要单弄出两个区域来 ）

```jsx
import { mande } from 'mande'

const api = mande('/api/users')

export const useUsers = defineStore('users', {
  state: () => ({
    userData: null,
    // ...
  }),

  actions: {
    async registerUser(login, password) {
      try {
        this.userData = await api.post({ login, password })
        showTooltip(`Welcome back ${this.userData.name}!`)
      } catch (error) {
        showTooltip(error)
        // let the form component display the error
        return error
      }
    },
  },
})
```

action在组件中的使用方法

```jsx
export default defineComponent({
  setup() {
    const main = useMainStore()
    // call the action as a method of the store
    main.randomizeCounter()

    return {}
  },
})
```

# 使用其他仓库中的actions

小菠萝引用其他仓库的的方式几乎一样，这里不再展开叙述了

```jsx
import { useAuthStore } from './auth-store'

export const useSettingsStore = defineStore('settings', {
  state: () => ({
    preferences: null,
    // ...
  }),
  actions: {
    async fetchUserPreferences() {
      const auth = useAuthStore()
      if (auth.isAuthenticated) {
        this.preferences = await fetchPreferences()
      } else {
        throw new Error('User must be authenticated')
      }
    },
  },
})
```

# 监听actions

小菠萝的仓库实例提供一个方法监听actions。

传递给它的回调函数会在action方法执行之前被调用。

回调函数中存在两个钩子： after和 onError 。

after 传入一个回调函数，他会在监听的函数执行完成之后调用。

onError 则是用来处理异常错误的

如果action为promise，上述两个钩子也会在resolve 或 reject之后调用

```jsx
const unsubscribe = someStore.$onAction(
  ({
    name, // name of the action
    store, // store instance, same as `someStore`
    args, // array of parameters passed to the action
    after, // hook after the action returns or resolves
    onError, // hook if the action throws or rejects
  }) => {
    // a shared variable for this specific action call
    const startTime = Date.now()
    // this will trigger before an action on `store` is executed
    console.log(`Start "${name}" with params [${args.join(', ')}].`)

    // this will trigger if the action succeeds and after it has fully run.
    // it waits for any returned promised
    after((result) => {
      console.log(
        `Finished "${name}" after ${
          Date.now() - startTime
        }ms.\nResult: ${result}.`
      )
    })

    // this will trigger if the action throws or returns a promise that rejects
    onError((error) => {
      console.warn(
        `Failed "${name}" after ${Date.now() - startTime}ms.\nError: ${error}.`
      )
    })
  }
)

// manually remove the listener
unsubscribe()
```

#