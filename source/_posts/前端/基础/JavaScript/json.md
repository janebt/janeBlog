---
title: json
date: 2017-04-30 10:55:34
updated: 2017-04-30 10:55:34
tags:
  - JavaScript
categories:
  - 基础
toc: true
---
本文介绍json。

<!-- more -->
# 知识点

## JSON是什么

JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。它是基于JavaScript的一个子集。数据格式简单, 易于读写, 占用带宽小。是前后台数据交互最常见的一种数据格式。

**特点**

- JavaScript 对象表示法（JavaScript Object Notation）。 
- JSON 是存储和交换文本信息的语法。类似 XML。 
- JSON 比 XML 更小、更快，更易解析。

## JSON语法

1. 数据在键值对中
2. 数据由逗号分隔
3. 花括号保存对象
4. 方括号保存数组

> 注：JSON 值可以是 null ，不能是 undefined

## JSON 文件

- JSON 文件的文件类型是 “.json”
- JSON 文本的 MIME 类型是 “application/json”

## 数据格式转换

### 1. JSON字符串转换为JSON对象:

```javascript
var obj =eval('('+ str +')');
var obj = JSON.parse(str);
```

### 2. JSON对象转换为JSON字符串：

```javascript
var last=JSON.stringify(obj);
```

### 3. 数组转JSON字符串

```javascript
 var array=[1,2,3,4];
 JSON.stringify($(array));
```

### 4. JSON字符串转数组，使用jquery

```javascript
 $(JSON.parse('{"0":1,"1":2,"2":3,"length":3}')); 
```



# 问题

## JSON与XML的差异化

**相似**

- JSON 是纯文本
- JSON 具有“自我描述性”（人类可读）
- JSON 具有层级结构（值中存在值）
- JSON 可通过 JavaScript 进行解析
- JSON 数据可使用 AJAX 进行传输

**不同**

- 没有结束标签
- 更短
- 读写的速度更快
- 能够使用内建的 JavaScript eval() 方法进行解析
- 使用数组
- 不使用保留字

## JSON与XML使用地方

**使用 XML**

- 读取 XML 文档
- 使用 XML DOM 来循环遍历文档
- 读取值并存储在变量中

**使用 JSON**

- 读取 JSON 字符串
- 用 eval() 处理 JSON 字符串




# 参考文章

- [Web前端面试指导(二十六)：JSON 格式是什么？你了解吗？](http://blog.csdn.net/lxcao/article/details/52735632)
- [JSON知识点汇总_W3SCHOOL](http://blog.csdn.net/crper/article/details/45625283)

