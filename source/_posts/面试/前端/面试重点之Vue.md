---
title: 面试重点之Vue
date: 2018-01-01 11:33:34
updated: 2018-01-01 15:25:00
tags:
  - 面试攻略
  - Vue
categories:
  - 面试
toc: true
---
本文是我为了应付面试的产物，针对性强不怕没的说，但如实说我一点皮毛还不懂。

为自己加个TODO：

- 阅读源码，实现一套双向绑定和渲染。
- 补充本文的一些细节+渲染+Vuex部分，写的很粗，其实是自己不懂乱copy

面试的框架部分，需要掌握以下：

> - 实现原理、源码
> - 对一些实现的思考

<!-- more -->

# 面试重点之Vue

[TOC]

## Vue的双向数据绑定原理是什么？

### 基本原理

这是 ES5 的特性，通过在 setter/getter，在属性被访问和修改时通知变化。比如 vue 就是用的这个特性。

vue.js 是采用**数据劫持结合发布者-订阅者模式**的方式，通过`Object.defineProperty()`来劫持各个属性的`setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调。

```javascript
function declar(obj){
   var rwprop = 0;
   Object.defineProperty(obj, "rwprop", {
        get : () => rwprop // getter
   });
   return obj
}

var a = {};

a = declar(a);

a.rwprop = 1 // 调用了setter
console.log(a.rwprop) // 调用了getter
```

### 具体步骤

**第一步：需要observe的数据对象进行递归遍历**，包括子属性对象的属性，都加上 `setter`和`getter`。这样的话，给这个对象的某个值赋值，就会触发`setter`，那么就能监听到了数据变化

**第二步：compile解析模板指令**，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图

**第三步：Watcher订阅者是Observer和Compile之间通信的桥梁**，主要做的事情是:
1、在自身实例化时往属性订阅器(dep)里面添加自己
2、自身必须有一个update()方法
3、待属性变动dep.notice()通知时，能调用自身的update()方法，并触发Compile中绑定的回调，则功成身退。

**第四步：MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者**，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。



#### 1. 实现 observer

思路：我们知道Object.defineProperty的特性了，我们就利用它的set和get。。我们将要observe的对象，通过递归，将它所有的属性，包括子属性的属性，都给加上set和get，这样的话，给这个对象的某个属性赋值，就会触发set

```javascript
export default class  Observer{
  constructor(value) {
    this.value = value
    this.walk(value)
  }
  //递归。。让每个字属性可以observe
  walk(value){
    Object.keys(value).forEach(key=>this.convert(key,value[key]))
  }
  convert(key, val){
    defineReactive(this.value, key, val)
  }
}

export function defineReactive (obj, key, val) {
  var childOb = observe(val)
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: ()=>val,
    set:newVal=> {      
     childOb = observe(newVal)//如果新赋值的值是个复杂类型。再递归它，加上set/get。。
     }
  })
}

export function observe (value, vm) {
  if (!value || typeof value !== 'object') {
    return
  }
  return new Observer(value)
}
```

所以，我们是不是应该写一个消息－订阅器呢？这样的话，一触发set方法，我们就发一个通知出来，然后，订阅这个消息的，就会怎样？。。。对咯。。收到消息。。。触发回调。

#### 2. 消息－订阅器

维护一个数组，这个数组，就放订阅着属性订阅器，一旦触发notify，订阅者就调用自己的update方法

```javascript
export function defineReactive (obj, key, val) {
  var dep = new Dep()
  var childOb = observe(val)
  
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: ()=>{
      // 说明这是watch 引起的
      if(Dep.target){
        dep.addSub(Dep.target)
      }
      return val
    },
    set:newVal=> {
      var value =  val
      if (newVal === value) {
        return
      }
      val = newVal
      childOb = observe(newVal)
      dep.notify()
    }
  })
}
```

谁是订阅者。。对，是Watcher。。一旦 `dep.notify()`就遍历订阅者，也就是Watcher，并调用他的`update()`方法

#### 3. 实现一个 Watcher

```javascript
export default class Watcher {
  constructor(vm, expOrFn, cb) {
    this.cb = cb
    this.vm = vm
    //此处简化.要区分fuction还是expression,只考虑最简单的expression
    this.expOrFn = expOrFn
    this.value = this.get()
  }
  update(){
    this.run()
  }
  run(){
    const  value = this.get()
    if(value !==this.value){
      this.value = value
      this.cb.call(this.vm)
    }
  }
  get(){
    Dep.target = this
    //此处简化。。要区分fuction还是expression
    const value = this.vm._data[this.expOrFn]
    Dep.target = null
    return value
  }
}
```

#### 4. 实现一个 Vue

```javascript
export default class Vue {
  constructor (options={}) {
    //这里简化了。。其实要merge
    this.$options=options
    //这里简化了。。其实要区分的
    let data = this._data=this.$options.data
    Object.keys(data).forEach(key=>this._proxy(key))
    observe(data,this)
  }


