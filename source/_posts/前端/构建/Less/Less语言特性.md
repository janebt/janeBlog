---
title: Less语言特性
date: 2017-10-02 09:57:34
updated: 2017-10-02 09:57:34
tags:
  - Less
categories:
  - 构建
toc: true
---
本文介绍Less语言特性

<!-- more -->

# Less语言特性

## 概念总结

### 1.变量

- 在定义变量值时使用其它的变量
- 多次定义，**当前作用域中最后一次定义**的将被使用
- **按需加载，不必强制在使用之前声明**

### 2.混合（Mixins）

- 可以定义一些通用的属性集为一个 class，然后在另一个 class 中去调用这些属性
- 任何 CSS class, id 属性集都可以以同样的方式引入
- 变量也会被混合，也就是说变量会被带到当前的作用域。

#### 带参数混合

- 定义一个带参数的属性集合，如果无默认值，调用必须传参
- 给参数设置默认值，调用可不传参
- 定义不带参数属性集合：隐藏（不在 CSS 中）+ 可引用

#### 多参数混合

- 使用分号或者逗号分隔，推荐使用分号分隔，因为逗号有两重含义：它既可以表示混合的参数，也可以表示一个参数中一组值的分隔符。如果编译器在混合的定义或者是调用中找到至少一个分号，就会假设参数是使用分号分隔的，所有的逗号都属于参数中的一组值的分隔符。
- 使用同样的名字和同样数量的参数定义多个混合是合法的。在被调用时，LESS会应用到所有可以应用的混合上。

##### @arguments 变量

- @arguments包含了所有传递进来的参数。

#### 高级参数用法与 @rest 变量

- 不限制参数的数量，可以在变量名后添加 …，表示这里可以使用 N 个参数。
- @rest 表示之后的参数

#### !important关键字

- 在混合后面加上`!important`关键字表示将混合带来的所有属性标记为`!important`

#### 模式匹配与Guard表达式

- 只有满足匹配要求的混合才会被使用
- 也可以根据参数的数量进行匹配

#### Guards

- Guards 被用来匹配表达式，LESS 选择使用 guard混合（类似于 @media 的工作方式）执行条件判断，而不是加入 if/else 声明。
- 关键词 when，它引入了一个 guard 条件
- Guards 支持的运算符包括：> >= = =< <。true关键字是唯一被判断为真的值
- 多个Guards可以通过逗号表示分隔，如果其中任意一个结果为 true，则匹配成功
- 不同的参数之间也可以比较，而参与比较的也可以一个参数都没有
- 如果需要根据值的类型匹配混合，可以使用 is* 函数
- 使用关键词 and 在 guard 中加入额外的条件
- 使用关键词 not 否定条件

### 3.嵌套规则

- LESS 可以让我们以嵌套的方式编写层叠样式
- 注意 & 符号的使用 — 如果你想写串联选择器，而不是写后代选择器，就可以用到 & 了。这点对伪类尤其有用如:hover 和 :focus。

#### 嵌套 Media Queries

- Media query也可以使用同样的方式进行嵌套

### & 的高级用法

- 用在选择器中的&还可以反转嵌套的顺序并且可以应用到多个类名上
- &也可以用在混合中用于指示嵌套这个混合的父选择器。

### 4.运算

- 任何数字、颜色或者变量都可以参与运算，运算应该被包裹在括号中
- 它能够分辨出颜色和单位

### 5.函数

- 提供了多种函数用于控制颜色变化、处理字符串、算术运算等等

### 6.命名空间

- 将一些变量或者混合模块打包起来
- 使用 `>` 取命名空间

### 7.作用域

- 首先会从本地查找变量或者混合模块，如果没找到的话会去父级作用域中查找，直到找到为止

### 8.注释

- CSS 的注释格式
- 支持双斜线的注释，但是编译成 CSS 的时候自动过滤掉

### 9.导入（Import）

- 既可以导入CSS文件，也可以导入LESS文件。但只有导入的LESS文件才会被处理（编译），导入的CSS文件会保持原样。如果你希望导入一个CSS文件，保留.css后缀即可
- 编译过程中，对导入CSS文件只做一处处理：将导入的语句提到最前，紧跟在@charset之后
- 被导入的LESS文件会被复制到含导入语句的文件中，然后一起编译。导入和被导入的文件共享所有的混合、命名空间、变量以及其它结构。
- 如果导入语句是通过media query指定的，那么导入的语句编译之后会被包裹在@Media声明中。
- LESSS文件的导入语句并不强制要求在顶部，它可以被入在规则内部、混合中或者其它的结构中。



