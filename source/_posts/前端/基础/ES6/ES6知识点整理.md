---
title: ES6知识点整理
date: 2017-11-04 10:35:34
updated: 2017-11-04 10:35:34
tags:
  - ES6
categories:
  - 基础
toc: true
---
ES6知识点整理。

ES6给我的感觉主要是一个简化的工具，一是将作用域、继承、原型这些JS比较难理解的概念做抽象，二是对对象、数组这些不太方便使用的方法增加方法，三是对函数模型做简化，默认值、Rest这些，四是对模块交互上做简化，比如Modules。有点C向C++过渡的意思。

<!-- more -->

# 知识点

## let/const

`let` 和 `const` 语句不会造成声明提升。会报错ReferenceError

`let` 和 `const` 是块级作用域语句。

使用 `let` 声明一个变量，使用 `const` 来声明一个不可改变的常量。

## 块级立即执行函数

ES6支持块级作用域，可以通过创建一个代码块（Block）来实现，不必通过创建一个函数来实现

## 箭头函数

一些时候，我们在函数嵌套中需要访问上下文中的 `this`。

- 一种通用的方式是把上下文中的 `this` 保存在一个变量里
- 也可以把 `this` 通过属性传进去
- 还可以直接使用 `bind`
- 使用 **箭头函数**

### 箭头函数需要注意的地方

当要求动态上下文的时候，就不能够使用箭头函数。也就是this的固定化

1. 在使用=>定义函数的时候，**this的指向是定义时所在的对象**，而不是使用时所在的对象
2. 不能够用作构造函数，这就是说，不能够使用new命令，否则就会抛出一个错误
3. 不能够使用arguments对象
4. 不能使用yield命令



## 字符串拓展

### .includes( )

替代以往判断内容`.indexOf(xxx) > -1` 的方式。

### .repeat( )

实现重复字符：

```javascript
'meow'.repeat(3); // 'meowmeowmeow'
```

### .contains()

返回布尔值，表示是否找到了参数字符串

### .startsWith()

返回布尔值，表示参数字符串是否在源字符串的头部

### .endsWith()

返回布尔值，表示参数字符串是否在源字符串的尾部

### 字符串模板字面量

- 在字符串中直接使用特殊字符，而不用转义。
- 支持直接插入变量
- 直接换行
- 内部可以使用表达式

```javascript
let text = ( `cat
    dog
    nickelodeon`
);
string${varible}string
```

## 数组的拓展

**Array.from()用于将两类对象转为真正的数组：**类似数组的对象（array-like object）和可遍历（iterable）的对象，其中包括ES6新增的Set和Map结构。

**【重点】find()和findIndex()**，意义较大，如下：

```javascript
[1,5,10,15].find( function(value, index, arr){ return value > 9; } ); // 返回10
[1,5,10,15].findIndex( function(value, index, arr){ return value > 9; } ); // 返回2
```

**fill()使用给定值，填充一个数组：**

```javascript
newArray(3).fill(7)
```



## Maps

**Maps** 是一个JavaScript中很重要（迫切需要）的数据结构。

Maps让我们使用 `set`，`get` 和 `search` 操作数据。

Maps最强大的地方在于我们不必只能使用字符串来做key了，现在可以使用任何类型来当作key，而且key不会被强制类型转换为字符串。

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

**遍历：**

```javascript
for (let key of map.keys()) {
for (let [key, value] of map.entries()) {
```

**ES6提供三个新的方法：entries()，keys()和values()用于遍历数组**。

可以用for...of循环进行遍历，唯一的区别是keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历



## Object

### Object.setPrototypeOf()，Object.getPrototypeOf()



## for...of循环

for...of循环可以代替数组实例的forEach方法

**和for...in的区别**

- for...of可以遍历键与值，for...in只能遍历键名
- 对于普通的对象，for...of结构不能直接使用，会报错，必须部署了iterator接口后才能使用。但是，这样情况下，for...in循环依然可以用来遍历键名





## WeakMaps

使用WeakMaps来保存我们私有数据的理由之一是不会暴露出属性名

通常情况下，在涉及DOM元素存储和缓存的情况下，使用WeakMaps是非常有效的。



## 解构

```javascript
let [a, b, c, d] = [1, 2, 3, 4];	//数组
let luke = { occupation: 'jedi', father: 'anakin' };	//对象
let {occupation, father} = luke;
```

该特性更多的用处是在遍历Map结构的时候，可以如下：

```javascript
for( let [ key,value] of map ) 
{ console.log ( key +':'+ value);}
```



## Modules

### export

```javascript
export default api;
```

总是在模块的 **最后** 使用 `export default` 方法。它让模块的出口更清晰明了，节省了阅读整个模块来寻找出口的时间。

### import

```javascript
import * as content from './content' 
```

**注意**：被导出的值是被 **绑定的（原文：bingdings）**，而不是引用。
所以，改变一个模块中的值的话，会影响其他引用本模块的代码，一定要避免此种改动发生。

## 参数默认值

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