  $watch(expOrFn, cb, options){
    new Watcher(this, expOrFn, cb)
  }

  _proxy(key) {
    var self = this
    Object.defineProperty(self, key, {
      configurable: true,
      enumerable: true,
      get: function proxyGetter () {
        return self._data[key]
      },
      set: function proxySetter (val) {
        self._data[key] = val
      }
    })
  }
}
```



## Vue通信

### vue父子组件通信

#### 子 -> 父：$emit

```vue
// 父组件
<single-address @edit-address="editAddress"></single-address>

// 子组件
methods: {
 editAddress () {
  this.$emit('edit-address', false)
 }
}
```

#### 父 -> 子：props

```javascript
// 父组件
<one-address :addressitems="addressitems"></one-address>

// 子组件
<div>{{ addressitems.partment }}{{ addressitems.address }}</div>
export default {
  props: {
    addressitems: Object
  }
}
```

### 非父子组件

```javascript
var bus = new Vue()
// 触发组件 A 中的事件
bus.$emit('id-selected', 1)
// 在组件 B 创建的钩子中监听事件
bus.$on('id-selected', function (id) {
 console.log(id)
})
```

### Vuex





## 条件渲染

v-if指令

v-show指令

v-else指令

v-for指令

v-bind指令：给DOM绑定元素属性，语法如下：`v-bind:argument="expression"`

v-on指令



## 事件处理

Methods 并不是创建自定义函数的唯一方式。

也可以使用 `watch` 。

两者的区别是 methods 适合小的、同步的计算，而 `watch` 对于多任务、异步或者响应数据变化时的开销大的操作是有利的。我经常在动画中使用 watch 。



## 组件，Props，Slots

从父组件向子组件传递数据的方式称为 **props**。

我们也可以向 props 中添加验证，这和 React 中的 `PropTypes` 类似。

### Slots

重用组件，并用相同的数据或功能填充它们

你也可以使用具名 slot 。如果一个组件中有两个 slot， 可以通过添加 name 属性区分它们



## 生命周期钩子

可以使用的钩子有： `beforeCreate, created, beforeMount, mounted, beforeUpdate, updated, activated, deactivated, beforeDestroy`, `destroyed` 。



## Vuex

- **Getters** 可以在模板中静态的显示数据。换句话说，getters 可以读取数据，但不能改变状态。
- **Mutations** 允许更新状态，但永远是同步的。Mutations 是 store 中改变状态数据的唯一方式。
- **Actions** 允许异步更新状态，但是需要使用一个已经存在的 mutation 。如果你需要以特定的顺序同时执行不同的 mutations 会非常有用。

`store.js`：

```javascript
export const store = new Vuex.Store({
  state: {
    counter: 0
  },
  // 展示内容, 无法改变状态
  getters: {
    tripleCounter: state => {
      return state.counter * 3;
    }
  },
  // 改变状态
  //mutations 永远是同步的
  mutations: {
    // 显示传递的载荷 payload, 用 num 表示
    increment: (state, num) => {
      state.counter += num;
    }
  }, 
  // 提交 mutation, 这是异步的
  actions: {
    // 显示传递的载荷 payload, 用 asynchNum ( 一个对象 )表示
    asyncDecrement: ({ commit }, asyncNum) => {
      setTimeout(() => {
        // asyncNum 对象可以是静态值
        commit('decrement', asyncNum.by);
      }, asyncNum.duration);
    }
  }
});
```


## Vue2 原理浅谈

- 输入了数据状态，输出视图（我们不关心中间的过程，它们均由框架帮助我们实现）；
- 数据变化侦测，从而re-render视图。
- 数据变化侦测分为`系统不可感知数据变化`和`系统可感知数据变化`
- 系统不可感知数据变化：React就是通过setState发信号告诉系统有可能数据变了,然后通过virtual dom diff去渲染视图，angular则是有一个脏值检查流程，遍历比对
- 系统可感知数据变化：通过观察者模式，使用Observable (可观察对象)，Observer (观察者)(或者是watcher)去订阅

上者`系统不可感知数据变化`,粒度粗，有时候还得手动优化（比如pureComponet和shouldComponentUpdate)去跳过一些数据不会更新的视图从而提升性能

下者`系统可感知数据变化`,粒度细，但是绑定大量观察者，有大量的依赖追踪的内存开销

**Vue2**，它采用了折中粒度的方式，粒度到组件级别上，由watcher订阅数据，当数据变化我们可以得知哪个组件数据变了，然后采用virtual dom diff的方式去更新相应组件。

### 数据响应原理

#### Object.defineProperty

> app.message如何拿到vue data中的message?

其实也是跟`Object.defineProperty`有关，Vue在初始化数据的时候会遍历data代理这些数据，其实就是用`Object.defineProperty`多加了一层的访问

#### 观察者模式（Observer, Watcher, Dep)

- Observer类主要用于给Vue的数据`defineProperty`增加getter/setter方法，并且在getter/setter中收集依赖或者通知更新
- Watcher类来用于观察数据（或者表达式）变化然后执行回调函数（其中也有收集依赖的过程），主要用于$watch API和指令上
- Dep类就是一个可观察对象，可以有不同指令订阅它（它是多播的）

观察者模式,跟发布／订阅模式有点像，区别是发布和订阅以及发布后调度订阅者的操作都是由中心统一完成，但是观察者模式则没有这样的中心

##### 如何实现观察者模式呢？

> 怎么实现订阅

Dep由于是可以被多个Watcher所订阅的，所以它拥有着订阅者数组，订阅了它，就把Watcher放入数组即可

```javascript
class Dep {
  constructor () {
    this.subs = []
  }
  notify () {
    const subs = this.subs.slice()
    for (let i = 0; i < subs.length; i++) {
        subs[i].update()
    }
  }
  addSub (sub) {
    this.subs.push(sub)
  }
}

