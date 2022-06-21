# vue3手写模态框组件

# 关于模态框

模态框，作为UI组件库基础模块之一，主要是对于浏览器自带模态组件（alert、confirm、prompt）的二次美化及功能增强。使用场景十分丰富

在执行敏感操作，提交信息时，都需要使用到模态框组件。

按功能来分，模态框大致分为两种： 只需要呈现提示消息，或额外需要确认用户操作的模态框（alert和confirm） 需要收集用户输入信息而进行接下来操作的模态框（prompt）

# 组件模态框要实现什么

这里拿element-ui plus的模态框组件来举例。 element-ui的模态框在使用时首先要从目标组件包中按需导入 使用时可以直接调用导入的组件函数，接收一个对象作为options配置 也可以调用组件函数上的confirm等方法达到使用不同形式的模态框 options函数中根据定义的属性可以定制标题，按钮文字，主体内容、按钮的显示隐藏等 通常会有半透明蒙版，点击蒙版会关闭当前模态框

alert和confirm有些业务有重叠，这里的例子就不实现alert的模态框了

![../../assets/Code/Vue/%E6%A8%A1%E6%80%81%E6%A1%86.png](../../assets/Code/Vue/%E6%A8%A1%E6%80%81%E6%A1%86.png)

image.png

# 模态框组件的主体是一个函数

由于在页面中使用模态框时，我们是调用的一个可以传递options配置的函数，所以我们可以得知模态框的主体部分就是导出了一个函数

```jsx
// 这个函数可以接收一个options参数来自定义配置
const MessageBox = (options) => {
// ......
}

// 按需导出
export {
 MessageBox
}
```

我们来看一下element-ui plus 中confirm模态框的代码

```jsx
const open = () => {
  ElMessageBox.confirm(
    'proxy will permanently delete the file. Continue?',
    'Warning',
    {
      confirmButtonText: 'OK',
      cancelButtonText: 'Cancel',
      type: 'warning',
    }
  )
    .then(() => {
      ElMessage({
        type: 'success',
        message: 'Delete completed',
      })
    })
    .catch(() => {
      ElMessage({
        type: 'info',
        message: 'Delete canceled',
      })
    })
}
```

这个模态框的函数后面可以调用then、catch方法来进行接下来的操作 **所以说明这个ElMessageBox函数，是一个promise函数，至少会返回一个Promise函数**

我们将自己的messageBox函数加工一下，让它满足Promise函数的要求

```jsx
const MessageBox = (options) => {

  // 返回一个promise
  return new Promise((resolve, reject) => {
    //......
  })
}
```

# 调用模态框函数显示弹出框

messageBox函数已经是一个Promise类型了，但这并不是最重要的。 模态框最重要的功能，是在我们点击指定的按钮（也就是触发了messageBox函数后），会在该页面弹出一个居中带有模板的卡片框出来。

很显然，这个弹出框并不是我们一开始在该页面就创建好的（如dialog弹出框） 那么这个弹出框则应该是在我们调用messageBox函数时动态生成的。

## vue3动态生成组件实例

vue3中，main主文件中生成vue根实例的方法已和vue2不同。

首先从vue包中解构导入一个**createApp**方法 这个方法接收两个参数，第一个参数时生成实例所根据的根组件，第二个参数是要传递给根组件的prop（在组件中使用props接收）

所以，使用craeteApp这个方法我们也可以动态的生成需要显示的弹出框来。

问题是： 根组件实例通过实例的mount方法挂载到根节点上，我们动态生成的实例应该挂载到哪上面呢？

## createDocumentFragment

我们可以创建一个空白的文档片段，将实例挂载到它上面，然后动态添加到目标页面的body上去

为什么要使用这个方法呢？ 参考MDN中对**DocumentFragment**的描述： > 因为文档片段存在于**内存中**，并不在DOM树中，所以将子元素插入到文档片段时不会引起页面回流（对元素位置和几何上的计算）。因此，使用文档片段通常会带来更好的性能。

```jsx
// 这是已经写好的弹出框组件
import MessageBoxComponent from './MessageBox';

const MessageBox = (options) => {
  // 使用createApp方法创建弹出框组件的实例，传入options
  const messageBoxApp = createApp(MessageBoxComponent, options);
  // 我们在返回的promise中再次调用一个方法来处理不同情况，将promise的两个参数传进去
  return new Promise((resolve, reject) => {
    showMessageBox(messageBoxApp, { resolve, reject });
  })
}
```

```jsx
const showMessageBox = (app, { resolve, reject }) => {
// 创建文档碎片
const oFragment = document.createDocumentFragment();
// 将传入的弹出框实例挂载到创建的文档碎片上
const vm = app.mount(oFragment);
// 在当前页面的body中添加该文档碎片
document.body.appendChild(oFragment);
}
```

