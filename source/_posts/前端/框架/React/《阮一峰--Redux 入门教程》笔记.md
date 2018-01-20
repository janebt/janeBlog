---
title: 《阮一峰--Redux 入门教程》笔记
date: 2018-01-10 10:35:34
updated: 2018-01-10 10:35:34
tags:
  - Redux
categories:
  - 框架
toc: true
---
Redux 入门教程学习笔记。

这篇文章讲的很好，配合官网文可以快速入门。

<!-- more -->

# 《阮一峰--Redux 入门教程》笔记

## 我的问题

### 中间件

只是为为异步吗？那为什么不直接使用promise呢做thunk呢？使用细节呢

### reducer

reducer的颗粒度才是最好

### state

哪些state应该扔到store里，跨层多次使用就扔吗

------

## Redux 入门教程

> [阮一峰--Redux 入门教程](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)
>
> 关于中间件部分不理解

### 基本用法

有两个方面，它没涉及。

> - 代码结构
> - 组件之间的通信

#### 零、你可能不需要 Redux

Redux 的适用场景：多交互、多数据源。

> - 用户的使用方式复杂
> - 不同身份的用户有不同的使用方式（比如普通用户和管理员）
> - 多个用户之间可以协作
> - 与服务器大量交互，或者使用了WebSocket
> - View要从多个来源获取数据

从组件角度看：

> - 某个组件的状态，需要共享
> - 某个状态需要在任何地方都可以拿到
> - 一个组件需要改变全局状态
> - 一个组件需要改变另一个组件的状态

#### 一、预备知识

