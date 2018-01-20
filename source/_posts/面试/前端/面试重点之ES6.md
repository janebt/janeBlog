---
title: 面试重点之ES6
date: 2018-01-01 11:33:34
updated: 2018-01-01 15:25:00
tags:
  - 面试攻略
  - ES6
  - JavaScript
categories:
  - 面试
toc: true
---
ES6问题在面试中是重中之重。我感觉是比较难的，如果仅仅是使用，那倒很轻松，但是其中会问实现、原理等，涉及的东西很多。

面试的ES6部分，需要掌握：

> - 掌握每个概念存在的意义，是为了解决什么类型问题
> - 掌握每个概念的实现原理，ES5怎么写
> - 掌握每个概念的一些使用上的坑和技巧

TODO：

> - 提到的概念深入细化

<!-- more -->

# 面试重点之ES6

## 知识点

- let/const
- 立即执行函数
- 箭头函数
- 字符串拓展
- 字符串模板字面量
- 数组的拓展
- Maps
- Object
- for...of循环
- 解构
- Modules
- 参数默认值
- Class
- Object.assign()
- Symbol
- Promise



## Promise

它的思想是，每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。比如，f1的回调函数f2,可以写成：

```javascript
f1().then(f2);
f1().then(f2).then(f3);
f1().then(f2).fail(f3);
```

如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。

API:

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
        .catch(reason => console.log(reason));
```

这里有两个处理函数，**resolve**（当Promise执行成功完毕时调用的回调函数） 和 **reject** （当Promise执行不接受时调用的回调函数）

**Promises的好处**：

- 大量嵌套错误处理回调函数会使代码变得难以阅读理解。使用Promises，我们可以通过清晰的路径将错误事件让上传递，并且适当地处理它们。

此外，Promise处理后的值，无论是解决（resolved）还是拒绝（rejected）的结果值，都是不可改变的。

### 1. 什么是promise对象，它能干什么？

**Promise** 对象用于延迟(deferred) 计算和异步(asynchronous ) 计算.。一个Promise对象代表着一个还未完成，但预期将来会完成的操作。

**Promise** 对象是一个返回值的代理，这个返回值在promise对象创建时未必已知。它允许你为异步操作的成功或失败指定处理方法。 这使得异步方法可以像同步方法那样返回值：异步方法会返回一个包含了原返回值的 promise 对象来替代原返回值。

**Promise** 对象有以下几种状态:

- *pending*: 表示一个初始状态, 非 fulfilled 或 rejected。
- *fulfilled*: 成功的操作。
- *rejected*: 失败的操作。

每一个异步任务都会返回一个Promise对象，该对象有一个then方法，允许指定回调函数。可以根据Promise对象的状态相应的去执行对应的回调函数。

### 2. 常用的API

#### 1.Promise.prototype.then()

它的作用是为promise实例添加状态改变时的回调函数。

then()方法的第一个参数是resolved状态的回调函数，第二个参数（可选）是rejected状态的回调函数。then方法返回的是一个新的promise实例。

then方法的第二个参数一般不推荐写。有以下两个原因：

- 由于是链式操作，这个then方法之后还可能会有其他操作，如果此时把错误捕捉的函数放在后面方法前边的话，并且之后再无错误捕获方法，then之后的错误就会捕捉不到。
- 在then方法里面，两个参数都是回调函数写了一大堆，这样结构看起来比较混乱。

```javascript
var p=new Promise(function(resolve, reject){
    resolve("ok");    
});
p.then(function(value){console.log(val)},
 function(err)(console.log(err))       
);
```

#### 2.Promise.prototype.catch()

这个方法是.then(null,rejection)的别名，这也能看出这个方法是专门只能用来捕获错误信息，用于指定发生错误时的回调函数。

要注意一下几点：

- 当promise状态已经变成resolved的时候，再抛出错误时是无效的。
- 尽量将catch方法写在链式操作的最后。**错误会一直冒泡到最后，catch放在最后会捕捉到所有错误。**
- 当没有使用catch方法指定错误处理函数的回调函数时，promise对象里面抛出的错误不会传递到外层的代码。

#### 3. Promise.resolve()

这个方法的作用就是将现有的对象转化为Promise对象，进而可以执行这些方法。

```javascript
Promise.resolve("foo");
```

#### 4. Promise.all()

这个方法用于将多个promise实例，包装成一个新的promise实例。

```javascript
var p=Promise.all([p1,p2,p3]);
```

要注意一下两点：

- 只有当p1,p2,p3状态都变为fulfilled之后，p的状态才会变为fulfilled。
- 只要p1.p2,p3中有任意一个状态变为rejected，p的状态就会变为rejected。

### 3. 实现异步编程的原理

Promise对象相当于是一个状态机，在其内部使用resolve方法，使其由初始状态变为成功时的fulfilled状态或者执行失败后的rejected状态。这时内部的工作就完成了，开始由外部监听其内部的状态的改变，调用then()方法（catch()方法相当于then内部的第二个参数方法）对应的状态调用对应的处理函数。



## 箭头函数需要注意的地方

当要求动态上下文的时候，就不能够使用箭头函数。也就是this的固定化

1. 在使用=>定义函数的时候，**this的指向是定义时所在的对象**，而不是使用时所在的对象
2. 不能够用作构造函数，这就是说，不能够使用new命令，否则就会抛出一个错误
3. 不能够使用arguments对象
4. 不能使用yield命令



## Proxy

使用 Proxy 的好处是：对象只需关注于核心逻辑，一些非核心的逻辑（如：读取或设置对象的某些属性前记录日志；设置对象的某些属性值前，需要验证；某些属性的访问控制等）可以让 Proxy 来做。从而达到关注点分离，降级对象复杂度的目的。

Proxy用于修改某些操作的默认行为，用来代理有些行为。Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。

```javascript
var engineer = { name: 'Joe Sixpack', salary: 50 };