**这时，如果页面有个绑定messageBox函数的按钮被点击，就是在该页面上显示写好的弹出框了**

## 我将如何控制弹出框的消失

我们的弹出框显示了，这里出现了另外一个问题： 假设我们已经完成了弹出框取消按钮，按下这个按钮，就会让当前的弹出框隐藏，我们该如何做？

很明显，我们现在只是一味的将弹出框摆在了当前页面上，并没有关心它该如何消失。 按照使用dialog弹出框的经验，我们可以设置一个显示弹出框的开关属性，当其为true时，弹出框显示，当其为false时，弹出框消失。

**这个开关属性设置在哪里呢？**

常理是：控制谁的开关，就应该存放在谁那里。这个开关属性应该存放在我们的弹出框组件中

我们在弹出框组件中定义该属性，并同时编写一个改变它值的开关函数

```jsx
const state = reactive({
    visible: false
  });

const setVisible = (isVisible) => {
    state.visible = isVisible;
  }
```

**注意：这些数据和方法我们都是在vue组件中定义的，而messageBox函数写在一个js文件中，我们如何能在messageBox函数中拿到它们并改变呢？**

## 使用expose或defineExpose

其实很简单，注意看下面的代码

```jsx
const showMessageBox = (app, { resolve, reject }) => {

const oFragment = document.createDocumentFragment();
  //vm是创建的弹出框组件实例，组件的所有数据方法都会挂载到实例上
const vm = app.mount(oFragment);
document.body.appendChild(oFragment);
}
```

在挂载弹出框组件时，我们已经拿到了这个组件的实例，所以我们就可以访问该组件上所有的数据和方法

