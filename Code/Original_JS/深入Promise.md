# 深入Promise

# Promise基本概念

关于Promise的基础知识，可以在下面的资料中详细了解

[ES6 入门教程](https://es6.ruanyifeng.com/#docs/promise)

[Promise - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

![](https://kerwin-1311807449.cos.ap-nanjing.myqcloud.com/promise.png)

> 通常来说，Promise对象以此有以下三个注意点：
> 
> 1. 一个回调，指Promise对象唯一的参数，是一个回调函数
> 2. 两个参数，指回调函数中的两个参数 reslove, reject，它们都是函数，分别代表成功与失败两种情况
> 3. 三个状态，指Promise对象执行的状态，pending resloved rejected ，分别为正在执行、成功、失败

## 重要的方法

### promise.then和promise.catch

完整的写法是 **Promise.prototype.then()** 和 **Promise.prototype.catch()。**

两者都会返回一个Promise对象，所以，可以使用链式调用（链式接收）。

.then方法有两个参数，**onFulfilled** 和 **onRejected，**前者解决成功的回调，后者则接收失败的回调，根据状态的不同，返回值也不相同，如果成功回调，且接收值是undefined，则返回值也为undefined。 .catch方法有一个参数 onRejected，值得注意：其实.catch方法等同于 **Promise.prototype.then(undefined, onRejected)。**

**错误穿透：** 指当链式调用时其中出现了失败，该错误能够穿透整个链式，在链子的最后面使用catch来处理。当然在链式调用的中间环节来处理这个错误也可以。 .then方法中发生错误后，后面的代码将不会执行下去，而是直接寻找下面最近的catch方法来处理这个错误，当错误处理完成后，.catch方法后面的.then方法依旧能执行。

### 语法糖:Promise.reslove() 和Promise.reject()

其实每个.then和.catch方法都会默认返回一个 完整的 Promise 对象。 静态方法 Promise.reslove() 和 Promise.reject() 分别能返回一个 Promise对象和 一个状态为拒绝的 Promise对象。 能免去每次都写完整的 Promise 对象 （当然你可以不写，会自动帮你返回数据的）。

### Promise.all

静态方法，能同时并发处理多个异步方法。 返回一个新的 promise 对象，等到所有的 promise 对象都成功或有任意一个 promise 失败。

**注意事项：** > 1. 接收一个iterator类型对象 > 1. 如果是promise对象，一次返回结果,且排列顺序同你传入方法的顺序 > 1. 如果不是promise对象，将数据直接用reslove()包裹返回 > 1. 传入为空，返回为空 > 1. 捕获第一个错误的错误对象

### Promise.race

静态方法，接收一个 promise 对象的集合。 当其中的任意一个 promise 成功，就返回那个成功的 promise 的值。

**注意事项：** > 1. 接收一个iterator类型对象 > 1. 接收的如果是promise对象，谁先完成，就返回谁（无论成功还是失败） > 1. 只要有一个返回了，剩下的就不会再管，无论成功与否 > 1. 适用场景：设置请求超时 测试接口速度