React + Flux + [文档](http://redux.js.org/) + 小视频（[前30集](https://egghead.io/courses/getting-started-with-redux)，[后30集](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)）

#### 二、设计思想

> （1）Web 应用是一个状态机，视图与状态是一一对应的。
>
> （2）所有的状态，保存在一个对象里面。

#### 三、基本概念和 API

##### 3.1 Store

整个应用只能有一个 Store。

Redux 提供`createStore`这个函数，用来生成 Store。

```react
import { createStore } from 'redux';
const store = createStore(fn);
```

##### 3.2 State

如果想得到某个时点的数据，就要对 Store 生成快照。这种时点的数据集合，就叫做 State。可以通过`store.getState()`拿到。

```react
import { createStore } from 'redux';
const store = createStore(fn);

const state = store.getState();
```

Redux 规定， 一个 State 对应一个 View。只要 State 相同，View 就相同。

##### 3.3 Action

State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发生变化了。

Action 是一个对象。其中的`type`属性是必须的，表示 Action 的名称。其他属性可以自由设置

```react
const action = {
  type: 'ADD_TODO',
  payload: 'Learn Redux'
};
```

可以这样理解，Action 描述当前发生的事情。改变 State 的唯一办法，就是使用 Action。它会运送数据到 Store。

##### 3.4 Action Creator

View 要发送多少种消息，就会有多少种 Action。可以定义一个函数来生成 Action，这个函数就叫 Action Creator。

```react
const ADD_TODO = '添加 TODO';

function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}

const action = addTodo('Learn Redux');
```

`addTodo`函数就是一个 Action Creator。

##### 3.5 store.dispatch()

`store.dispatch()`是 View 发出 Action 的唯一方法。

```react
store.dispatch({
  type: 'ADD_TODO',
  payload: 'Learn Redux'
});
```

`store.dispatch`接受一个 Action 对象作为参数，将它发送出去。

结合 Action Creator，这段代码可以改写如下。

```react
store.dispatch(addTodo('Learn Redux'));
```

##### 3.6 Reducer

Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。

Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。

```react
const reducer = function (state, action) {
  // ...
  return new_state;
};
```

`store.dispatch`方法会触发 Reducer 的自动执行。为此，Store 需要知道 Reducer 函数，做法就是在生成 Store 的时候，将 Reducer 传入`createStore`方法。

```react
import { createStore } from 'redux';
const store = createStore(reducer);
```

为什么这个函数叫做 Reducer 呢？因为它可以作为数组的`reduce`方法的参数。

##### 3.7 纯函数

Reducer 函数最重要的特征是，它是一个纯函数。也就是说，只要是同样的输入，必定得到同样的输出。

纯函数是函数式编程的概念，必须遵守以下一些约束。

> - 不得改写参数
> - 不能调用系统 I/O 的API
> - 不能调用`Date.now()`或者`Math.random()`等不纯的方法，因为每次会得到不一样的结果

由于 Reducer 是纯函数，就可以保证同样的State，必定得到同样的 View。但也正因为这一点，Reducer 函数里面不能改变 State，必须返回一个全新的对象，请参考下面的写法。

```react
// State 是一个对象
function reducer(state, action) {
  return Object.assign({}, state, { thingToChange });
  // 或者
  return { ...state, ...newState };
}

// State 是一个数组
function reducer(state, action) {
  return [...state, newItem];
}
```

最好把 State 对象设成只读。这样的好处是，任何时候，与某个 View 对应的 State 总是一个不变的对象。

##### 3.8 store.subscribe()

Store 允许使用`store.subscribe`方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。

```react
import { createStore } from 'redux';
const store = createStore(reducer);

store.subscribe(listener);
```

显然，只要把 View 的更新函数（对于 React 项目，就是组件的`render`方法或`setState`方法）放入`listen`，就会实现 View 的自动渲染。

`store.subscribe`方法返回一个函数，调用这个函数就可以解除监听。

```react
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
);

unsubscribe();
```

#### 四、Store 的实现

可以发现 Store 提供了三个方法。

- store.getState()
- store.dispatch()
- store.subscribe()

```react
import { createStore } from 'redux';
let { subscribe, dispatch, getState } = createStore(reducer);
```

`createStore`方法还可以接受第二个参数，表示 State 的最初状态。这通常是服务器给出的。

```react
let store = createStore(todoApp, window.STATE_FROM_SERVER)
```

注意，如果提供了这个参数，它会覆盖 Reducer 函数的默认初始值。

`createStore`方法的一个简单实现

```react
const createStore = (reducer) => {
  let state;
  let listeners = [];

  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    }
  };

  dispatch({});

  return { getState, dispatch, subscribe };
};
```

#### 五、Reducer 的拆分

Reducer 函数负责生成 State。由于整个应用只有一个 State 对象，包含所有数据，导致 Reducer 函数也十分庞大。

```react
const chatReducer = (state = defaultState, action = {}) => {
  return {
    chatLog: chatLog(state.chatLog, action),
    statusMessage: statusMessage(state.statusMessage, action),
    userName: userName(state.userName, action)
  }
};
```

这样一拆，Reducer 就易读易写多了。而且，这种拆分与 React 应用的结构相吻合：一个 React 根组件由很多子组件构成。这就是说，子组件与子 Reducer 完全可以对应。

Redux 提供了一个`combineReducers`方法，用于 Reducer 的拆分合成。

```react
import { combineReducers } from 'redux';

const chatReducer = combineReducers({
  chatLog,
  statusMessage,
  userName
})

export default todoApp;
```

这种写法有一个前提，就是 State 的属性名必须与子 Reducer 同名。如果不同名，就要采用下面的写法。

```react
const reducer = combineReducers({
  a: doSomethingWithA,
  b: processB,
  c: c
})
```

可以把所有子 Reducer 放在一个文件里面，然后统一引入。

```react
import { combineReducers } from 'redux'
import * as reducers from './reducers'

const reducer = combineReducers(reducers)
```

#### 六、工作流程

![](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016091802.jpg)

首先，用户发出 Action。

```react
store.dispatch(action);
```

Store 自动调用 Reducer，并且传入两个参数：当前 State 和收到的 Action。Reducer 会返回新的 State

```react
let nextState = todoApp(previousState, action);
```

State 一旦有变化，Store 就会调用监听函数。

```react
store.subscribe(listener);
```

`listener`可以通过`store.getState()`得到当前状态。如果使用的是 React，这时可以触发重新渲染 View。

```react
function listerner() {
  let newState = store.getState();
  component.setState(newState);   
}
```

#### 七、实例：计数器

### 中间件与异步操作

Action 发出以后，Reducer 立即算出 State，这叫做同步；Action 发出以后，过一段时间再执行 Reducer，这就是异步。

怎么才能 Reducer 在异步操作结束后自动执行呢？这就要用到新的工具：中间件（middleware）。

#### 一、中间件的概念

如果要添加功能，你会在哪个环节添加？

> （1）Reducer：纯函数，只承担计算 State 的功能，不合适承担其他功能，也承担不了，因为理论上，纯函数不能进行读写操作。
>
> （2）View：与 State 一一对应，可以看作 State 的视觉层，也不合适承担其他功能。
>
> （3）Action：存放数据的对象，即消息的载体，只能被别人操作，自己不能进行任何操作。

中间件就是一个函数，对`store.dispatch`方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能。

Case. `store.dispatch`进行了重定义，在发送 Action 前后添加了打印功能。

#### 二、中间件的用法

常用的中间件都有现成的，只要引用别人写好的模块即可。介绍怎么使用中间件。

```react
import { applyMiddleware, createStore } from 'redux';
import createLogger from 'redux-logger';
const logger = createLogger();

const store = createStore(
  reducer,
  applyMiddleware(logger)
);
```

有两点需要注意：

（1）`createStore`方法可以接受整个应用的初始状态作为参数，那样的话，`applyMiddleware`就是第三个参数了。

```react
const store = createStore(
  reducer,
  initial_state,
  applyMiddleware(logger)
);
```

（2）中间件的次序有讲究。

```react
const store = createStore(
  reducer,
  applyMiddleware(thunk, promise, logger)
);
```

比如，`logger`就一定要放在最后，否则输出结果会不正确。

#### 三、applyMiddleware()

它是 Redux 的原生方法，作用是将所有中间件组成一个数组，依次执行。下面是它的源码。

```react
export default function applyMiddleware(...middlewares) {
  return (createStore) => (reducer, preloadedState, enhancer) => {
    var store = createStore(reducer, preloadedState, enhancer);
    var dispatch = store.dispatch;
    var chain = [];

    var middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    };
    chain = middlewares.map(middleware => middleware(middlewareAPI));
    dispatch = compose(...chain)(store.dispatch);

    return {...store, dispatch}
  }
}
```

上面代码中，所有中间件被放进了一个数组`chain`，然后嵌套执行，最后执行`store.dispatch`。可以看到，中间件内部（`middlewareAPI`）可以拿到`getState`和`dispatch`这两个方法。

#### 四、异步操作的基本思路

同步操作只要发出一种 Action 即可，异步操作的差别是它要发出三种 Action。

- 操作发起时的 Action
- 操作成功时的 Action
- 操作失败时的 Action

以向服务器取出数据为例，三种 Action 可以有两种不同的写法。

```
// 写法一：名称相同，参数不同
{ type: 'FETCH_POSTS' }
{ type: 'FETCH_POSTS', status: 'error', error: 'Oops' }
{ type: 'FETCH_POSTS', status: 'success', response: { ... } }

// 写法二：名称不同
{ type: 'FETCH_POSTS_REQUEST' }
{ type: 'FETCH_POSTS_FAILURE', error: 'Oops' }
{ type: 'FETCH_POSTS_SUCCESS', response: { ... } }
```

除了 Action 种类不同，异步操作的 State 也要进行改造，反映不同的操作状态。

```react
let state = {
  // ... 
  isFetching: true,
  didInvalidate: true,
  lastUpdated: 'xxxxxxx'
};
```

#### 五、redux-thunk 中间件

异步操作至少要送出两个 Action：用户触发第一个 Action，这个跟同步操作一样，没有问题；如何才能在操作结束时，系统自动送出第二个 Action 呢？

奥妙就在 Action Creator 之中。Action Creator（动作生成器）的例子：

```react
const fetchPosts = postTitle => (dispatch, getState) => {
  dispatch(requestPosts(postTitle));
  return fetch(`/some/API/${postTitle}.json`)
    .then(response => response.json())
    .then(json => dispatch(receivePosts(postTitle, json)));
  };
};

// 使用方法一
store.dispatch(fetchPosts('reactjs'));
// 使用方法二
store.dispatch(fetchPosts('reactjs')).then(() =>
  console.log(store.getState())
);
```

`fetchPosts`是一个Action Creator（动作生成器），返回一个函数。这个函数执行后，先发出一个Action（`requestPosts(postTitle)`），然后进行异步操作。拿到结果后，先将结果转成 JSON 格式，然后再发出一个 Action（ `receivePosts(postTitle, json)`）。

有几个地方需要注意。

> （1）`fetchPosts`返回了一个函数，而普通的 Action Creator 默认返回一个对象。
>
> （2）返回的函数的参数是`dispatch`和`getState`这两个 Redux 方法，普通的 Action Creator 的参数是 Action 的内容。
>
> （3）在返回的函数之中，先发出一个 Action（`requestPosts(postTitle)`），表示操作开始。
>
> （4）异步操作结束之后，再发出一个 Action（`receivePosts(postTitle, json)`），表示操作结束。

这样的处理，就解决了自动发送第二个 Action 的问题。但是，又带来了一个新的问题，Action 是由`store.dispatch`方法发送的。而`store.dispatch`方法正常情况下，参数只能是对象，不能是函数。这时，就要使用中间件[`redux-thunk`](https://github.com/gaearon/redux-thunk)。

```react
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import reducer from './reducers';

// Note: this API requires redux@>=3.1.0
const store = createStore(
  reducer,
  applyMiddleware(thunk)
);
```

上面代码使用`redux-thunk`中间件，改造`store.dispatch`，使得后者可以接受函数作为参数。

因此，异步操作的第一种解决方案就是，写出一个返回函数的 Action Creator，然后使用`redux-thunk`中间件改造`store.dispatch`。

#### 六、redux-promise 中间件

另一种异步操作的解决方案，就是让 Action Creator 返回一个 Promise 对象。这就需要使用`redux-promise`中间件。

```react
import { createStore, applyMiddleware } from 'redux';
import promiseMiddleware from 'redux-promise';
import reducer from './reducers';

const store = createStore(
  reducer,
  applyMiddleware(promiseMiddleware)
); 
```

这个中间件使得`store.dispatch`方法可以接受 Promise 对象作为参数。这时，Action Creator 有两种写法。写法一，返回值是一个 Promise 对象。

```react
const fetchPosts = 
  (dispatch, postTitle) => new Promise(function (resolve, reject) {
     dispatch(requestPosts(postTitle));
     return fetch(`/some/API/${postTitle}.json`)
       .then(response => {
         type: 'FETCH_POSTS',
         payload: response.json()
       });
});
```

写法二，Action 对象的`payload`属性是一个 Promise 对象。这需要从[`redux-actions`](https://github.com/acdlite/redux-actions)模块引入`createAction`方法，并且写法也要变成下面这样。

```react
import { createAction } from 'redux-actions';

class AsyncApp extends Component {
  componentDidMount() {
    const { dispatch, selectedPost } = this.props
    // 发出同步 Action
    dispatch(requestPosts(selectedPost));
    // 发出异步 Action
    dispatch(createAction(
      'FETCH_POSTS', 
      fetch(`/some/API/${postTitle}.json`)
        .then(response => response.json())
    ));
  }
```

上面代码中，第二个`dispatch`方法发出的是异步 Action，只有等到操作结束，这个 Action 才会实际发出。注意，`createAction`的第二个参数必须是一个 Promise 对象。

`redux-promise`的[源码](https://github.com/acdlite/redux-promise/blob/master/src/index.js)

```react
export default function promiseMiddleware({ dispatch }) {
  return next => action => {
    if (!isFSA(action)) {
      return isPromise(action)
        ? action.then(dispatch)
        : next(action);
    }

    return isPromise(action.payload)
      ? action.payload.then(
          result => dispatch({ ...action, payload: result }),
          error => {
            dispatch({ ...action, payload: error, error: true });
            return Promise.reject(error);
          }
        )
      : next(action);
  };
}
```

从上面代码可以看出，如果 Action 本身是一个 Promise，它 resolve 以后的值应该是一个 Action 对象，会被`dispatch`方法送出（`action.then(dispatch)`），但 reject 以后不会有任何动作；如果 Action 对象的`payload`属性是一个 Promise 对象，那么无论 resolve 和 reject，`dispatch`方法都会发出 Action。

### React-Redux 的用法

> 为了方便使用，Redux 的作者封装了一个 React 专用的库 [React-Redux](https://github.com/reactjs/react-redux)这个库是可以选用的。实际项目中，你应该权衡一下，是直接使用 Redux，还是使用 React-Redux。

#### 一、UI 组件

React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。

UI 组件有以下几个特征。

- 只负责 UI 的呈现，不带有任何业务逻辑
- 没有状态（即不使用`this.state`这个变量）
- 所有数据都由参数（`this.props`）提供
- 不使用任何 Redux 的 API

```react
const Title =
  value => <h1>{value}</h1>;
```

因为不含有状态，UI 组件又称为"纯组件"，即它纯函数一样，纯粹由参数决定它的值。

#### 二、容器组件

容器组件的特征恰恰相反。

- 负责管理数据和业务逻辑，不负责 UI 的呈现
- 带有内部状态
- 使用 Redux 的 API

总之，只要记住一句话就可以了：UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

如果一个组件既有 UI 又有业务逻辑，将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。

#### 三、connect()

React-Redux 提供`connect`方法，用于从 UI 组件生成容器组件。`connect`的意思，就是将这两种组件连起来。

```react
import { connect } from 'react-redux'
const VisibleTodoList = connect()(TodoList);
```

因为没有定义业务逻辑，上面这个容器组件毫无意义。为了定义业务逻辑，需要给出下面两方面的信息。

> （1）输入逻辑：外部的数据（即`state`对象）如何转换为 UI 组件的参数
>
> （2）输出逻辑：用户发出的动作如何变为 Action 对象，从 UI 组件传出去。

`connect`方法的完整 API 如下。

```react
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```

`connect`方法接受两个参数：`mapStateToProps`和`mapDispatchToProps`。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将`state`映射到 UI 组件的参数（`props`），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。

#### 四、mapStateToProps()

它的作用就是像它的名字那样，建立一个从（外部的）`state`对象到（UI 组件的）`props`对象的映射关系。

作为函数，`mapStateToProps`执行后应该返回一个对象，里面的每一个键值对就是一个映射。

```react
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}
```

面代码中，`mapStateToProps`是一个函数，它接受`state`作为参数，返回一个对象。这个对象有一个`todos`属性，代表 UI 组件的同名参数，后面的`getVisibleTodos`也是一个函数，可以从`state`算出 `todos` 的值。

`mapStateToProps`会订阅 Store，每当`state`更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

`mapStateToProps`的第一个参数总是`state`对象，还可以使用第二个参数，代表容器组件的`props`对象。

```react
// 容器组件的代码
//    <FilterLink filter="SHOW_ALL">
//      All
//    </FilterLink>

const mapStateToProps = (state, ownProps) => {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}
```

使用`ownProps`作为参数后，如果容器组件的参数发生变化，也会引发 UI 组件重新渲染。

`connect`方法可以省略`mapStateToProps`参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

#### 五、mapDispatchToProps()

`mapDispatchToProps`是用来建立 UI 组件的参数到`store.dispatch`方法的映射。也就是说，它定义了哪些用户的操作应该当作 Action，传给 Store。它可以是一个函数，也可以是一个对象。

如果`mapDispatchToProps`是一个函数，会得到`dispatch`和`ownProps`（容器组件的`props`对象）两个参数。

```react
const mapDispatchToProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch({
        type: 'SET_VISIBILITY_FILTER',
        filter: ownProps.filter
      });
    }
  };
}
```

从上面代码可以看到，`mapDispatchToProps`作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

如果`mapDispatchToProps`是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。举例来说

```react
const mapDispatchToProps = {
  onClick: (filter) => {
    type: 'SET_VISIBILITY_FILTER',
    filter: filter
  };
}
```

#### 六、`<Provider>` 组件

`connect`方法生成容器组件以后，需要让容器组件拿到`state`对象，才能生成 UI 组件的参数。

一种解决方法是将`state`对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将`state`传下去就很麻烦。

React-Redux 提供`Provider`组件，可以让容器组件拿到`state`。

```react
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp);

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

`Provider`在根组件外面包了一层，这样一来，`App`的所有子组件就默认都可以拿到`state`了。它的原理是`React`组件的[`context`](https://facebook.github.io/react/docs/context.html)属性，请看源码。

```react
class Provider extends Component {
  getChildContext() {
    return {
      store: this.props.store
    };
  }
  render() {
    return this.props.children;
  }
}

Provider.childContextTypes = {
  store: React.PropTypes.object
}
```

上面代码中，`store`放在了上下文对象`context`上面。然后，子组件就可以从`context`拿到`store`，样例代码大致如下。

```react
class VisibleTodoList extends Component {
  componentDidMount() {
    const { store } = this.context;
  }

  render() {
    const props = this.props;
    const { store } = this.context;
    const state = store.getState();
    // ...
  }
}
```

`React-Redux`自动生成的容器组件的代码，就类似上面这样，从而拿到`store`。

#### 七、实例：计数器

一个计数器组件，它是一个纯的 UI 组件。

```react
class Counter extends Component {
  render() {
    const { value, onIncreaseClick } = this.props
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncreaseClick}>Increase</button>
      </div>
    )
  }
}
```

这个 UI 组件有两个参数：`value`和`onIncreaseClick`。前者需要从`state`计算得到，后者需要向外发出 Action。

定义`value`到`state`的映射，以及`onIncreaseClick`到`dispatch`的映射。

```react
function mapStateToProps(state) {
  return {
    value: state.count
  }
}

function mapDispatchToProps(dispatch) {
  return {
    onIncreaseClick: () => dispatch(increaseAction)
  }
}

// Action Creator
const increaseAction = { type: 'increase' }
```

然后，使用`connect`方法生成容器组件。

```react
const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)
```

定义这个组件的 Reducer

```react
// Reducer
function counter(state = { count: 0 }, action) {
  const count = state.count
  switch (action.type) {
    case 'increase':
      return { count: count + 1 }
    default:
      return state
  }
}
```

最后，生成`store`对象，并使用`Provider`在根组件外面包一层

```react

import { loadState, saveState } from './localStorage';

const persistedState = loadState();
const store = createStore(
  todoApp,
  persistedState
);

store.subscribe(throttle(() => {
  saveState({
    todos: store.getState().todos,
  })
}, 1000))

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

概括

```react
// 1.React component: 独立
// 2.Action:3 相对独立
// 3.Reducer:2 相对独立
// 4.Store:3
// 5.Map Redux state to component props:1
// 6.Map Redux actions to component props:1,2
// 7.Connected Component:5+6,1
```

#### 八、React-Router 路由库

使用`React-Router`的项目，与其他项目没有不同之处，也是使用`Provider`在`Router`外面包一层，毕竟`Provider`的唯一功能就是传入`store`对象。

```react
const Root = ({ store }) => (
  <Provider store={store}>
    <Router>
      <Route path="/" component={App} />
    </Router>
  </Provider>
);
```