var interceptor = {
  set: function (receiver, property, value) {
    console.log(property, 'is changed to', value);
    receiver[property] = value;
  }
};

engineer = Proxy(engineer, interceptor);
engineer.salary = 60;	//会触发处理器
```

**（1）get(target, propKey, receiver)**

拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。

最后一个参数`receiver`是一个对象，可选，参见下面`Reflect.get`的部分。

**（2）set(target, propKey, value, receiver)**

拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。

**（3）has(target, propKey)**

拦截`propKey in proxy`的操作，返回一个布尔值。

**（4）deleteProperty(target, propKey)**

拦截`delete proxy[propKey]`的操作，返回一个布尔值。

**（5）ownKeys(target)**

拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。

**（6）getOwnPropertyDescriptor(target, propKey)**

拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。

**（7）defineProperty(target, propKey, propDesc)**

拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。

**（8）preventExtensions(target)**

拦截`Object.preventExtensions(proxy)`，返回一个布尔值。

**（9）getPrototypeOf(target)**

拦截`Object.getPrototypeOf(proxy)`，返回一个对象。

**（10）isExtensible(target)**

拦截`Object.isExtensible(proxy)`，返回一个布尔值。

**（11）setPrototypeOf(target, proto)**

拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。

如果目标对象是函数，那么还有两种额外操作可以拦截。

**（12）apply(target, object, args)**

拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。

**（13）construct(target, args)**

拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。



## Generator 函数

异步编程的方法

- 回调函数
- 事件监听
- 发布/订阅
- Promise 对象

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。异步操作需要暂停的地方，都用 yield 语句注明。

Generator 函数不同于普通函数的另一个地方，即执行它不会返回结果，返回的是指针对象。

```javascript
function* gen(x){
  var y = yield x + 2;
  return y;
}

var g = gen(1);
g.next() // { value: 3, done: false }
g.next() // { value: undefined, done: true }
```

**Case**

```javascript
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}
```

```javascript
var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

一句话说，next方法参数的作用，是覆盖掉上一个yield语句的值。

带参数跟不带参数的区别是，带参数的情况，首先第一步就是将上一个yield语句重置为参数值，然后再照常执行剩下的语句。**总之，区别就是先有一步先重置值，接下来其他全都一样。**



## ES7的Async/Await

```javascript
var sleep = function (time) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve();
        }, time);
    })
};

var start = async function () {
    // 在这里使用起来就像同步代码那样直观
    console.log('start');
    await sleep(3000);
    console.log('end');
};

start();
```

- async 表示`这是一个async函数`，`await只能用在这个函数里面`。
- await 表示在这里`等待promise返回结果`了，再继续执行。
- await 后面跟着的`应该是一个promise对象`

### 特点

await等待的虽然是promise对象，但不必写`.then(..)`，直接可以得到返回值。

await看起来就像是同步代码，所以可以理所当然的写在`for`循环里，不必担心以往需要`闭包`才能解决的问题。

值得注意的是，`await`必须在`async函数的上下文中`的。