## 变量

很容易理解：

```less
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header { color: @light-blue; }
```

输出：

```less
#header { color: #6c94be; }
```

甚至可以**在定义变量值时使用其它的变量**：

```less
@fnord: "I am fnord.";
@var: 'fnord';
content: @@var;
```

解析后：

```less
content: "I am fnord.";
```

如果对同一个变量定义两次的话，在**当前作用域中最后一次定义**的将被使用。这与CSS的机制类似，最后一次定义的值会成为这个属性的值。 
比如：

```less
@var: 0;
.class1 {
    @var: 1;
    .class {
        @var: 2;
        three: @var;
        @var: 3;
    }
    one: @var;
}
```

会编译成：

```css
.class1.class {
    three: 3;
}
.class1 {
    one: 1;
}
```

变量是“**按需加载**”（lazy loaded）的，因此**不必强制在使用之前声明**。 
下面是一个有效的LESS代码片段：

```less
lazy-eval {
    width: @var;
}

@var: @a;
@a: 9%;
```

下面这个片段也是有效的：

```less
.lazy-eval-scope {
    width: @var;
    @a: 9%;
}

@var: @a;
@a: 100%;
```

这两个片段都会编译成：

```css
.lazy-eval-scope {
    width: 9%;
}
```

------

## 混合（Mixins）

在 LESS 中我们**可以定义一些通用的属性集为一个 class，然后在另一个 class 中去调用这些属性**，下面有这样一个 class：

```less
.bordered {
    border-top: dotted 1px black;
    border-bottom: solid 2px black;
}
```

那如果我们现在需要在其他 class 中引入那些通用的属性集，那么我们只需要在任何 class 中像下面这样调用就可以了：

```less
#menua {
    color: #111;
    .bordered;
}
.posta {
    color: red;
    .bordered;
}
```

.bordered class 里面的属性样式都会在 #menu a 和 .post a 中体现出来：

```less
#menua {
    color: #111;
    border-top: dotted 1px black;
    border-bottom: solid 2px black;
}
.posta {
    color: red;
    border-top: dotted 1px black;
    border-bottom: solid 2px black;
}
```

任何 **CSS class, id 属性集**都可以以同样的方式引入。

> 注意：变量也会被混合，也就是说变量会被带到当前的作用域。这个特性还有争议，也许在未来会有变化。

### **带参数混合**

在 LESS 中，你还可以像函数一样**定义一个带参数的属性集合**：

```less
.border-radius (@radius) {
    border-radius: @radius;
    -moz-border-radius: @radius;
    -webkit-border-radius: @radius;
}
```

然后在其他 class 中像这样调用它：

```less
#header {
    .border-radius(4px);
}
.button {
    .border-radius(6px);
}
```

我们还可以像这样**给参数设置默认值**：

```less
.border-radius (@radius: 5px) {
    border-radius: @radius;
    -moz-border-radius: @radius;
    -webkit-border-radius: @radius;
}
```

所以现在如果我们像这样调用它的话：

```less
#header {
    .border-radius;
}
```

radius 的值就会是 *5px*。 
你也可以**定义不带参数属性集合**，如果你想隐藏这个属性集合，不让它暴露到 CSS 中去，但是你还想在其他的属性集合中引用，你会发现这个方法非常的好用：

```less
.wrap () {
    text-wrap: wrap;
    white-space: pre-wrap;
    white-space: -moz-pre-wrap;
    word-wrap: break-word;
}

pre { .wrap }
```

输出：

```css
pre {
    text-wrap: wrap;
    white-space: pre-wrap;
    white-space: -moz-pre-wrap;
    word-wrap: break-word;
}
```

### 多参数混合

多个参数可以**使用分号或者逗号分隔，推荐使用分号分隔**，因为**逗号有两重含义：它既可以表示混合的参数，也可以表示一个参数中一组值的分隔符。** 
使用分号作为参数分隔符意味着可以将逗号分隔的一组值作为一个变量处理。换句话说，**如果编译器在混合的定义或者是调用中找到至少一个分号，就会假设参数是使用分号分隔的，所有的逗号都属于参数中的一组值的分隔符。** 
2个参数，每个参数都含有通过逗号分隔的一组值的情况：.name(1, 2, 3; something, else)。 
3个参数，每个参数只含一个数字的情况：.name(1, 2, 3)。 
使用一个象征性的分号可以创建一个只含一个参数，但参数包含一组值的混合：.name(1, 2, 3;)。 
逗号分隔的一组值参数的默认值：.name(@param1: red, blue;)。 
**使用同样的名字和同样数量的参数定义多个混合是合法的。在被调用时，LESS会应用到所有可以应用的混合上。**比如你调用混合时只传了一个参数.mixin(green)，那么所有只强制要求一个参数的混合都会被调用：