class Watcher {
  constructor () {
  }
  update () {
  }
}

let dep = new Dep()
dep.addSub(new Watcher()) // Watcher订阅了依赖
```

> 怎么通知notify

把每一个数据当成一个Dep实例，然后setter的时候去notify就行了，所以我们可以在defineProperty中new Dep()，通过闭包setter就可以取到Dep实例

>怎么让我的Watcher实例订阅到这个Dep实例

从get里面做手脚，在get中是可以取到这个Dep实例的，所以可以在执行watch操作的时候，执行获取数值，触发getter去收集依赖

在new Watcher的时候会执行一个求值的操作，然后因为标记了这个Watcher触发的，所以收集了依赖，也就是观察者订阅了依赖

> case

```javascript
const vm = new Vue({
  data: {
    msg: 1,
  }
})
vm.$watch("msg", () => console.log("msg变了"));
vm.msg = 2; //输出「变了」
```

- 首先是new Vue遍历了数据，给数据defineProperty加上了getter/setter方法
- 我们`new Watcher(vm, 'msg', () => console.log("msg变了"))`,首先标记了全局变量Dep.target = 该Watcher实例，然后执行msg的get操作，触发到了它的getter，然后dep成功获取到它的订阅者，放入它的订阅者数组，最后我们将Dep.target = null
- 最后设置vm.msg = 2,触发到了setter,闭包中的dep.notify,遍历订阅者数组，执行相应的回调操作。

### Render

第一部分其实就是各种正则了，对左右开闭标签的匹配以及属性的收集，通过栈的形式，不断出栈入栈去匹配以及更换父节点，最后生成一个对象，包含children,children又包含children的对象

第二部分则是以第一部分为基础，根据节点类型找出一些静态的节点并标记

第三部分就是生成render函数代码了

知道了`vm._render()`创建了vnode返回，接下来就是`vm._update`了

#### 数据到视图的整体流程

所以到这里我们就可以得出一个数据到视图的整体流程的结论了

- 在组件级别，vue会执行一个new Watcher
- new Watcher首先会有一个求值的操作，它的求值就是执行一个函数，这个函数会执行render，其中可能会有编译模板成render函数的操作，然后生成vnode(virtual dom)，再将virtual dom应用到视图中
- 其中将virtual dom应用到视图中（这里涉及到diff后文会讲），一定会对其中的表达式求值(比如{{message}},我们肯定会取到它的值再去渲染的），这里会触发到相应的getter操作完成依赖的收集
- 当数据变化的时候，就会notify到这个组件级别的Watcher,然后它还会去求值，从而重新收集依赖，并且重新渲染视图


## vue生命周期

1、什么是vue生命周期？
答： Vue 实例从创建到销毁的过程，就是生命周期。也就是从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期。2、vue生命周期的作用是什么？
答：它的生命周期中有多个事件钩子，让我们在控制整个Vue实例的过程时更容易形成好的逻辑。

3、vue生命周期总共有几个阶段？
答：它可以总共分为8个阶段：创建前/后, 载入前/后,更新前/后,销毁前/销毁后

4、第一次页面加载会触发哪几个钩子？
答：第一次页面加载时会触发 beforeCreate, created, beforeMount, mounted 这几个钩子

5、DOM 渲染在 哪个周期中就已经完成？
答：DOM 渲染在 mounted 中就已经完成了。

6、简单描述每个周期具体适合哪些场景？
答：生命周期钩子的一些使用方法： beforecreate : 可以在这加个loading事件，在加载实例时触发 created : 初始化完成时的事件写在这里，如在这结束loading事件，异步请求也适宜在这里调用 mounted : 挂载元素，获取到DOM节点 updated : 如果对数据统一处理，在这里写上相应函数 beforeDestroy : 可以做一个确认停止事件的确认框 nextTick : 更新数据后立即操作dom