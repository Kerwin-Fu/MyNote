---
title: 13_TS 基础
date: 2021-12-07 21:16:55
tags:
---

## 今日目标

✔ 掌握 TypeScript 的基本使用。

<!-- more -->

## TypeScript 是什么

### 目标

能够说出什么是 TypeScript。

### 内容

![TS是JS的超集](/resource/images/TS是JS的超集.png)

-   [TS 官方文档](https://www.typescriptlang.org/)，[TS 中文参考，不再维护](https://www.tslang.cn/)。

-   TypeScript 简称 TS，是 JavaScript 的超集，简单来说就是就是 JS 有的 TS 都有。

-   TypeScript = `Type` + JavaScript（在 JS 基础之上，为 JS 添加了类型支持/类型检测）。

-   TypeScript 是微软开发的开源编程语言，可以在任何运行 JavaScript 的地方运行。

```ts
// TS 代码 => 有明确的类型，即 number（数值类型）
let age1: number = 18
// JS 代码 => 无明确的类型
let age2 = 18
```

## 为什么要有 TypeScript

### 目标

能够说出为什么需要有 TypeScript。

### 内容

-   背景：JS 的类型系统存在“先天缺陷”，是弱类型语言，而代码中的大部分错误都是类型错误（TypeError）。

-   这些经常出现的错误，导致了在使用 JS 进行项目开发时，增加了找 Bug、改 Bug 的时间，严重影响开发效率。

-   从编程语言的动静来区分，**TypeScript 属于静态类型的编程语言**，**JavaScript 属于动态类型的编程语言**。

    a，静态类型：**编译**期做类型检查。

    b，动态类型：**执行**期做类型检查。

-   对于 JS 来说：需要等到代码真正去执行的时候才能发现错误（晚）。

-   对于 TS 来说：在代码编译的时候（代码执行前）就可以发现错误（早）。

-   并且，配合 VSCode 等开发工具，<font color=e32d40>**发现错误的时机 TS 可以提前到在编写代码的时候**</font>，减少找 Bug、改 Bug 时间。

```js
let num = 123
num = 'abc'
num.toFixed(2) // Uncaught TypeError: num.toFixed is not a function
```

### 对比

-   使用 JavaScript。

    1. 在 VSCode 里面写代码。

    2. 在浏览器中运行代码时 --> 才会发现错误【晚】。

-   使用 TypeScript。

    1. 在 VSCode 里面写代码 --> 写代码的同时，就会发现错误【早】。

    2. 在浏览器中运行代码。

## TS 相比 JS 的优势

### 目标

能够说出 TS 相比 JS 有哪些优势。

### 内容

1. <font color=e32d40>**更早（写代码的同时）发现错误**</font>，减少找 Bug、改 Bug 时间，提升开发效率。

2. 程序中任何位置的代码都有<font color=e32d40>**代码提示**</font>，随时随地的安全感，增强了开发体验。

3. 强大的类型系统提升了代码的可维护性，使得<font color=e32d40>**重构代码更加容易**</font>。

4. 支持最新的 ECMAScript 语法，<font color=e32d40>**优先体验最新的语法，让你走在前端技术的最前沿**</font>。

5. TS 类型推断机制，不需要在代码中的每个地方都显示标注类型，让你在享受优势的同时，尽量降低了学习负担。

6. Vue3 源码使用 TS 重写、Angular 默认支持 TS、React 与 TS 完美配合，TypeScript 已成为大中型前端项目的首选编程语言。

7. 目前，前端最新的开发技术栈：React（TS + Hooks），Vue（TS + Vue3）。

## 安装编译 TS 的工具包

### 目标

能够安装 TS 的工具包来编译 TS。

### 内容

-   问题：为什么要安装编译 TS 的工具包？

-   回答：Node.js/浏览器，只认识 JS 代码，不认识 TS 代码，因此需要先将 TS 代码转化为 JS 代码，然后才能运行。

-   安装命令：`npm i -g typescript` 或者 `yarn global add typescript`。

    a，TypeScript 包：用来编译 TS 代码的包，提供了 `tsc` 命令，实现了 TS -> JS 的转化。

    b，注意：Mac 电脑安装全局包时，需要添加 `sudo` 获取权限，`sudo npm i -g typescript` 或 `sudo yarn global add typescript`。

-   验证是否安装成功：tsc –v（查看 TypeScript 的版本）。

![TS 编译](/resource/images/TS编译.png)

## 编译并运行 TS 代码

### 目标

能够理解 TypeScript 的运行步骤。

### 内容

1. 创建 `hello.ts` 文件（注意：TS 文件的后缀名为 `.ts`）。

2. 将 TS 编译为 JS：在终端中输入命令，`tsc hello.ts`（此时，在同级目录中会出现一个同名的 JS 文件）。

3. 执行 JS 代码：在终端中输入命令，`node hello.js`。

4. 说明：所有合法的 JS 代码都是 TS 代码，有 JS 基础只需要学习 TS 的类型即可。

5. 注意：由 TS 编译生成的 JS 文件，代码中就没有类型信息了。

## 简化运行 TS 的步骤

### 目标

能够通过 `ts-node` 包来简化 TS 的运行。

### 内容

-   问题描述：每次修改代码后，都要重复执行两个命令，才能运行 TS 代码，太繁琐。

-   简化方式：使用 `ts-node` 包，直接在 Node.js 中执行 TS 代码。

-   安装命令：`npm i -g ts-node`。

    a，ts-node 包提供了 `ts-node` 命令。

    b，使用方式：`ts-node hello.ts`（注意：ts-node 不会生成 JS 文件）。

-   解释说明：`ts-node` 命令在内部偷偷的将 TS -> JS，然后，再运行 JS 代码。

-   其他方法：VSCode 中也可以安装 `Code Runner` 插件来直接运行 TS 代码。

## TypeScript 常用类型

### 类型注解

#### 目标

能够理解什么是 TypeScript 的类型注解。

#### 内容

-   TypeScript 是 JS 的超集，TS 提供了 JS 的所有功能，并且额外的增加了：<font color=e32d40>**类型系统**</font>。

    a，所有的 JS 代码都是 TS 代码。

    b，JS 虽然也有类型（比如，number/string 等），但是 JS 不会检查变量的类型是否发生变化，而 TS 会检查。

-   TypeScript 类型系统的主要优势：**可以显式标记出代码中的意外行为，从而降低了发生错误的可能性**。

    ```ts
    let age: number = 18
    ```

-   说明：代码中的 `:number` 就是<font color=e32d40>**类型注解**</font>。

-   作用：**为变量添加类型约束**，比如上述代码中，约定变量 age 的类型为 number 类型。

-   解释：**约定了什么类型，就只能给变量赋值该类型的值，否则，就会报错**。

    ```ts
    // 错误原因：将 string 类型的值赋值给了 number 类型的变量，类型不一致
    let age: number = '18'
    ```

-   约定了类型之后，代码的提示也会非常的清晰。

### 原始类型

#### 目标

能够理解 TS 中原始类型的使用。

#### 内容

可以将 TS 中的常用基础类型细分为两类，分别是 JS 已有类型和 TS 新增类型。

-   JS 已有类型。

    a，原始类型：`number/string/boolean/null/undefined/symbol/bigint`。

    b，对象类型：`object`（包括，数组、对象、函数等对象）。

-   TS 新增类型。

    a，联合类型、自定义类型（类型别名）、接口、元组、字面量类型、枚举、void、any 等。

    b，注意：原始类型在 TS 和 JS 中写法一致；对象类型在 TS 中更加细化，每个具体的对象（比如数组、对象、函数）都有自己的类型语法。

```ts
let age: number = 18
let myName: string = 'Ifer'
let isLoading: boolean = false
// ...
```

### 数组类型

#### 目标

掌握 TS 中数组类型的两种写法。

#### 内容

```ts
// 写法 1
let numbers: number[] = [1, 3, 5]
```

```ts
// 写法 2
let strings: Array<string> = ['a', 'b', 'c']
strings.push('d') // 后续 push 的数据也必须是字符串
```

### 联合类型

#### 目标

能够通过联合类型将多个类型组合成一个类型。

#### 内容

-   需求：数组中既有 number 类型，又有 string 类型，这个数组的类型应该如何写？

    ```jsx
    // 定义一个数组，数组中可以有数字或者字符串, 需要注意 | 的优先级
    let arr: (number | string)[] = [1, 'abc', 2]
    ```

-   解释：`|`（竖线）在 TS 中叫做联合类型，即由两个或多个其他类型组成的类型，表示可以是这些类型中的一种。

-   注意：这是 TS 中联合类型的语法，只有一根竖线，不要与 JS 中的或（||）混淆了。

-   场景：定时器的初始变量定义。

    ```ts
    let timer: number | null = null
    timer = setInterval(() => {}, 1000)
    ```

### 类型别名

#### 目标

能够使用类型别名给类型起别名。

#### 内容

-   类型别名作用：为任意类型起别名，别名甚至可以是中文。

    ```ts
    type s = string
    const myName: s = 'ifer'

    type 字符串类型 = string
    const myAddress: 字符串类型 = '河南老乡~'
    ```

-   使用场景：当同一类型（复杂）且可能被多次使用时，可以通过类型别名，**简化该类型的使用**。

    ```ts
    type CustomArray = (number | string)[]

    let arr1: CustomArray = [1, 'a', 3, 'b']
    let arr2: CustomArray = ['x', 'y', 6, 7]
    ```

-   解释说明。

    a，使用 `type` 关键字来创建自定义类型。

    b，类型别名（比如，此处的 CustomArray）可以是任意合法的变量名称。

    c，推荐使用大写字母开头。

    d，创建类型别名后，直接使用该类型别名作为变量的类型注解即可。

### 函数类型-基本使用

#### 目标

能够给函数指定类型。

#### 内容

函数的类型实际上指的是：`函数参数` 和 `返回值` 的类型，为函数指定类型有如下两种方式。

-   单独指定参数、返回值的类型。

    ```ts
    // 函数声明
    function add(num1: number, num2: number): number {
        return num1 + num2
    }

    // 箭头函数
    const add = (num1: number, num2: number): number => {
        return num1 + num2
    }
    ```

-   同时指定参数、返回值的类型。

    ```ts
    // 解释：可以通过类似箭头函数形式的语法来为函数添加类型，注意这种形式只适用于函数表达式。
    type AddFn = (num1: number, num2: number) => number

    const add: AddFn = (num1, num2) => {
        return num1 + num2
    }
    ```

### 函数类型-void 类型

#### 目标

能够了解 void 类型的使用。

#### 内容

-   如果函数没有返回值，那么函数返回值类型为：`void`。

```ts
function greet(name: string): void {
    console.log('Hello', name)
}
```

-   注意：如果一个函数没有返回值，此时，在 TS 的类型中，应该使用 `void` 类型。

```ts
// 如果什么都不写，此时，add 函数的返回值类型为：void
const add = () => {}
// 这种写法是明确指定函数返回值类型为 void，与上面不指定返回值类型相同
const add = (): void => {}
// 但，如果指定返回值类型为 undefined，此时，函数体中必须显示的 return undefined 才可以
const add = (): undefined => {
    // 此处，返回的 undefined 是 JS 中的一个值
    return undefined
}
```

### 函数类型-可选参数

#### 目标

能够使用 `?` 给函数指定可选参数类型。

#### 内容

-   使用函数实现某个功能时，参数可以传也可以不传，这种情况下，在给函数参数指定类型时，就用到**可选参数**了。

-   比如，数组的 slice 方法，可以 `slice()` 也可以 `slice(1)` 还可以 `slice(1, 3)`。

    ```ts
    // start、end 可传可不传，传就传 number 类型
    function mySlice(start?: number, end?: number): void {
        console.log('起始索引：', start, '结束索引：', end)
    }
    ```

-   可选参数：在可传可不传的参数名称后面添加 `?`（问号）。

-   注意：<font color=e32d40>**可选参数只能出现在参数列表的最后**</font>，也就是说可选参数后面不能再出现必选参数。

### 函数类型-参数默认值

#### 目标

能够给函数指定默认值。

#### 内容

参数默认值和可选参数互斥的，只能指定其中一种。

```ts
// Error: Parameter cannot have question mark and initializer
function mySlice(start: number = 0, end?: number = 0) {}
```

```ts
// 可选参数
function mySlice(start: number = 0, end?: number) {}
```

```ts
// 默认值
function mySlice(start: number = 0, end: number = 0) {}
```

### 对象类型-基本使用

#### 目标

掌握对象类型的基本使用。

#### 内容

JS 中的对象是由属性和方法构成的，而 <font color=e32d40>**TS 对象的类型就是在描述对象的结构**</font>（有什么类型的属性和方法）。

-   基本使用。

```ts
const person: object = {}
```

-   另一种使用方式。

```ts
// 左边的 {} 表示类型，右边的 {} 表示值
let person: {} = {}
```

-   可以精确描述对象里面具体内容的类型。

```ts
// 要求必须指定 string 类型的 name 属性
const person: { name: string } = {
    name: '同学',
}
```

-   描述对象中方法的类型。

```ts
// 在一行代码中指定对象的多个属性类型时，使用 `;`（分号）来分隔
const person: { name: string; add(n1: number, n2: number): number } = {
    name: '同学',
    add(n1, n2) {
        return n1 + n2
    },
}
```

-   也可以通过换行来分隔多个属性类型，去掉 `;`。

```ts
const person: {
    name: string
    add(n1: number, n2: number): number
} = {
    name: '同学',
    add(n1, n2) {
        return n1 + n2
    },
}
```

-   定义对象类型时也可以结合**类型别名**来使用。

```ts
type Person = {
    name: string
    add(n1: number, n2: number): number
}
const person: Person = {
    name: '同学',
    add(n1, n2) {
        return n1 + n2
    },
}
```

#### 小结

-   使用 `{}` 来描述对象结构。

-   属性采用 `属性名: 类型` 的形式。

-   方法采用 `方法名(): 返回值类型` 的形式。

### 对象类型-箭头函数形式的方法类型

```ts
/* type Person = {
  name: string
  add(n1: number, n2: number): number
} */

type Person = {
    name: string
    // 也可以通过箭头函数的形式来描述对象中方法的类型
    add: (n1: number, n2: number) => number
}
const person: Person = {
    name: '同学',
    add(n1, n2) {
        return n1 + n2
    },
}
```

### 对象类型-对象可选属性

-   对象的属性或方法，也可以是可选的，此时就用到**可选属性**了。

-   比如，我们在使用 `axios({ ... })` 时，如果发送 GET 请求，method 属性就可以省略。

-   可选属性的语法与函数可选参数的语法一致，都使用 `?` 来表示。

```ts
type Config = {
    url: string
    method?: string
}

function myAxios(config: Config) {
    console.log(config)
}
```

### 对象类型-练习

创建两个学生对象：包含姓名、性别、成绩、身高、学习、打游戏。

```ts
type Student = {
    name: string
    gender: string
    score: number
    height: number
    study(): void
    play: (name: string) => void
}

const stu: Student = {
    name: 'xxx',
    gender: 'man',
    score: 88,
    height: 178,
    study() {
        console.log('学学学')
    },
    // play() 这里不写参数，也不会马上报错，但 stu.play() 调用的时候就知道了
    play(name) {},
}
```

### 对象类型-接口

当一个对象类型被多次使用时，一般会使用接口（`interface`）来描述对象的类型，达到复用的目的。

-   使用 `interface` 关键字来声明接口。

-   接口名称(比如，此处的 IPerson)，可以是任意合法的变量名称，推荐以 `I` 开头。

-   声明接口后，直接使用接口名称作为变量的类型。

-   因为每一行只有一个属性类型，因此，属性类型后没有 `;`（分号）。

```ts
interface IStudent {
    name: string
    gender: string
    study(): void
}

const stu: IStudent = {
    name: 'xxx',
    gender: 'man',
    study() {
        console.log('学学学')
    },
}
```

### 对象类型-interface vs type

interface（接口）和 type（类型别名）的对比。

-   相同点：都可以给对象指定类型。

-   不同点。

    a，接口，只能为对象指定类型。

    b，类型别名，不仅可以为对象指定类型，实际上可以为任意类型指定别名。

-   推荐：能使用 type 就使用 type。

interface

```ts
interface IPerson {
    name: string
    age: number
    sayHi(): void
}
```

type

```ts
type IPerson = {
    name: string
    age: number
    sayHi(): void
}

type NumStr = number | string
```

### 对象类型-接口继承

如果两个类型之间有相同的属性或方法，可以将<font color=e32d40>**公共的属性或方法抽离出来，通过继承来实现复用**</font>。

-   type 方式。

```ts
type Point2D = {
    x: number
    y: number
}
type Point3D = {
    x: number
    y: number
    z: number
}
```

-   interface 方式。

```ts
interface Point2D {
    x: number
    y: number
}
// 使用 `extends`（继承）关键字实现了接口 Point3D 继承 Point2D
// 继承后，Point3D 就有了 Point2D 的所有属性和方法（此时，Point3D 同时有 x、y、z 三个属性）
interface Point3D extends Point2D {
    z: number
}
```

### 元组类型

-   场景：在地图中，使用经纬度坐标来标记位置信息。

-   可以使用数组来记录坐标，那么，该数组中只有两个元素，并且这两个元素都是数值类型。

```ts
const position: number[] = [116.2317, 39.5427]
```

-   使用 `number[]` 的缺点：不严谨，因为该类型的数组中可以出现任意多个数字。

-   更好的方式：`元组 Tuple`。

-   元组类型是另一种类型的数组，<font color=e32d40>**它能确定元素的个数以及特定索引对应的类型**</font>。

```ts
const position: [number, number] = [39.5427, 116.2317]
```

-   解释说明。

    a，元组类型可以确切地标记出有多少个元素，以及每个元素的类型。

    b，该示例中，元素有两个元素，每个元素的类型都是 number。

```ts
// 其实 useState 的返回值就是一个元组
function useState(num: number): [number, (number) => void] {
    const setNum = (num: number) => {}
    return [num, setNum]
}
const [num, setNum] = useState(8)
```

### 类型推论

-   在 TS 中，某些没有明确指出类型的地方，<font color=e32d40>**TS 的类型推论机制会帮助提供类型**</font>。

-   换句话说：由于类型推论的存在，这些地方，类型注解可以省略不写。

-   常见的发生类型推论的 2 种场景。

    a，声明变量并初始化时。

    b，决定函数返回值时。

```ts
// 变量 age 的类型被自动推断为：number
let age = 18

const obj = {
    name: 'ifer',
    age: 18,
    show() {},
}

// 函数返回值的类型被自动推断为：number
function add(num1: number, num2: number) {
    return num1 + num2
}
```

-   推荐：代码写熟了之后，有类型推论的情况下可以省略类型注解，充分利用 TS 类型推论的能力，提升开发效率。

-   技巧：如果不知道类型，可以通过鼠标放在变量名称上，利用 VSCode 的提示来查看类型。

-   建议：在 VSCode 中写代码的时候，多看方法、属性的类型，养成写代码看类型的习惯，例如 `const oDiv = document.createElement('div')`。

### 字面量类型-基本使用

思考以下代码，两个变量的类型分别是什么?

```ts
let str1 = 'Hello TS'
const str2 = 'Hello TS'
```

通过 TS 类型推论机制，可以得到答案：变量 str1 的类型为：string，变量 str2 的类型为：'Hello TS'。

-   str1 是一个变量（let），它的值可以是任意字符串，所以类型为：string。

-   str2 是一个常量（const），它的值不能变化只能是 'Hello TS'，所以，它的类型为：'Hello TS'。

-   注意：此处的 'Hello TS'，就是一个<font color=e32d40>**字面量类型**</font>，也就是说某个特定的字符串也可以作为 TS 中的类型。

-   任意的 JS 字面量都可以作为类型使用，例如 `{ name: 'jack' }`、`[]`、`18`、`'abc'`、`false`、`function() {}` 等。

### 字面量类型-使用方式和场景

-   使用方式：<font color=e32d40>**字面量类型常配合联合类型一起使用**</font>。

-   使用场景：用来表示一组明确的可选值列表，比如在贪吃蛇游戏中，游戏方向的值只能是上、下、左、右中的一个。

```ts
type Direction = 'up' | 'down' | 'left' | 'right'
function changeDirection(direction: Direction) {
    console.log(direction)
}
changeDirection('up') // 调用函数时，会有类型提示
```

-   解释：参数 direction 的值只能是 up/down/left/right 中的任意一个。

-   优势：<font color=e32d40>**相比于 string 类型，使用字面量类型更加精确、严谨**</font>。

-   其他应用场景，性别和 Redux 中的 Action 等等。

```ts
type Gender = '男' | '女'
const zs: Gender = '男'
```

```ts
type Action = {
    type: 'TODO_ADD' | 'TODO_DEL' | 'TODO_CHANGE' | 'TODO_FIND'
}

function reducer(state, action: Action) {
    switch (action.type) {
        case 'TODO_ADD': // 这里会自动具有提示
    }
}
```

### 枚举类型-基本使用（了解）

-   枚举的功能类似于**字面量类型+联合类型组合**的功能，也可以表示一组明确的可选值。

-   枚举：定义一组命名常量，它描述一个值，该值可以是这些命名常量中的一个。

-   使用 `enum` 关键字定义枚举，约定枚举名称以大写字母开头。

-   枚举中的多个值之间通过 `,`（逗号）分隔，定义好枚举后，直接使用枚举名称作为类型注解。

```ts
// 创建枚举
enum Direction {
    Up,
    Down,
    Left,
    Right,
}

// 可以当做类型使用枚举
function changeDirection(direction: Direction) {
    console.log(direction)
}

// 也可以当做值使用枚举
// 调用函数时，需要传入：枚举 Direction 成员的任意一个，类似于 JS 中的对象，直接通过点（.）语法 访问枚举的成员
changeDirection(Direction.Up)
```

### 枚举类型-数字枚举

-   问题：我们把枚举成员作为了函数的实参，它的值是什么呢?

-   解释：通过将鼠标移入 Direction.Up，可以看到枚举成员 Up 的值为 0。

-   注意：枚举成员是有值的，默认为：从 0 开始自增的数值。

-   我们把枚举成员的值为数字的枚举称为：`数字枚举`。

-   当然，也可以通过“等号”给枚举中的成员指定初始值，如下所示。

```ts
// Down -> 11、Left -> 12、Right -> 13
enum Direction {
    Up = 10,
    Down,
    Left,
    Right,
}

enum Direction {
    Up = 2,
    Down = 4,
    Left = 8,
    Right = 16,
}
console.log(Direction['Up']) // 2
// 也可以反向操作
console.log(Direction[2]) // Up
```

### 枚举类型-枚举实现原理

-   枚举类型比较特殊，不仅仅用作类型，还可以当做值使用，因为枚举成员都是有值的。

-   也就是说，其他的类型会在编译为 JS 代码时自动移除，但是，**枚举类型会被编译为 JS 代码**。

-   说明：枚举与前面讲到的字面量类型 + 联合类型组合的功能类似，都用来表示一组明确的可选值列表。

-   推荐：<font color=e32d40>**字面量类型 + 联合类型**</font>组合的方式，因为相比枚举，这种方式更加直观、简洁、高效。

```ts
enum Direction {
    Up = 2,
    Down = 4,
    Left = 8,
    Right = 16,
}

// 会被编译为以下 JS 代码：
var Direction
;(function (Direction) {
    Direction[(Direction['Up'] = 2)] = 'Up'
    Direction[(Direction['Down'] = 4)] = 'Down'
    Direction[(Direction['Left'] = 8)] = 'Left'
    Direction[(Direction['Right'] = 16)] = 'Right'
    console.log(Direction)
})(Direction || (Direction = {}))
```

### 枚举类型-字符串枚举

-   定义：枚举成员的值是字符串称为字符串枚举。

-   注意：字符串枚举没有自增长行为，因此，<font color=e32d40>**字符串枚举的每个成员必须有初始值**</font>。

```ts
enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT',
}
```

具体的使用案例。

```ts
enum Gender {
    女,
    男,
}
type User = {
    name: string
    age: number
    // gender: '男' | '女' // 但后台需要 0 和 1
    gender: Gender
}

const user: User = {
    name: 'ifer',
    age: 18,
    gender: Gender.男,
}
```

### any 类型

-   原则：不推荐使用 any！这会让 TypeScript 变为 “AnyScript”（失去 TS 类型保护的优势）。

-   因为当值的类型为 any 时，可以对该值进行任意操作，即使可能存在错误，并且不会有代码提示。

    ```ts
    let num: any = 8
    num.toFixed() // 没有提示
    num = 'xxx' // 可以赋任意值（即可以把任意值给 any 类型）
    ```

-   尽可能的避免使用 any 类型，除非临时使用 any 来“避免”书写很长、很复杂的类型，或者有些参数就是可以使用任何类型，例如 `console.log()`。

-   其他隐式具有 any 类型的情况（因为不推荐使用 any，所以下面两种情况下都应该提供类型）。

    a，声明变量不提供类型也不提供默认值。

    b，函数参数不加类型。

### 类型断言

有时候你会比 TS 更加明确一个值的类型，此时可以使用类型断言来指定更具体的类型，比如根据 ID 选择 a 标签。

```ts
// 注意 document.querySelector('a') 这种写法会自动推断出是 HTMLLinkElement 类型
const oLink = document.getElementById('link')
```

-   注意：该方法返回值的类型是 HTMLElement，该类型只包含所有标签公共的属性或方法，不包含 a 标签特有的 href 等属性。

-   因此，这个类型太宽泛（不具体），无法操作 href 等 a 标签特有的属性或方法。

-   解决方式：这种情况下就需要使用类型断言指定更加具体的类型。

```ts
const oLink = document.getElementById('link') as HTMLAnchorElement
```

-   解释说明。

    a，使用 `as` 关键字实现类型断言。

    b，关键字 as 后面的类型是一个更加具体的类型（HTMLAnchorElement 是 HTMLElement 的子类型）。

    c，通过类型断言，oLink 的类型变得更加具体，这样就可以访问 a 标签特有的属性或方法了。

-   另一种语法，使用 `<>` 语法，这种语法形式不常用知道即可。

    ```ts
    const oLink = <HTMLAnchorElement>document.getElementById('link')
    ```

-   技巧：打开浏览器控制台，选中标签，通过 `$0.__proto__` 可以获取 DOM 元素的类型。

### typeof

-   众所周知，JS 中提供了 typeof 操作符，用来在 JS 中获取数据的类型。

    ```js
    // 使用的是 JS 自身的 typeof
    console.log(typeof 'Hello world') // 'string'
    ```

-   TS 中 typeof 的使用场景：根据已有变量的值，获取该值的类型，来简化类型书写。

    ```ts
    const p = { x: 1, y: 2 }
    function formatPoint(point) {} // 没有提示
    function formatPoint(point: { x: number; y: number }) {} // 有提示，写法麻烦
    // 使用 `typeof` 操作符来获取变量 p 的类型，结果与上面对象字面量的形式相同
    function formatPoint(point: typeof p) {} // 推荐
    ```

-   注意 typeof <font color=e32d40>**出现在类型注解的位置**</font>（参数名称的冒号后面，区别于 JS 代码）。

## 泛型-基本介绍

-   泛型：定义时不确定，使用时通过传递类型变量才能确定的类型，常用于：函数、接口、class 中。

-   需求：创建一个 id 函数，传入什么数据类型就返回该数据类型本身（也就是说，参数和返回值类型相同）。

    ```ts
    function id(value: number): number {
        return value
    }
    ```

-   比如，`id(10)` 调用以上函数就会直接返回 10 本身，但是，该函数只接收数值类型，无法用于其他类型。

-   为了让函数能够接受任意类型，可以将参数类型修改为 any，但是，这样就失去了 TS 的类型保护，类型不安全。

    ```ts
    function id(value: any): any {
        return value
    }
    ```

## 泛型-泛型函数

-   定义。

    a，语法：在函数名称的后面添加 `<>`（尖括号），<font color=e32d40>**尖括号中添加类型变量**</font>。

    b，类型变量：一种特殊类型的变量，它处理类型而不是值，比如下面案例中的 Type。

    c，该类型变量相当于一个类型容器，能够捕获用户提供的类型（具体是什么类型由用户调用该函数时指定）。

    d，因为 Type 是类型，因此可以将其作为函数参数和返回值的类型，表示参数和返回值具有相同的类型。

    e，类型变量 Type，可以是任意合法的变量名称，一般简写为 T。

    ```ts
    function id<Type>(value: Type): Type {
        return value
    }

    function id<T>(value: T): T {
        return value
    }
    ```

-   调用。

    a，语法：在函数名称的后面添加 `<>`（尖括号），<font color=e32d40>**尖括号中指定具体的类型**</font>，比如 number 或 string 等。

    b，当传入类型 number 后，这个类型就会被函数声明时指定的类型变量 Type 捕获到。

    c，此时，Type 的类型就是 number，所以，函数 id 参数和返回值的类型也都是 number。

    d，同样，如果传入类型 string，函数 id 参数和返回值的类型就都是 string。

    e，这样，通过泛型就做到了让 id 函数与多种不同的类型一起工作，**实现了复用的同时保证了类型安全**。

    ```ts
    const num = id<number>(10)
    const str = id<string>('a')
    ```

## 简化泛型函数调用

```ts
let num = id(10) // 省略 <number> 调用函数
let str = id('a') // 省略 <string> 调用函数
```

-   在调用泛型函数时，**可以省略 `<类型>` 来简化泛型函数的调用**。

-   此时，TS 内部会采用一种叫做**类型参数推断**的机制，来根据传入的实参自动推断出类型变量 Type 的类型。

-   比如，传入实参 10，TS 会自动推断出变量 num 的类型 number，并作为 Type 的类型。

-   推荐：使用这种简化的方式调用泛型函数，使代码更简短，更易于阅读。

-   说明：**当编译器无法推断类型或者推断的类型不准确时，就需要显式地传入类型参数**。

## 泛型约束

-   泛型函数的类型变量 Type 可以代表任意类型，这导致访问泛型类型定义的数据属性时会没有提示，或者报错。

-   比如，id('a') 调用函数时获取参数的长度。

    ```ts
    function id<Type>(value: Type): Type {
        console.log(value.length) // Property 'length' does not exist on type 'Type'
        return value
    }

    id(['a', 'b'])
    ```

-   解释：Type 可以代表任意类型，无法保证一定存在 length 属性，比如 number 类型就没有 length。

-   解决：需要为泛型添加约束来收缩类型（缩窄类型取值范围）。

-   主要有两种方式：1. 指定更加具体的类型，2. 通过 extends 关键字配合 interface 来添加约束。

### 指定更加具体的类型

比如，将类型修改为 `Type[]`（Type 类型的数组），因为只要是数组就一定存在 length 属性，因此就可以访问了。

```ts
// 参数是由泛型组成的数组
function id<Type>(value: Type[]): Type[] {
    console.log(value.length)
    return value
}

id<string>(['a', 'b'])
```

### 添加泛型约束

-   创建描述约束的接口 ILength，该接口要求提供 length 属性。

-   通过 `extends` 关键字使用该接口，为泛型（类型变量）添加约束。

-   该约束表示：**传入的类型必须具有 length 属性**。

```ts
interface ILength {
    length: number
}

// Type extends ILength 添加泛型约束
// 表示传入的类型必须满足 ILength 接口的要求才行，也就是得有一个 number 类型的 length 属性
function id<Type extends ILength>(value: Type): Type {
    console.log(value.length)
    return value
}

id('abc')
id(['a', 'b', 'c'])
id({ length: 8 })
```

## 泛型-多个类型变量

泛型的类型变量可以有多个，并且<font color=e32d40>**类型变量之间还可以约束**</font>（比如第二个类型变量受第一个类型变量约束）。

📝 需求：创建一个函数来获取对象中属性的值。

```ts
function getProp<Type, Key extends keyof Type>(obj: Type, key: Key) {
    return obj[key]
}
let person = { name: 'jack', age: 18 }
getProp(person, 'name')
```

1. 添加了第二个类型变量 Key，两个类型变量之间使用 `,` 逗号分隔。

2. **keyof 关键字接收一个对象类型，生成其键名称（可能是字符串或数字）的联合类型**。

3. 本示例中 `keyof Type` 实际上获取的是 person 对象所有键的联合类型，也就是：`'name' | 'age'`。

4. 类型变量 Key 受 Type 约束，即 Key 只能是 Type 所有键中的任意一个，或者说只能访问对象中存在的属性。

```ts
// Type extends object 表示：Type 应该是一个对象类型，如果不是对象类型，就会报错
// 注意：如果要用到对象类型，应该用 object ，而不是 Object
function getProperty<Type extends object, Key extends keyof Type>(obj: Type, key: Key) {
    return obj[key]
}
```

## 泛型接口

接口也可以配合泛型来使用，以增加其灵活性，增强其复用性。

```ts
interface User<T> {
    name: T
    age: number
}
const user: User<string> = {
    name: 'ifer',
    age: 18,
}
```

思考下面代码的意思，并写出对应的实现。

```ts
interface IdFunc<Type> {
    id: (value: Type) => Type // 接收什么类型，返回什么类型
    ids: () => Type[] // 返回值是，根据接收到的类型组成的数组
}
```

```ts
let obj: IdFunc<number> = {
    id(value) {
        return value
    },
    ids() {
        return [1, 3, 5]
    },
}
```

1. 在接口名称的后面添加 `<类型变量>`，那么，这个接口就变成了泛型接口。

2. 接口的类型变量，对接口中所有其他成员可见，也就是**接口中所有成员都可以使用类型变量**。

3. 使用泛型接口时，<font color=e32d40>**需要显式指定具体的类型**</font>（比如，此处的 `IdFunc<number>`）。

4. 此时，id 方法的参数和返回值类型都是 number，ids 方法的返回值类型是 `number[]`。

```ts
// 这其实也是通过泛型接口的形式来定义的数组类型
const arr: Array<number> = [1, 2, 3]
```

## 泛型工具类型

-   泛型工具类型：TS 内置了一些常用的工具类型，来简化 TS 中的一些常见操作。

-   说明：它们都是基于泛型实现并且是内置的，可以直接在代码中使用，这些工具类型有很多，主要学习以下几个。

    a，`Partial<Type>`

    b，`Readonly<Type>`

    c，`Pick<Type, Keys>`

### Partial

-   Partial<Type> 用来构造（创建）一个类型，将 Type 的所有属性设置为可选。

```ts
type Props = {
    id: string
    children: number[]
}

// 构造出来的新类型 PartialProps 结构和 Props 相同，但所有属性都变为可选的啦
type PartialProps = Partial<Props>
```

### Readonly

-   Readonly<Type> 用来构造一个类型，将 Type 的所有属性都设置为 readonly（只读）。

-   当我们想给 id 属性重新赋值时，就会报错：无法分配到 "id"，因为它是只读属性。

```ts
type Props = {
    id: string
    children: number[]
}
// 构造出来的新类型 ReadonlyProps 结构和 Props 相同，但所有属性都变为只读的啦
type ReadonlyProps = Readonly<Props>

let props: ReadonlyProps = { id: '1', children: [] }
props.id = '2' // Cannot assign to 'id' because it is a read-only property
```

### Pick

-   Pick<Type, Keys> 从 Type 中选择一组属性来构造新类型。

-   Pick 工具类型有两个类型变量，1. 表示选择谁的属性，2. 表示选择哪几个属性。

-   第二个类型变量传入的属性只能是第一个类型变量中存在的属性。

-   构造出来的新类型 PickProps，只有 id 和 title 两个属性类型。

```ts
interface Props {
    id: string
    title: string
    children: number[]
}
// 摘出 id 和 title
type PickProps = Pick<Props, 'id' | 'title'>
```

Omit，和 Pick 相反，表示排除的意思。

```ts
// 排除 id 和 title
type OmitProps = Omit<Props, 'id' | 'title'>
```

## 创建新项目

-   命令：`npx create-react-app my-app --template typescript`。

-   说明：在命令行中，添加 `--template typescript` 表示创建支持 TS 的项目。

-   项目目录的变化。

    a，在项目根目录中多了一个 TS 配置文件：`tsconfig.json`。

    b，在 src 目录中，文件的后缀有变化，由原来的 .js 变为 `.ts` 或 `.tsx`（使用了 jsx 模板，后缀名必须叫 tsx）。

    c，在 src 目录中，多了 `react-app-env.d.ts` 类型声明文件，用来指定 node、react、react-dom 库的类型。

## tsconfig 的介绍

-   tsconfig.json 是 TypeScript 项目的配置文件，用于配置 TypeScript，也可以通过 `tsc --init` 生成。

-   说明：所有的配置项都可以通过鼠标移入的方式，来查看配置项的解释说明，[配置说明](https://www.typescriptlang.org/tsconfig)。

```json
{
    // 编译选项
    "compilerOptions": {
        // 生成代码的语言版本：即将我们写的 TS 代码编译成哪个版本的 JS 代码
        "target": "es5",
        // 内置库
        // 例如如果没有 dom，项目中使用 document.querySelector('div') 将会报错
        "lib": ["dom", "dom.iterable", "esnext"],
        // 允许 TS 编译器编译 JS 文件
        "allowJs": true,
        // 跳过类型声明文件的类型检查
        "skipLibCheck": true,
        // 是否屏蔽 ESModule 和 CommonJS 之间的差异，可以使用 import 或 require
        "esModuleInterop": true,
        // 导入导出规则的控制
        "allowSyntheticDefaultImports": true,
        // 开启严格模式
        "strict": true,
        // 对文件名称强制区分大小写，例如 Demo.ts 和 demo.ts 是不同的文件
        "forceConsistentCasingInFileNames": true,
        // 为 switch 语句启用错误报告
        "noFallthroughCasesInSwitch": true,
        // 生成代码的模块化标准
        "module": "esnext",
        // 模块解析（查找）策略
        "moduleResolution": "node",
        // 允许导入扩展名为 .json 的模块
        "resolveJsonModule": true,
        // 是否将没有 import/export 的文件视为旧（全局而非模块化）脚本文件
        "isolatedModules": true,
        // 编译时不生成任何文件（只进行类型检查）
        "noEmit": true,
        // 指定将 JSX 编译成什么形式
        "jsx": "react-jsx"
    },
    // 指定允许 ts 处理的目录
    "include": ["src"]
}
```

## 类型声明文件

### 基本介绍

-   几乎所有的 JavaScript 应用都会引入许多第三方库来完成任务需求，这些第三方库不管是否是用 TS 编写的，最终都要编译成 JS 代码，才能发布给开发者使用。

-   我们知道是 TS 提供了类型，才有了代码提示和类型保护等机制，但在项目开发中使用第三方库时，你会发现它们几乎都有相应的 TS 类型，这些类型是怎么来的呢？

-   答案：<font color=e32d40>**类型声明文件**</font>，用来为已存在的 JS 库提供类型信息。

-   TS 中有两种文件类型，分别是 `.ts` 和 `.d.ts` 文件。

-   `.ts` 文件：<font color=e32d40>**既可以包含类型信息又可以包含可执行代码**</font>。

    a，可以被编译为 .js 文件，然后执行代码。

    b，用途：编写程序代码的地方。

-   `.d.ts` 文件：<font color=e32d40>**只包含类型信息的类型声明文件**</font>。

    a，不会生成 `.js` 文件，仅用于提供类型信息，在 `.d.ts` 文件中不允许出现可执行的代码，只用于提供类型。

    b，用途：为 JS 提供类型信息。

-   总结：`.ts` 是 `implementation`（代码实现文件），`.d.ts` 是 declaration（类型声明文件），如果要为 JS 库提供类型信息，需要使用 `.d.ts` 文件。

### 内置文件

-   TS 为 JS 所有标准化内置 API 都提供了类型声明文件，比如在使用数组时，数组所有方法都会有相应的代码提示以及类型信息。

-   比如，查看 forEach 方法的类型声明，通过 Ctrl + 鼠标左键，在 VSCode 中会自动跳转到 `lib.es5.d.ts` 类型声明文件中。

-   当然，像 window、document 等 BOM、DOM API 也都有相应的类型声明（`lib.dom.d.ts`）。

`src/index.tsx`

```ts
const strs = ['a', 'b', 'c']
// 鼠标放在 forEach 上查看类型
strs.forEach
```

### 第三方库

-   目前，几乎所有常用的第三方库都有相应的类型声明文件。

-   第三方库的类型声明文件有两种存在形式，分别是库自带类型声明文件或者由 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/) 提供。

-   库自带类型声明文件：比如，axios，通过查看 `node_modules/axios` 目录可以发现 `index.d.ts` 类型定义文件，这种情况下只需要正常导入该库，**TS 就会自动加载库自己的类型声明文件**，提供该库的类型声明。

-   由 DefinitelyTyped 提供。

    a，DefinitelyTyped 是一个 Github 仓库，用来提供高质量 TypeScript 类型声明。

    b，可以通过 npm/yarn 来下载该仓库提供的 TS 类型声明包，这些包的名称格式为:`@types/*`。

    c，比如，@types/react、@types/lodash 等。

    d，说明：在实际项目开发时，如果你使用的第三方库没有自带的声明文件，VSCode 会给出明确的提示。

-   当安装 `@types/*` 类型声明库后，**TS 也会自动加载该类声明库**，可以通过此[链接](https://www.typescriptlang.org/dt)来查询 @types/\* 库。

### 自己定义

`utils/index.js`

```js
let count = 10
let songName = '痴心绝对'
let position = {
    x: 0,
    y: 0,
}

function add(x, y) {
    return x + y
}

function changeDirection(direction) {
    console.log(direction)
}

const fomartPoint = (point) => {
    console.log('当前坐标：', point)
}

export { count, songName, position, add, changeDirection, fomartPoint }
```

方法 1：将 js 文件改成 ts 文件，并修改成 ts 代码。

```ts
// #1
type Position = {
    x: number
    y: number
}
// #2
type Direction = 'top' | 'down' | 'left' | 'right'

let count = 10
let songName = '痴心绝对'
// #3
let position: Position = {
    x: 0,
    y: 0,
}

// #4
function add(x: number, y: number): number {
    return x + y
}

// #5
function changeDirection(direction: Direction) {
    console.log(direction)
}

// #6
const fomartPoint = (point: Position) => {
    console.log('当前坐标：', point)
}

export { count, songName, position, add, changeDirection, fomartPoint }
```

方法 2：定义类型声明文件 `index.d.ts`，无需改动原来的代码。

```ts
declare let count: number

declare let songName: string

interface Position {
    x: number
    y: number
}

declare let position: Position

declare function add(x: number, y: number): number

type Direction = 'left' | 'right' | 'top' | 'down'

declare function changeDirection(direction: Direction): void

type FomartPoint = (point: Position) => void

declare const fomartPoint: FomartPoint

export { count, songName, position, add, changeDirection, FomartPoint, fomartPoint }
```

-   如果多个 .ts 文件中都用到同一个类型，此时可以创建 .d.ts 文件提供该类型，实现类型共享。

-   declare 关键字，用于类型声明，为其他地方（比如 .js 文件）已存在的变量声明类型，而不是创建一个新的变量。

    a，对于 type、interface 等这些明确就是 TS 类型的（只能在 TS 中使用的），可以省略 declare 关键字。

    b，对于 let、function 等具有双重含义（在 JS、TS 中都能用），应该使用 declare 关键字，明确指定此处用于类型声明。

-   操作步骤。

    1. 创建 `index.d.ts` 类型声明文件。

    2. 通过 declare 关键字创建需要共享的类型，最后使用 export 统一导出。

    3. 通过 import 正常导入 JS/TS 文件即可，TS 会自动加载与之同名的 .d.ts 文件。