```less
.mixin(@color) {
    color-1: @color;
}
.mixin(@color; @padding:2) {
    color-2: @color;
    padding-2: @padding;
}
.mixin(@color; @padding; @margin: 2) {
    color-3: @color;
    padding-3: @padding;
    margin: @margin @margin @margin @margin;
}
.some.selectordiv {
    .mixin(#008000);
}
```

编译结果：

```less
.some.selectordiv {
    color-1: #008000;
    color-2: #008000;
    padding-2: 2;
}
```

#### **@arguments 变量**

**@arguments包含了所有传递进来的参数。** 如果你不想单独处理每一个参数的话就可以像这样写：

```less
.box-shadow (@x: 0, @y: 0, @blur: 1px, @color: #000) {
    box-shadow: @arguments;
    -moz-box-shadow: @arguments;
    -webkit-box-shadow: @arguments;
}
.box-shadow(2px, 5px);
```

将会输出：

```css
box-shadow: 2px5px1px#000;
-moz-box-shadow: 2px5px1px#000;
 -webkit-box-shadow: 2px5px1px#000;
```

### 高级参数用法与 @rest 变量

如果需要在 mixin 中**不限制参数的数量，可以在变量名后添加 …，**表示这里可以使用 N 个参数。

```
.mixin (...) {        // 接受 0-N 个参数
     .mixin () {           // 不接受任何参数
     .mixin (@a: 1) {      // 接受 0-1 个参数
     .mixin (@a: 1, ...) { // 接受 0-N 个参数
     .mixin (@a, ...) {    // 接受 1-N 个参数
```

**此外：**

```less
.mixin (@a, @rest...) {
    // @rest 表示 @a 之后的参数// @arguments 表示所有参数
}
```

### **!important关键字**

调用时**在混合后面加上`!important`关键字表示将混合带来的所有属性标记为`!important`：**

```less
.mixin (@a: 0) {
    border: @a;
    boxer: @a;
}
.unimportant {
    .mixin(1); 
}
.important {
    .mixin(2) !important; 
}
```

编译成：

```css
.unimportant {
    border: 1;
    boxer: 1;
}
.important {
    border: 2!important;
    boxer: 2!important;
}
```

### **模式匹配与Guard表达式**

LESS 提供了通过参数值控制 mixin 行为的功能，让我们先从最简单的例子开始：

```less
.mixin (@s, @color) { ... }

.class {
    .mixin(@switch, #888);
}
```

如果要根据 @switch 的值控制 .mixin 行为，只需按照下面的方法定义 .mixin：

```less
.mixin (dark, @color) {
    color: darken(@color, 10%);
}
.mixin (light, @color) {
    color: lighten(@color, 10%);
}
.mixin (@_, @color) {
    display: block;
}
```

然后调用：

```less
@switch: light;

.class {
    .mixin(@switch, #888);
}
```

将会得到以下 CSS：

```css
.class {
    color: #a2a2a2;
    display: block;
}
```

传给 .mixin 的颜色将执行 lighten 函数，如果 @switch 的值是 dark，那么则会执行 darken 函数输出颜色。 
以下是整个过程如何发生的：

```
1. 第一个.mixin 没有匹配，因为不满足 dark 条件；
2. 第二个.mixin 可以被匹配，因为它满足了 light 条件；
3. 第三个.mixin 也可以被匹配，因为它接受任何参数。
```

**只有满足匹配要求的混合才会被使用。**混合中的变量可以匹配任何值，非变量形式的值只有与传入的值完全相等时才可以匹配成功。 
我们**也可以根据参数的数量进行匹**配，比如：

```less
.mixin (@a) {
    color: @a;
}
.mixin (@a, @b) {
    color: fade(@a, @b);
}
```

调用 .mixin 时，如果使用了一个参数，输出第一个 .mixin，使用了两个参数，则输出第二个。

### **Guards**

