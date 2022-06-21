# 虚拟Dom树与diff算法

现代前端框架中，并不会直接操作并修改Dom，取而代之的是操作虚拟Dom树。 每次代码改动后，生成新的虚拟Dom树，将它与改动之前的虚拟Dom树进行对比，使用diff算法将有差异的地方提取出来，收集成一个补丁包，再通过一定的方法去真实Dom树上安装补丁，实现部分的有目的性的改动。 这样做的好处是节约了Dom树整个生成渲染造成的性能浪费，特别是对于庞大的项目来说。

而这整个实现的过程主要分为以下几步：

通过特定方法生成虚拟节点 将虚拟节点渲染成真实节点 将真实节点挂载到指定地方 当代码改动后，生成新的虚拟dom树 对比新旧dom树，通过Diff算法生成改动的补丁包 将补丁包打上真实节点，生成改动后的真实节点

接下来将通过一个简单的demo实现以上几个步骤。当然，真实的这个过程十分复杂，这里demo只做核心的几个功能的实现。 自动生成虚拟节点的方法这里不实现，取而代之阐述虚拟节点的本质 diff算法及渲染真实节点的方法中需要判断的情况很多，这里只取几个简单的典型的如替换，删除，文本替换，属性修改。 本Demo只为理解整个虚拟Dom、diff算法的实现机制，并不是完全的重写。

# VirtuaDom

所谓的虚拟节点树，本质上是一个对象，利用对象的方式模拟真实Dom树。最后通过它用特定方式可以对照地转化成真实dom树。

virtualDom树（参照下图左部分）的每个节点一般使用某个方法创建，这里我们定义这个方法名为 `createEle-ment`。这个方法返回一个构造函数，接收三个参数，分别为： **type**: **节点名 接收一个string** (如下图节点0的 ul)； **props**: **属性 接收一个对象**（节点0 的 class: ‘list’，这个属性值为一个对象，也就是可以定义多个属性 )； **children**: **子节点 接收一个数组**（下图每条伸出的灰线代表一个拥有的子节点，如节点0拥有3个子节点）。

