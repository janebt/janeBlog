---
title: HTML基础
date: 2017-01-26 13:33:34
updated: 2017-12-16 13:33:34
tags:
  - HTML
categories:
  - 基础
toc: true
---
HTML的一些基础概念理解。

<!-- more -->

# 知识点

## 对Web标准的理解

1.html：

- 标签字母要小写
- 标签要闭合
- 标签不允许随意嵌套

2.css和js:

- 尽量使用外链css样式表和js脚本。
- 样式尽量少用行间样式表，使结构与表现分离，标签的id和class等属性命名要做到见文知义，标签越少，加载越快。
- 不需要变动页面内容，便可提供打印版本而不需要复制内容，提高网站易用性。

## 浏览器内核

### 浏览器内核的理解

渲染引擎 + JS引擎

### 常见的浏览器内核有哪些？

- Trident内核：IE，360，搜狗浏览器；
- Gecko内核：Netscape6及以上版本，Mozilla FireFox(火狐浏览器)
- Presto内核：Opera
- Blink内核：Opera；
- Webkit内核：Safari，Chrome

### 优缺点

- **Trident内核**：和W3C标准脱节，大量Bug等安全问题
- **Presto内核**：速度最快，缺点是兼容性。
- **Gecko内核**：功能强大，消耗很多的资源。
- **Webkit内核**：速度较快，兼容性较低

## HTML语义化

### 为什么要语义化

- 有利于seo
- 方便其他设备监听 屏幕阅读设备 盲人阅读器
- 方便团队协作开发

### 语义化元素

- header, footer, hgroup, nav, article, section




# 问题

## 文档模式

### Doctype作用？

告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE不存在或格式不正确会导致文档以兼容模式呈现。

### 标准模式与兼容模式各有什么区别?

标准模式的排版和JS运作模式是以浏览器支持的最高标准运行。在兼容模式中，页面以宽松的向后兼容方式显示。

### HTML5为什么只需要写<!DOCTYPE HTML>？

HTML5 不基于 SGML，因此不需要对DTD进行引用，但是需要doctype来规范浏览器的行为；

而HTML4.01基于SGML，所以需要对DTD进行引用，才能告知浏览器文档所使用的文档类型。

## 加载顺序

### html加载顺序

正常的网页加载流程是这样的。

1. 浏览器一边下载HTML网页，一边开始解析
2. 解析过程中，发现`<script>`标签
3. 暂停解析，网页渲染的控制权转交给JavaScript引擎
4. 如果`<script>`标签引用了外部脚本，就下载该脚本，否则就直接执行
5. 执行完毕，控制权交还渲染引擎，恢复往下解析HTML网页

### js放在网页尾部加载

- js放在head中会立即执行，阻塞后续的资源下载与执行。
- 因为js有可能会修改dom，如果不阻塞后续的资源下载，dom的操作顺序不可控。在DOM结构生成之前就调用DOM，JavaScript会报错

### 加载优化

1.css需要分块，首页的css独立，其余的css需要动态加载，因为html的绘制会被css阻塞，这样可以减少首次进入时的白屏时间。

> js的执行依赖前面的样式。即只有前面的样式全部下载完成后才会执行js，但是此时外链css和外链js是并行下载的。

2.defer="true"属性，将会并行加载js，到页面全部加载完成后才会执行，会按顺序执行。

> 对于内置而不是连接外部脚本的script标签，以及动态生成的script标签，`defer`属性不起作用。

## 代码分析

### DOCTYPE

> `DOCTYPE html`

IE6+兼容，都是标准模式，IE6-是怪异模式

### xmlns

> `<html xmlns="http://www.w3.org/1999/xhtml">`

xmlns是XHTML namespace的缩写。

作用：xml采用名字空间声明，通过网址来识别标记。

XHTML是HTML先xml过度的标记语言，它需要符合xml文档规则，因此，也需要定义名字空间，又因为XHTML1.0不能自定义标识，所以，它的名字空间都相同

### webkit

> `<meta name="renderer" content="webkit">`

对于双核浏览器，如果用户手动调节成兼容模式，那么无法切换为极速模式

### X-UA-Compatible

> `<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">`

IE8的专用标记，指定IE8去模拟某个版本IE浏览器的渲染方式(比如IE6)，以此来解决部分兼容问题

谷歌做了个外挂：Google Chrome Frame(谷歌内嵌浏览器框架GCF)。这个插件可以让用户的IE浏览器外不变，但用户在浏览网页时，实际上使用的是Google Chrome浏览器内核，而且支持IE6、7、8等多个版本的IE浏览器。

这样写可以达到的效果是如果安装了GCF，则使用GCF来渲染页面，如果为安装GCF，则使用最高版本的IE内核进行渲染。

### icon

> `<link rel="shortcut icon" href="..." type="image/x-icon" />`

固定写法，定义网站图标