与上面匹配值或者匹配参数数量的情况不同，**Guards 被用来匹配表达式** (expressions)。如果你很熟悉编程函数的用法，那么很可能你已经掌握它的用法了。 
为了尽可能地符合 CSS 的语言结构，**LESS 选择使用 guard混合（guarded mixins）（类似于 @media 的工作方式）执行条件判断，而不是加入 if/else 声明。** 
首先通过下面的例子开始介绍：

```less
.mixin (@a) when (lightness(@a) >= 50%) {
    background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
    background-color: white;
}
.mixin (@a) {
    color: @a;
}
```

要点在于**关键词 when，它引入了一个 guard 条件** （这里只用到一个 guard）。现在如果运行下面的代码：

```less
.class1 { .mixin(#ddd) }
.class2 { .mixin(#555) }
```

将会得到以下输出结果：

```less
.class1 {
    background-color: black;
    color: #ddd;
}
.class2 {
    background-color: white;
    color: #555;
}
```

**Guards 支持的运算符包括：> >= = =< <**。说明一下，**true关键字是唯一被判断为真的值，也就是这两个混合是相等的**：

```less
.truth (@a) when (@a) { ... }
.truth (@a) when (@a = true) { ... }
```

其他不为 true 的值都判为假：

```css
.class {
    .truth(40); // 不会匹配上面的 mixin
}
```

**多个Guards可以通过逗号表示分隔，如果其中任意一个结果为 true，则匹配成功**：

```less
.mixin (@a) when (@a > 10), (@a < -10) { ... }
```

值得注意的是**不同的参数之间也可以比较，而参与比较的也可以一个参数都没有**：

```less
@media: mobile;

.mixin (@a) when (@media = mobile) { ... }
.mixin (@a) when (@media = desktop) { ... }

.max (@a, @b) when (@a > @b) { width: @a }
.max (@a, @b) when (@a < @b) { width: @b }
```

**如果需要根据值的类型匹配混合，可以使用 is* 函数**：

```less
.mixin (@a, @b: 0) when (isnumber(@b)) { ... }
.mixin (@a, @b: black) when (iscolor(@b)) { ... }
```

**几个检查基本类型的函数**：

- iscolor
- isnumber
- isstring
- iskeyword
- isurl

如果需要检查一个值（数字）使用了哪个单位，可以使用下面三个函数：

- ispixel
- ispercentage
- isem

最后，你**可以使用关键词 and 在 guard 中加入额外的条件**: 
.mixin (@a) when (isnumber(@a)) and (@a > 0) { … } 
或者，**使用关键词 not 否定条件**： 
.mixin (@b) when not (@b > 0) { … }

------

## **嵌套规则**

**LESS 可以让我们以嵌套的方式编写层叠样式。** 让我们先看下下面这段 CSS：

```css
#header { color: black; }

#header.navigation {
font-size: 12px;
}

#header.logo {
width: 300px;
}

#header.logo:hover {
text-decoration: none;
}
```

在 LESS 中, 我们就可以这样写：

```less
#header {
    color: black;

    .navigation {
        font-size: 12px;
    }

    .logo {
        width: 300px;
        &:hover { text-decoration: none }
    }
}
```

或者这样写：

```less
#header { color: black;
    .navigation { font-size: 12px }
    .logo { width: 300px;
        &:hover { text-decoration: none }
    }
}
```

代码更简洁了，而且感觉跟 DOM 结构格式有点像。 
**注意 & 符号的使用 — 如果你想写串联选择器，而不是写后代选择器，就可以用到 & 了。这点对伪类尤其有用如:hover 和 :focus。** 
例如：

```less
.bordered {
    &.float {
        float: left;
    }
    .top {
        margin: 5px;
    }
}
```

输出：

```css
.bordered.float {
    float: left;
}
.bordered.top {
    margin: 5px;
}
```

### **嵌套 Media Queries**

**Media query也可以使用同样的方式进行嵌套**。

```less
.one {
    @media (width: 400px) {
        font-size: 1.2em;
        @media printandcolor {
            color: blue;
        }
    }
}
```

输出：

```less
@media (width: 400px) {
    .one {
        font-size: 1.2em;
    }
}
@media (width: 400px) andprintandcolor {
    .one {
        color: blue;
        }
}
```

## **& 的高级用法**

**用在选择器中的&还可以反转嵌套的顺序并且可以应用到多个类名上**。 
例如：

```less
.child, .sibling {
    .parent & {
        color: black;
    }
    & + & {
        color: red;
    }
}
```