## Rest

使用 **rest** 操作符，我们可以给函数传入一个不确定数量的参数列表：

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```



## Class

class语法相对原型、构造函数、继承更接近传统语法

```javascript
class Animal {
    constructor () {
        this.type = 'animal'
    }
    says(say) {
        console.log(this.type + 'says' + say)
    }
}
 let animal = new Animal()
 animal.says('hello') // animal says hello

 class Cat extends Animal {
     constructor() {
         super()
         this.type = 'cat'
     }
 }
 let cat = new Cat()
 cat.says('hello') // cat says hello
```

contructor内部定义的方法和属性是实例对象自己的，不能通过extends 进行继承。

在ES6中，子类的构造函数必须含有super函数，super表示的是调用父类的构造函数，虽然是父类的构造函数，但是this指向的却是cat

## Object.assign()

Object.assign方法用来将源对象（source）的所有可枚举属性，复制到目标对象（target）

```javascript
var target = { a:1};
var source1 = { b:2};
var source2 = { c:3};
Object.assign(target, source1, source2);
target// {a:1, b:2, c:3}

var n = Object.assign(a,b,c)向n中添加a,b,c的属性
```

**应用**

- 为对象添加属性
- 为对象添加方法


- 克隆对象
- 为属性指定默认值

## Symbol

ES6引入了一种新的原始数据类型Symbol，表示独一无二的ID。它通过Symbol函数生成。

Symbols常用的一个使用场景，尤其是使用 `Symbol.for(key)` 方法，是用于实现代码间的互操作。



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

**Promise** 对象用于延迟(deferred) 计算和异步(asynchronous ) 计算。一个Promise对象代表着一个还未完成，但预期将来会完成的操作。

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
- 尽量将catch方法写在链式操作的最后。错误会一直冒泡到最后，catch放在最后会捕捉到所有错误。
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



# 问题

## Case1.promise执行顺序

```javascript
setTimeout(function(){
    console.log(1);
}, 0)

new Promise(function executor(resolve){
    console.log(2);
    for(var i = 0; i < 1000; i++){
        i = 9999 && resolve();
    }
    console.log(3);
}).then(function(){
    console.log(4);
})

console.log(5);
// 2， 3， 5， 4， 1
```

- 在定时器，事件，ajax等操作的时候，**会使一个异步操作，会把该操作放到一个task queue里，需要等当前主线程的任务完成后，会读取任务队列(task queue)中的是事件。**
- promise中的then操作是放在执行栈，也就是主线程的最后。 



## Case2.promise的原理？jquery的ajax返回的是promise对象吗？

jquery的ajax返回的是**deferred对象**，通过**promise的resolve()方**法将其转换为promise对象。

```javascript
var jsPromise = Promise.resolve($.ajax('/whatever.json'));
```



## Case3.promise对象捕获错误

```javascript
//使用throw添加错误事件
var p = new Promise(function(resolve, reject) {
    resolve("ok");
    throw new Error('error0');
    //setTimeout(function() { throw new Error('error1') }, 0);
});
p.then(function(value){
    console.log(value) 
　})
 .catch(function(err){
    console.log(err)
    });
process.on('unhandledRejection', function (err, p) { console.error('catch exception:',err.stack) });

//设置定时器来抛出错误事件
var p = new Promise(function(resolve, reject) {
    resolve("ok");
    //throw new Error('error0');
    setTimeout(function() { throw new Error('error1') }, 0);
});
p.then(function(value){
    console.log(value) 
　})
 .catch(function(err){
    console.log(err)
    });
process.on('unhandledRejection', function (err, p) { console.error('catch exception:',err.stack) });

//同时添加错误事件
var p = new Promise(function(resolve, reject) {
    resolve("ok");
    throw new Error('error0');
    setTimeout(function() { throw new Error('error1') }, 0);
});
p.then(function(value){
    console.log(value) 
　})
 .catch(function(err){
    console.log(err)
    });
process.on('unhandledRejection', function (err, p) { console.error('catch exception:',err.stack) });
```

第一个只会打印出 “ok”。状态被设定为fulfilled之后，再进行抛出错误处理，错误也不会被后续的catch方法捕获到。

第二个先打印出“ok”，之后抛出process里面定义的错误。虽然状态已经变为fulfilled，但是定时器抛出的错误属于异步抛出的错误，无法被try catch捕获到，因此和Promise对象无关，所以错误可以正常的抛出来

第三个只打印出“ok”。当执行throw之后，虽然错误未被外部函数捕获处理，但这也是个实实在在存在的错误啊，对于javascript来说，有错就不会继续往下面执行了。所以并不会执行到定时器抛出错误就停止了



# 参考文章

- [前端面试之ES6篇（高产似母猪）](https://segmentfault.com/a/1190000011344301)
- [ECMAScript 6重点一览——基础篇](http://www.jianshu.com/p/c4ba3b25ca0d)
- [面试考察之Promise对象](http://blog.csdn.net/shuidinaozhongyan/article/details/77864182)
- ​