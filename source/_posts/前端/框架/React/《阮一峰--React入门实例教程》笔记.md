---
title: 《阮一峰--React入门实例教程》笔记 
date: 2017-01-04 10:35:34
updated: 2017-01-04 10:35:34
tags:
  - React
categories:
  - 框架
toc: true
---
《阮一峰 --React 入门实例教程》笔记 

个人认为讲的太浅，无门槛但是收获也低。推荐官方文档。

<!-- more -->

## 《React 入门实例教程》笔记

> 作者： [阮一峰](http://www.ruanyifeng.com/)

### 一、HTML 模板

首先，最后一个 `<script>` 标签的 `type` 属性为 `text/babel` 。这是因为 React 独有的 JSX 语法，跟 JavaScript 不兼容。凡是使用 JSX 的地方，都要加上 `type="text/babel"` 。

上面代码一共用了三个库： `react.js` 、`react-dom.js` 和 `Browser.js` ，它们必须首先加载。其中，`react.js` 是 React 的核心库，`react-dom.js` 是提供与 DOM 相关的功能，`Browser.js`的作用是将 JSX 语法转为 JavaScript 语法，这一步很消耗时间，实际上线的时候，应该将它放到服务器完成。

### 二、ReactDOM.render()

ReactDOM.render 是 React 的最基本方法，用于将模板转为 HTML 语言，并插入指定的 DOM 节点。

注意：有逗号无分号

```javascript
ReactDOM.render(
  <HelloMessage name="john"></HelloMessage>,
  document.getElementById('example')
)
```

### 三、JSX 语法

 JSX 的基本语法规则：遇到 HTML 标签（以 `<` 开头），就用 HTML 规则解析；遇到代码块（以 `{` 开头），就用 JavaScript 规则解析。

JSX 允许直接在模板插入 JavaScript 变量。如果这个变量是一个数组，则会展开这个数组的所有成员

### 四、组件

- 所有组件类都必须有自己的 `render` 方法，用于输出组件。


- 组件类的第一个字母必须大写，否则会报错
- 组件类只能包含一个顶层标签，否则也会报错。
- 组件的属性可以在组件类的 `this.props` 对象上获取
- 添加组件属性，有一个地方需要注意，就是 `class` 属性需要写成 `className` ，`for` 属性需要写成 `htmlFor` ，这是因为 `class` 和 `for` 是 JavaScript 的保留字。

### 五、this.props.children

`this.props.children` 属性。它表示组件的所有子节点

需要注意， `this.props.children` 的值有三种可能：如果当前组件没有子节点，它就是 `undefined` ;如果有一个子节点，数据类型是 `object` ；如果有多个子节点，数据类型就是 `array` 。所以，处理 `this.props.children` 的时候要小心。

React 提供一个工具方法 [`React.Children`](https://facebook.github.io/react/docs/top-level-api.html#react.children) 来处理 `this.props.children` 。我们可以用 `React.Children.map` 来遍历子节点，而不用担心 `this.props.children` 的数据类型是 `undefined` 还是 `object`。

### 六、PropTypes

组件类的`PropTypes`属性，就是用来验证组件实例的属性是否符合要求

此外，`getDefaultProps` 方法可以用来设置组件属性的默认值。s

### 七、获取真实的DOM节点

组件是virtual DOM，只有当它插入文档以后，才会变成真实的 DOM 。

有时需要从组件获取真实 DOM 的节点，这时就要用到 `ref` 属性

需要注意的是，由于 `this.refs.[refName]` 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。

React 组件支持很多事件，除了 `Click` 事件以外，还有 `KeyDown` 、`Copy`、`Scroll` 等

注意：引用是refs，定义是ref

```javascript
var MyComponent = React.createClass({
  handleClick: function() {
    this.refs.myTextInput.focus();
  },
  render: function() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the text input" onClick={this.handleClick} />
      </div>
    );
  }
});
```

### 八、this.state

将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI 

`getInitialState` 方法用于定义初始状态，也就是一个对象，这个对象可以通过 `this.state` 属性读取。每次修改以后，自动调用 `this.render` 方法，再次渲染组件。

由于 `this.props` 和 `this.state` 都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，`this.props` 表示那些一旦定义，就不再改变的特性，而 `this.state` 是会随着用户互动而产生变化的特性。

### 九、表单

用户在表单填入的内容，属于用户跟组件的互动，所以不能用 `this.props` 读取

文本输入框的值，不能用 `this.props.value` 读取，而要定义一个 `onChange` 事件的回调函数，通过 `event.target.value` 读取用户输入的值。

### 十、组件的生命周期

组件的[生命周期](https://facebook.github.io/react/docs/working-with-the-browser.html#component-lifecycle)分成三个状态：

> - Mounting：已插入真实 DOM
> - Updating：正在被重新渲染
> - Unmounting：已移出真实 DOM

React 为每个状态都提供了两种处理函数，`will` 函数在进入状态之前调用，`did` 函数在进入状态之后调用，三种状态共计五种处理函数。

> - componentWillMount()
> - componentDidMount()
> - componentWillUpdate(object nextProps, object nextState)
> - componentDidUpdate(object prevProps, object prevState)
> - componentWillUnmount()

React 还提供两种特殊状态的处理函数。

> - componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
> - shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用

注意setinterval()方法中，回调函数一定要加.bind(this)方法，原因是：在setInterval()中定义的回调函数，是在同步代码执行完后，随着事件触发来异步执行的，此时函数的上下文Context已经由定义该函数的Script文件变为全局变量，如果不通过bind(this)来指定由组件实例作为上下文的话，回调函数中的this会指向全局变量中的Window变量，显然不是我们想要的结果。

组件的`style`属性的设置方式也值得注意：

```javascript
style={{opacity: this.state.opacity}}
```

这是因为 [React 组件样式](https://facebook.github.io/react/tips/inline-styles.html)是一个对象，所以第一重大括号表示这是 JavaScript 语法，第二重大括号表示样式对象。

### 十一、Ajax

组件的数据来源，通常是通过 Ajax 请求从服务器获取，可以使用 `componentDidMount` 方法设置 Ajax 请求，等到请求成功，再用 `this.setState` 方法重新渲染 UI

甚至可以把一个Promise对象传入组件