输出：

```css
.parent.child,
.parent.sibling {
    color: black;
}
.child + .child,
.child + .sibling,
.sibling + .child,
.sibling + .sibling {
    color: red;
}
```

**&也可以用在混合中用于指示嵌套这个混合的父选择器。**

------

## **运算**

**任何数字、颜色或者变量都可以参与运算，运算应该被包裹在括号中**。来看一组例子：

```less
@base: 5%;
@filler: (@base * 2);
@other: (@base + @filler);

color: (#888 / 4);
background-color: (@base-color + #111);
height: (100% / 2 + @filler);
```

LESS 的运算已经超出了我们的期望，如果像下面这样的话，**它能够分辨出颜色和单位**：

```less
@var: (1px + 5);
```

LESS 将会使用出现的单位，最终输出 6px。 
也可以使用括号：

```less
width: ((@var + 5) * 2);
```

## **函数**

LESS **提供了多种函数用于控制颜色变化、处理字符串、算术运算等等**。这些函数在函数手册中有详细介绍。 
函数的用法非常简单，下面这个例子将介绍如何将 0.5 转换为 50%，将颜色饱和度增加 5%,以及颜色亮度降低25% 色相值增加 8 等

```less
@base: #f04615;
@width: 0.5;

.class {
    width: percentage(0.5); // returns `50%`color: 			saturate(@base, 5%);
    background-color: spin(lighten(@base, 25%), 8);
}
```

**命名空间** 
有时候，你可能为了更好组织 CSS 或者单纯是为了更好的封装，将一些变量或者混合模块打包起来，你可以像下面这样在 #bundle 中定义一些属性集之后可以重复使用：

```less
#bundle {
    .button () {
        display: block;
        border: 1px solid black;
        background-color: grey;
        &:hover { background-color: white }
    }
    .tab { ... }
    .citation { ... }
}
```

你只需要在 #header a 中像**这样引入 .button**：

```less
#headera {
    color: orange;
    #bundle > .button;
}
```

------

## **作用域**

LESS 中的作用域跟其他编程语言非常类似，**首先会从本地查找变量或者混合模块，如果没找到的话会去父级作用域中查找，直到找到为止**。

```less
@var: red;

#page {
    @var: white;
    #header {
        color: @var; // white
    }
}

#footer {
    color: @var; // red
}
```

------

## **注释**

CSS 的注释格式在 LESS 中依然有效：

```less
/* Hello, I'm a CSS-style comment */.class { color: black }1
```

LESS 同样**也支持双斜线的注释，但是编译成 CSS 的时候自动过滤掉**：

```css
// Hi, I'm a silent comment, I won't show up in your CSS.class { color: white }1
```

## **导入（Import）**

在LESS中，你**既可以导入CSS文件，也可以导入LESS文件。但只有导入的LESS文件才会被处理（编译），导入的CSS文件会保持原样。如果你希望导入一个CSS文件，保留.css后缀即可**：

```less
@import "lib.css";
```

**编译过程中，对导入CSS文件只做一处处理：将导入的语句提到最前，紧跟在@charset之后**。 
例如输入的文件有导入语句：

```less
h1 { color: green; }
@import "import/official-branding.css?urlParameter=23";
```

导入语句将被提到最前：

```less
@import "import/official-branding.css?urlParameter=23";
h1 { color: green; }
```

**被导入的LESS文件会被复制到含导入语句的文件中，然后一起编译。导入和被导入的文件共享所有的混合、命名空间、变量以及其它结构。** 
另外，**如果导入语句是通过media query指定的，那么导入的语句编译之后会被包裹在@Media声明中。** 
例如有被导入的文件library.less：

```less
@imported-color: red;
h1 { color: green; }
```

主样式文件导入了上面的library.less：

```less
@import "library.less" screen and (max-width: 400px); // 通过media query指定的import@import "library.less"; // 无media query的import.class {
    color: @importedColor; // 使用导入的变量
}
```

将会编译出：

```less
// 对应通过media query指定的import@media screen and (max-width: 400px) {
    h1 { color: green; }
}

// 对应无media query的importh1 { color: green; }
.class {
    // 使用导入的变量color: #ff0000;
}
```

**LESSS文件的导入语句并不强制要求在顶部，它可以被入在规则内部、混合中或者其它的结构中。** 
例如放在规则内部：

```less
pre {
    @import "library.less";
    color: @importedColor;
}
```