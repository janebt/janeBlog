---
title: Vue源码阅读
date: 2017-12-04 10:35:34
updated: 2017-12-04 10:35:34
tags:
  - Vue
categories:
  - 框架
toc: true
---
TODO：整理的很乱，需要细化并补充完整

<!-- more -->

# Vue源码阅读

### Virtual DOM

#### 为什么会有Virtual DOM?

DOM操作都是很慢 >> 因为DOM的属性太多，可能触发重排的操作很多

Virtual DOM：操作js对象，然后最后把这个对象再一起转换成真正的DOM就行了，所以就变成 代码 => Virtual DOM( 一个特殊的js对象） => DOM

#### 什么是Virtual DOM

一个特殊的js对象

```javascript
export class VNode {
  constructor (
    tag?: string,
    data?: VNodeData,
    children?: ?Array<VNode>,
    text?: string,
    ...
  ) {
    this.tag = tag
    this.data = data
    this.children = children
    ...
  }
}
```



#### Virtual DOM算法

- 首先是js对象（Virtual DOM）描述树（`vm._render`)，转换dom插入(第一次渲染）
- 状态变化，生成新的js对象（Virtual DOM），比对新旧对象
- 将变更应用到DOM上，并保存新的js对象（Virtual DOM），重复第二步操作

用js对象描述树(生成Virtual DOM），Vue中就是先转成AST生成code,然后通过$creatElement通过Vnode的那种形式生成Virtual DOM (`vm._render的操作`)

`vm._update`（其实就是Virtual DOM算法的后两步）

```javascript
function lifecycleMixin (Vue) {
  Vue.prototype._update = function (vnode, hydrating) {
    if (vm._isMounted) {...}
    if (!prevVnode) {
      // initial render
      vm.$el = vm.__patch__(...)
    } else {
      // updates
      vm.$el = vm.__patch__(...)
    }
}
```

`vm.__patch__`，其实它就是Virtual DOM Diff的核心

##### Virtual DOM Diff

patch函数

```javascript
function patch (oldVnode, vnode, hydrating, removeOnly, parentElm, refElm) {
    if (isUndef(vnode)) {
      if (isDef(oldVnode)) invokeDestroyHook(oldVnode)
      return
    }

    let isInitialPatch = false
    const insertedVnodeQueue = []

    if (isUndef(oldVnode)) {
      // empty mount (likely as component), create new root element
      // 老节点不存在，直接创建元素
      isInitialPatch = true
      createElm(vnode, insertedVnodeQueue, parentElm, refElm)
    } else {
      const isRealElement = isDef(oldVnode.nodeType)
      if (!isRealElement && sameVnode(oldVnode, vnode)) {
        // patch existing root node
        // 新节点和老节点相同，则给老节点打补丁
        patchVnode(oldVnode, vnode, insertedVnodeQueue, removeOnly)
      } else {
        // ... 省略ssr代码
        // replacing existing element
        // 新节点和老节点相同，直接替换老节点
        const oldElm = oldVnode.elm
        const parentElm = nodeOps.parentNode(oldElm)
        createElm(
          vnode,
          insertedVnodeQueue,
          // extremely rare edge case: do not insert if old element is in a
          // leaving transition. Only happens when combining transition +
          // keep-alive + HOCs. (#4590)
          oldElm._leaveCb ? null : parentElm,
          nodeOps.nextSibling(oldElm)
        )
      }
    }
    // ...省略代码
    return vnode.elm
  }
```

所以patch大概做下面几件事

- 判断老节点存不存在
  - 不存在则为首次渲染，直接创建元素
  - 存在的话则sameVnode使用判断根节点是否相同
    - 相同则使用patchVnode给老节点打补丁
    - 不相同则使用新节点直接替换老节点

对于sameVnode判断，其实就是简单比较了几个属性判断

```javascript
function sameVnode (a, b) {
  return (
    a.key === b.key && (
      (
        a.tag === b.tag &&
        a.isComment === b.isComment &&
        isDef(a.data) === isDef(b.data) &&
        sameInputType(a, b)
      ) || (
        isTrue(a.isAsyncPlaceholder) &&
        a.asyncFactory === b.asyncFactory &&
        isUndef(b.asyncFactory.error)
      )
    )
  )
}
```

patchVnode

```javascript
function patchVnode (oldVnode, vnode, insertedVnodeQueue, removeOnly) {
  if (oldVnode === vnode) {
    // 新老节点相同
    return
  }
  // ... 省略代码
  if (isUndef(vnode.text)) {
    // 假如新节点没有text
    if (isDef(oldCh) && isDef(ch)) {
      // 假如老节点和新节点都有子节点
      // 不相等则更新子节点
      if (oldCh !== ch) updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOnly)
    } else if (isDef(ch)) {
      // 新节点有子节点，老节点没有
      // 老节点加上
      if (isDef(oldVnode.text)) nodeOps.setTextContent(elm, '')
      addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue)
    } else if (isDef(oldCh)) {
      // 老节点有子节点，新节点没有
      // 老节点移除
      removeVnodes(elm, oldCh, 0, oldCh.length - 1)
    } else if (isDef(oldVnode.text)) {
      // 老节点有文本，新节点没有文本
      nodeOps.setTextContent(elm, '')
    }
  } else if (oldVnode.text !== vnode.text) {
    // 假如新节点和老节点text不相等
    nodeOps.setTextContent(elm, vnode.text)
  }
  if (isDef(data)) {
    if (isDef(i = data.hook) && isDef(i = i.postpatch)) i(oldVnode, vnode)
  }
}
```



## 算法实现

### 步骤一：用JS对象模拟DOM树

用 JavaScript 来表示一个 DOM 节点是很简单的事情，你只需要记录它的节点类型、属性，还有子节点

### 步骤二：比较两棵虚拟DOM树的差异

两个树的完全的 diff 算法是一个时间复杂度为 O(n^3) 的问题。但是在前端当中，你很少会跨越层级地移动DOM元素。所以 Virtual DOM 只会对同一个层级的元素进行对比

#### 深度优先遍历，记录差异

在深度优先遍历的时候，每遍历到一个节点就把该节点和新的的树进行对比。如果有差异的话就记录到一个对象里面。

上面的`div`和新的`div`有差异，当前的标记是0，那么：

```
patches[0] = [{difference}, {difference}, ...] // 用数组存储新旧节点的不同
```

#### 差异类型

对 DOM 操作可能会：

1. 替换掉原来的节点，例如把上面的`div`换成了`section`
2. 移动、删除、新增子节点，例如上面`div`的子节点，把`p`和`ul`顺序互换
3. 修改了节点的属性
4. 对于文本节点，文本内容可能会改变。

对于重新排序，替代开销就非常大。而实际上是不需要替换节点，而只需要经过节点移动

#### 列表对比算法

现在知道了新旧的顺序，求最小的插入、删除操作（移动可以看成是删除和插入操作的结合）。这个问题抽象出来其实是字符串的最小编辑距离问题（[Edition Distance](https://en.wikipedia.org/wiki/Edit_distance)），最常见的解决算法是 [Levenshtein Distance](https://en.wikipedia.org/wiki/Levenshtein_distance)，通过动态规划求解，时间复杂度为 O(M * N)。但是我们并不需要真的达到最小的操作，我们只需要优化一些比较常见的移动情况，牺牲一定DOM操作，让算法时间复杂度达到线性的（O(max(M, N))。

### 步骤三：把差异应用到真正的DOM树上

applyPatches，根据不同类型的差异对当前节点进行 DOM 操作：

```javascript
function applyPatches (node, currentPatches) {
  currentPatches.forEach(function (currentPatch) {
    switch (currentPatch.type) {
      case REPLACE:
        node.parentNode.replaceChild(currentPatch.node.render(), node)
        break
      case REORDER:
        reorderChildren(node, currentPatch.moves)
        break
      case PROPS:
        setProps(node, currentPatch.props)
        break
      case TEXT:
        node.textContent = currentPatch.content
        break
      default:
        throw new Error('Unknown patch type ' + currentPatch.type)
    }
  })
}
```

## 结语

Virtual DOM 算法主要是实现上面步骤的三个函数：[element](https://github.com/livoras/simple-virtual-dom/blob/master/lib/element.js)，[diff](https://github.com/livoras/simple-virtual-dom/blob/master/lib/diff.js)，[patch](https://github.com/livoras/simple-virtual-dom/blob/master/lib/patch.js)。然后就可以实际的进行使用：

```javascript
// 1. 构建虚拟DOM
var tree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: blue'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li')])
])

// 2. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)

// 3. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: red'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li'), el('li')])
])

// 4. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)

// 5. 在真正的DOM元素上应用变更
patch(root, patches)
```