**但是我们还是建议使用expose将需要用到的数据及方法返回出来**

 [Vue3 expose文档](https://staging-cn.vuejs.org/api/options-state.html#expose)

因为vue3中除去setup()写法，还有一种使用<script setup>的语法糖，该标签时默认关闭的，即通过模板 ref 或者 $parent 链获取到的组件的公开实例，不会暴露任何在 <script setup> 中声明的绑定。
为了在 <script setup> 组件中明确要暴露出去的属性，使用 **defineExpose** 编译器宏

假如我们的组件是使用的<script setup>语法糖（vue3更推荐使用它）
使用defineExpose暴露出去我们需要使用的方法与数据

```jsx
const state = reactive({
    visible: false
  });

const setVisible = (isVisible) => {
    state.visible = isVisible;
  }

//====================================================

const { visible } = toRefs(state);
defineExpose({
    setVisible,
    state
  });
```

那么我们在向body中添加了该组件之后，调用暴露出来的的setVisible函数改变visible的数值为true

```jsx
//......
const vm = app.mount(oFragment);
document.body.appendChild(oFragment);

vm.setVisible(true);
```

在业务操作完成之后，我们点击取消或者确定（判定最后需要关闭弹出框时）
我们再次调用该方法将visible变为false

```jsx
//..........

vm.setVisible(false);
```

这是弹出框组件中弹出框盒子，他的显隐由该bisible控制

```jsx
 <div
      class="ui-message-box"
      v-show="visible"
    >

   ........

</div>
```

# **给函数挂载属性和方法**

在element-ui plus中，模态框有这样几种用法：
直接调用ElMessageBox
调用ElMessageBox.confirm()

从前面我们得知，ElMessageBox与我们自己的messageBox一样，是一个函数，怎么做到给函数挂载属性方法呢？

很简单，就这样挂

因为JS中的函数本质上也是对象，属于使用Function构造函数创建出来的实例，那么它也是可以添加属性的。

在这里我们定义一个数组，里面放入messageBox可能用到的方法名（这里只实现confirm和prompt）

```jsx
// 导出，因为在弹出框组件中需要用到它做字段验证
export const fields = ['confirm', 'prompt'];
```

通过以下方法给messageBox函数添加新的方法，对相应的options参数做改动

```jsx
fields.forEach(field => {
  // 给messageBox挂载方法，该方法也接收options
  MessageBox[field] = options => {
    console.log(field)
    // 将方法名也存入options参数中保存
    options.field = field;
    // 这个方法返回原本的messageBox
    return MessageBox(options);
  }
})
```

为什么返回原本的MessageBox函数呢？因为我们只需要根据不同messageBox方法中是否有动态添加的field属性就能判断当前属于调用的哪个方法了，这样集中编写代码会更加容易。

我们来验证一下
在messageBox函数中我们打印options中的field属性

```jsx
const MessageBox = (options) => {
  ++ console.log(options.field)

  const messageBoxApp = createApp(MessageBoxComponent, options);
  return new Promise((resolve, reject) => {
    showMessageBox(messageBoxApp, { resolve, reject });
  })
}
```

我们给三个按钮绑定messageBox方法，第二个按钮为messageBox.confirm，第三个为messageBox.prompt

打印结果如下： 和预想一样，没有问题

# **弹出框的确认和取消**

上面我们已经对不同方法创建的弹出框通过往options中添加属性，能够区分当前弹出的是哪种类型的模态框了（是confirm还是prompt）。

现在把目光聚焦到更核心的业务上来：不管哪种弹出框，底部都会有操作按钮。取消和确定，你可以不用，当它必须要有。

[弹出框主体是一个函数](notion://www.notion.so/vue3-eeaed762c4ba4d48b3d6fb2f4bce37ff#iEDRm)[生成文档碎片](notion://www.notion.so/vue3-eeaed762c4ba4d48b3d6fb2f4bce37ff#gqcDC)

在上两章节中我们知道，弹出框主体函数返回一个promise对象，在里面调用了生成模态框的代码。但是两个promise参数，resolve和reject被一起传给了showMessageBox函数（这样做为了让每个函数的分工更加明确），却还一直没有使用。

显然，当我们点击取消，会调用reject方法，表示我们取消这次异步动作
当我们点击确定，调用resolve方法，表示这次异步动作成功且进入下一步。

两个方法无一例外，执行后弹出框都将会关闭，关键的数据处理完毕通过方法返回，弹出框的任务也就完成了。

我们可以让两个按钮都绑定setVisible方法，在点击后使弹出框消失。

```jsx
<button
  class="btn btn-primary"
  @click="setVisible(false)"
>
   确定
</button>
<button
  class="btn btn-default"
  @click="setVisible(false)"
>
    取消
</button>
```

**但是问题是：这样子做怎么甄别使点的哪个按钮使弹出框隐藏的呢？(甚至有可能是点击呢右上角叉叉，或者模态框外部蒙版) 造成的影响就是无法判断该进行reslove方法还是reject方法**

所以推荐分别创建两个新的方法，以及一个用来储存弹出框关闭状态的属性

```jsx
<button
class="btn btn-primary"
@click="confirmBtnClick"
>
  确定
</button>
<button
class="btn btn-default"
@click="cancelBtnClick"
>
  取消
</button>

//===================================================

const state = reactive({
  visible: false,
  // 用来储存弹出框是以什么状态关闭的
  type: 'confirm'
  });

const setVisible = (isVisible) => {
    state.visible = isVisible;
  }

// 改变弹出框状态，同时关闭弹出框
const confirmBtnClick = () => {
    state.type = 'confirm';
    setVisible(false);
  }

  const cancelBtnClick = () => {
    state.type = 'cancel';
    setVisible(false);
  }
```

这样我们通过state.type属性就能判断点击呢弹出框的哪个按钮了

# **confirm与prompt的判别**

type属性有了，何时，以及怎么判断你点击了按钮，并触发reject或resolve方法呢？

[组件暴露属性](notion://www.notion.so/vue3-eeaed762c4ba4d48b3d6fb2f4bce37ff#PfEUR)

这一节我们将弹出框组件的state属性暴露出来，所以我们很容易拿到state.type属性。
**使用vue提供的watch侦听器，我们可以很方便侦听type属性的变化**。由于侦听器是惰性的，所以第一次加载type属性并不会触发它。

同时只有弹出框关闭，也就是当state.Visible属性为false时，才会触发侦听器

```jsx
 watch(vm.state, (state) => {
    if (!state.visible) {
      switch (state.type) {
        case 'cancel':
          reject();
          break;
        case 'confirm':
          resolve();
          break;
        default:
          break;
      }
     }
  })
```

## **侦听完毕后销毁弹出框组件？**

当侦听器侦听到弹出框隐藏，并根据type触发呢对应的方法后，这个弹出框的历史使命就完成了。下次调用它就是崭新的它，所以每次关闭弹出框后，最好能销毁掉这个组件，万一后面在该页面再也用不上它了，能节约一点性能。

```jsx
watch(vm.state, (state) => {
    if (!state.visible) {
      switch (state.type) {
        case 'cancel':
          reject();
          break;
        case 'confirm':
          resolve();
          break;
        default:
          break;
      }
      // 调用销毁组件的方法
      hideMessageBox(app);
     }
  })

const hideMessageBox = (app) => {
  // 销毁组件，也就是解绑它。
  app.unmount();
}
```

# **弹框的差异化处理**

## **prompt弹出框需要记录输入**

通过messageBox和messageBox.confirm创建的弹出框可以分为一组，他们十分的相似，只会显示一段信息，然后让用户进行确认操作。
而通过messageBox.prompt方法创建出来的弹出框则额外需要收集用户输入的信息，且在完成操作后将该信息返回至弹出框外部。

也就是，prompt弹出框组件需要一个输入框组件（input）
之前在给函数挂载属性时，我们已经往弹出框函数的options对象中创建了对应的type属性，通过这个属性我们能很方便的用v-if来动态显示一个输入框。

但是在这里，我们采取另一种方式，使用渲染函数区别type来动态渲染需要的组件

事实上，每个组件本质上就是一个函数或者对象，当一个函数返回一个渲染函数时，它也能当作组件直接在template中使用，同时由于是一个函数，在它当中能做许多灵活的操作

```jsx
// 我们将放有弹出框type类型的数组导出
export const fields = ['confirm', 'prompt'];

//================================================

/*
*之前往options对象中创建了field属性，在弹出框组件中可以使用props接收
*同时field属性只能是我们定义的，如果在fields数组中不包含属性值，应该返回一个错误
**/
const props = defineProps({
  field: {
    type: String,
    default: 'confirm',
    validator: (value) => {
      return fields.includes(value);
    }
  }
})

//==================================================

// state中定义第三个属性来储存prompt输入框的数值
const state = reactive({
    visible: false,
    promptValue: '',
    type: 'confirm'
  });

const ContentView = ({ field }) => {
  // 判断field属性的值，分别返回相应的渲染函数
  switch (field) {
    case !field || 'confirm':
      return h('p', null, props.content);
    case 'prompt':
      return h('input', {
        value: state.promptValue,
        onInput: e => state.promptValue = e.target.value,
        class: 'message-input'
      });
    default:
      return '';
  }
}

<div class="message-box-content">
  // 返回渲染函数的函数可以直接当作组件使用，这里将field属性传入
  <content-view :field="field" />
 </div>
```

## **自定义是否显示按钮**

许多弹出框的属性都是可以自定义的，标题，文字内容，按钮文字，当然也包括是否显示某个按钮。
如果不需要取消按钮，只需要在messageBox函数的options对象中传入相应的属性：**showCancelBtn:false**

```jsx
const props = defineProps({
    title: {
      type: String,
      default: 'Message',
    },
    content: {
      type: String,
      default: 'Message content.'
    },
    showCancelBtn: {
      type: Boolean,
      default: true
    },
    confirmBtnText: {
      type: String,
      default: 'OK'
    },
    cancelBtnText: {
      type: String,
      default: 'Cancel'
    },
    field: {
      type: String,
      default: 'confirm',
      validator: (value) => {
        return fields.includes(value);
      }
    }
  })

//==============================================================

// 通过传入的属性用v-if来控制按钮的显示隐藏
 <button
   class="btn btn-default"
   @click="cancelBtnClick"
   v-if="showCancelBtn"
  >
    {{ cancelBtnText }}
  </button>
```

# **弹出框关闭后要做什么**

弹出框的作用无非就两种：收集用户对某些操作的执行建议，收集用户输入的数据进行下阶段的操作

那么弹出框关闭后，特别是prompt弹出框，需要使用reslove方法需要返回一些东西出去

```jsx
  watch(vm.state, (state) => {
    if (!state.visible) {
      switch (state.type) {
        case 'cancel':
          reject();
          break;
        case 'confirm':
          // 当按下确认按钮，进行reslove方法，那收集到的信息（prompt方法）需要返回出去
          resolve(state.promptValue);
          break;
        default:
          break;
      }

      hideMessageBox(app);
    }
  })
```

```jsx
const showPromptMessageBox = () => {
    MyMessageBox.prompt({
      confirmBtnText: '确定',
      showCancelBtn: true,
      cancelBtnText: '取消',
      title: 'PromptMessageBox',
      content: 'This is PromptMessageBox content.'
    })
    .then((value) => {
      // resolve方法中我们要能获取到输入的内容，进行接下来的业务操作
       console.log('PromptMessageBox resolve:', value);
    }).catch(() => {
       console.log('PromptMessageBox reject');
    });
  }
```

# **后记**

这篇文章只是讲解整个弹出框编写的大致思路，里面使用的代码片段都是不完整的，只为方便理解我在干什么。
完整项目的代码放在这里：
todo（将会是个网址）

:::info
通过手写vue3的模态框，能学到这些东西：
**高阶函数**（函数返回一个函数）
vue3的**渲染函数**使用方法
vue3的动画过场组件 **transition** 
vue3 暴露组件方法与属性（expose）
vue3动态生成组件、插入页面的方法
函数也能挂载方法与属性
:::