![](https://kerwin-1311807449.cos.ap-nanjing.myqcloud.com/diff.png)

image.png

```jsx
function createElement(type, props, children) {  
		return new Element(type, props, children)
	}
-----------------------------------------------------------  
// Element类简单的将储存的属性绑定给它的实例对象  
class Element {  
constructor(type, props, children) 
	{    
this.type = type    
this.props = props    
this.children = children  
		}
}
```

所以我们将上图左边的虚拟dom树创建出来，它的结构如下所示 现实的框架中，会有专门的方法将你的样式结构转化成虚拟dom树，这里我们不阐述这种方法怎么实现，所以就手动创建虚拟dom

```jsx
const vDom1 = createElement(
  'ul',
  {
    class: 'list',
    style: 'width: 300px; height: 300px; background-color: orange'
  },
  [
    createElement('li', { class: 'item', 'data-index': 0 }, [
      createElement('p', { class: 'text' }, ['第1个列表项'])
    ]),
    createElement('li', { class: 'item', 'data-index': 1 }, [
      createElement('p', { class: 'text' }, [
        createElement('span', { class: 'title' }, [])
      ])
    ]),
    createElement('li', { class: 'item', 'data-index': 2 }, ['第3个列表项'])
  ]
)
```

在控制台中打印，我们可以清晰看出它的结构 #

![](https://kerwin-1311807449.cos.ap-nanjing.myqcloud.com/elment.png)

有了虚拟Dom，但是结构还是不会出来，我们需要将虚拟Dom转换为真实的Dom挂载到页面上去，页面上才会显示真正的结构，这里就需要专门的方法来处理这一事务。

我们这里将渲染真实Dom树的方法叫做 render，挂载真实Dom的方法叫做renderDom # # 生成真实Dom树和挂载

我们用render方法来将虚拟Dom树转化为挂载用的真实Dom树

## 生成真实Dom树

这个方法主要处理三个任务：

1. 根据传入的vDom（virtuaDom简写，虚拟节点）的 **type** 属性来创建相应dom节点
2. 根据传入的 **props** 属性来处理添加节点上拥有的各种属性
3. 根据传入的 **children** 属性来递归处理该节点的子节点

```jsx
function render(vDom) {
  const { type, props, children } = vDom,
    el = document.createElement(type) // 1. 首先根据渲染的类型创建dom节点
  for (const key in props) {
    //  遍历第二个属性参数
    setAttrs(el, key, props[key])
    // 2. 调用设置属性的方法给节点设置方法
  }
  // 3. 处理第三个子节点参数
  children.map((c) => {
    c =
      c instanceof Element
        ? // 3.1 判断子元素是不是Element构造函数
          render(c)
        : // 3.2 如果是递归render函数设置前两个参数 （顺便处理它里面的子元素）
          document.createTextNode(c)
    // 3.3 子元素也可能是字符串，如果是，直接创建字节节点
    el.appendChild(c)
    // todo 3.4 将处理好的子元素添加到创建的dom节点内
  })
  return el
  // 3.5 返回创建的dom节点
}
```

处理属性值时用到了setAttrs方法，这个方法用于挂载节点的各个属性 dom节点属性多种多样，常见的有可以使用attribute操作的属性，value属性，操作css样式的style 每种情况应该分别做判断后处理

```jsx
// ! 设置属性需要三个参数，分别是节点，属性名，属性值
function setAttrs(node, prop, value) {
  // ! 需要判断属性名，然后分情况做处理
  switch (prop) {
    // ! 有些节点的value跟其他的节点不相同
    case 'value':
      if (node.tagName === 'INPUT' || node.tagName === 'TEXTAREA') {
        node.value = value
      } else {
        node.setAttribute(prop, value)
      }
      break
    // ! 如果属性名是style，直接给节点创建css样式
    case 'style':
      node.style.cssText = value
      break
    // ! 其他情况都可以使用这个方法创建属性
    default:
      node.setAttribute(prop, value)
      break
  }
}
```

处理节点的子节点 子节点会出现两种情况：是个普通节点，是个文本节点 虚拟Dom所有的节点都是使用createElement创建的，而这个函数会返回一个类Element的实例，所以可以使用 instanceof Element 来判断它是否是构造函数创建的实例。如果不是，那它就是文本节点，自然处理方式不同 普通节点，就需要使用render函数递归深度处理 文本节点，直接创建文本节点就好 最后创建好的子节点挂载到上面创建的Dom节点下去 （如果没有子节点，children属性值为空数组，map后不会进行任何处理）

```jsx
// 3. 处理第三个子节点参数
  children.map(c => {
    c = c instanceof Element  // 3.1 判断子元素是不是Element构造函数
      ?
      render(c) // 3.2 如果是递归render函数设置前两个参数 （顺便处理它里面的子元素）
      :
      document.createTextNode(c) // 3.3 子元素也可能是字符串，如果是，直接创建字节节点
    el.appendChild(c) // todo 3.4 将处理好的子元素添加到创建的dom节点内
  })
```

## 挂载真实Dom

生成好真实Dom树后，使用renderDom方法将其挂载到需要显示的页面上

```jsx
// 挂载函数就直接将处理好的真实节点挂载到指定的根节点上就行
function renderDom(rDom, rootEl) {
  rootEl.appendChild(rDom)
}
```

# diff算法与补丁包

渲染的真实Dom树也挂载好了，现在有个相关业务，我需要修改这个Dom树的结构，如下

```jsx
const vDom2 = createElement('ul', {
  class: 'list-wrap',
  style: 'width: 300px; height: 300px; background-color: orange'
}, [
  createElement('li', {
    class: 'item',
    'data-index': 0
  }, [
    createElement('p', {
      class: 'title'
    }, [
      '特殊列表项'
    ])
  ]),
  createElement('li', {
    class: 'item',
    'data-index': 1
  }, [
    createElement('p', {
      class: 'text'
    }, [])
  ]),
  createElement('div', {
    class: 'item',
    'data-index': 2
  }, [
    '第3个列表项'
  ])
]);
```

如果需要它也能替换显示到需要的页面上去，按照之前的步骤，就需要再将它转化为真实的dom树，然后进行渲染。

但是，看看下面的新旧vDom对照图：（右部红色处为修改过的地方，虚线灰色为被删除了的节点）

![](https://kerwin-1311807449.cos.ap-nanjing.myqcloud.com/diff.png)

我们发现，有修改的只有0、2、3、6、7对应的节点，如果仍然使用之前一整套处理方式，岂不是很浪费资源？ 明明1、4、5、8并没有任何修改，但渲染成真实Dom树时也是需要将他们一道处理的。

这是我们心想，有没有一种办法，能够像js文件按需导入一样，按需修改，只修改有变动的节点呢？ 有的，就是下面要介绍的diff算法

## diff算法

diff算法就是进行虚拟节点对比，并返回一个patch对象，用来存储两个节点不同的地方，最后用patch记录的消息去局部更新Dom 简单来说Diff算法就是在虚拟DOM树从上至下进行同层比对，如果上层已经不同了，那么下面的DOM全部重新渲染。这样的好处是算法简单，减少比对次数，加快算法完成速度

它有如下特点： **平级对比**，只对新旧虚拟树的平级节点做对比 **通过索引对比** 遍历顺序：**深度优先** 同节点下的子节点位置交换不会触发打补丁

说白呢就是利用生成补丁包的方式，告诉真实Dom树哪里需要需改哪里不需要进行改动

具体到代码，应该怎么实现呢？ （注意：真实的diff算法考虑的情况十分繁杂，这里只做四个情况的处理：删除，更改属性，文本替换，节点替换。大家理解它的原理就好 ）

domDiff函数需要新旧节点作为参数传入

```jsx
//patchTypes文件中做属性映射

export const ATTR = 'ATTR'
export const TEXT = 'TEXT'
export const REPLACE = 'REPLACE'
export const REMOVE = 'REMOVE'

---------------------------------------------------------------------

import { ATTR, TEXT, REPLACE, REMOVE } from './patchTypes'

let patches = {}, // 全局补丁包，每个节点处理完成之后会将其中所有变动放入其中

// 启动diff算法 需要传入新旧虚拟dom用于对比
function domDiff(oldVDom, newVDom) {
  // 初始化Index,调用主函数时传入
  let index = 0
  // 主函数，用于对比新旧虚拟dom的差异，根据不同点创建补丁包
  vNodeWalk(oldVDom, newVDom, index)
  // 完成后将最后的补丁包合集返回出去
  return patches
}
```