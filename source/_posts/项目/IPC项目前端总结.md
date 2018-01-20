---
title: IPC项目前端总结
date: 2017-11-04 10:35:34
updated: 2017-11-04 10:35:34
tags:
  - IPC
categories:
  - 项目
toc: true
---
IPC项目前端一部分经验的整理。

里面东西还是很多的，后续有时间再补上。

<!-- more -->

# IPC项目总结

## 1. 前端导出**excel**功能的实现

### 方案分析

#### 服务器端生成  Vs  浏览器端生成

服务器端生成：

**table**或是**cvs**纯文本的数据格式等。然后按**.xls**或是**.cvs**格式的文件的形式返回给用户，指定**Content-Type:application/vnd.ms-excel ,**浏览器就会提示要下载的文件是**excel**文件。

缺点：

产品主要还是以嵌入式开发为主，**CPU**、内存等资源资源有限，也没有相关逻辑代码，没有第三方的接口可用，实现起来反而会比前端实现更复杂。

#### 浏览器端生成

1. activex方式：使用js/vbs调用excel对象。            （ie+excel）
  e.g. var oXL = new ActiveXObject("Excel.Application"); 
2. ie命令方式：将html或是csv输出到open的window，然后使用execCommand的saveas命令，存为csv或xls。             (ie only)                              
  e.g. xlsWin.document.execCommand('Saveas', true, inName);
3. 服务器端中转方式：将html的table或是拼接的csv传到服务器端，服务器端再按照Content-Type:application/vnd.ms-excel返回，浏览器就会按excel方式处理。     (all)
  e.g. Response.contentType= “application/vnd.ms-excel ”
4. data协议方式：对于支持data协议的浏览器，可以将html或是csv先用js base64处理，然后前缀data:application/vnd.ms-excel;base64,，即可使浏览器将其中的数据当做excel来处理，浏览器将提示下载或打开excel文件,可惜的是ie不支持 。extjs的官网有一个grid的plugin就是采用该做法。(except IE)
  e.g. var uri = 'data:application/vnd.ms-excel;base64

#### 浏览器端生成的缺陷

1. activex方式：使用js/vbs调用excel对象。            （ie+excel）

   缺点：安全性差，数据流会被截取；需要应用配合，局限性大。
2. ie命令方式：将html或是csv输出到open的window，然后使用execCommand的saveas命令，存为csv或xls。                                               (ie only)

     缺点：只支持ie
3. 服务器端中转方式：将html的table或是拼接的csv传到服务器端，服务器端再按照Content-Type:application/vnd.ms-excel返回，浏览器就会按excel方式处理。 (all)

    缺点：我司框架结构比较特殊，无法简单运用，该方法一般用于独立的js和asp页面，通用性较差。需要增加通信流程，实现复杂化。

    4. data协议方式：对于支持data协议的浏览器，可以将html或是csv先用js base64处理，然后前缀data:application/vnd.ms-excel;base64,，即可使浏览器将其中的数据当做excel来处理，浏览器将提示下载或打开excel文件 。(except IE)
      缺点：ie不支持；模板中的excel格式无法进行复杂化自定义。

### 设计思路

IE浏览器，采用方案2

非IE浏览器，采用方案4

### 实现细节

#### 方案2

> 分为两步：getTblData() + doFileExport()

##### getTblData()

- 检查输入框、单选框
- 检查colspan
- 间隔为\t，换行\r\n

##### doFileExport()

- 建一个隐藏的iframe
- 往iframe中写字符串，执行Saveas命令

#### 方案4

URL拼接下载

- 有一个固定的url模版，用于格式化表格
- 建一个链接，并指定href为固定的uri+格式化后的字符串
- 模拟点击链接




## 2. Canvas兼容

> 目标：兼容IE6、IE7、IE8，需要html5.js + excanvas.js

### html5.js

1. IE8无法识别html5标签，这些标签在IE8下无效果；
2. document.createElement();
3. 设置css属性等；

如`<article>`

### excanvas.js(Google发起)

工作原理：

- 使用VML对象来实现canvas的方法；
- 加载库文件时，遍历网页中所有的canvas同时将canvas的方法赋给遍历得到的所有结果；

### 核心代码

```javascript
var canvasId = “canvasArea”;
window.G_vmlCanvasManager.initElement(canvasId);

var els = document.getElementsByTagName('canvas');
for (var i = 0; i < els.length; i++)
{
    if (this.canvasId == els[i].id)
    {
    	this.can = window.G_vmlCanvasManager.initElement(els[i]);
    }
}
```

原方法：

```javascript
var G_vmlCanvasManager_ = {
  init: function(opt_doc) {
    var doc = opt_doc || document;
    // Create a dummy element so that IE will allow canvas elements to be
    // recognized.
    doc.createElement('canvas');
    doc.attachEvent('onreadystatechange', bind(this.init_, this, doc));
  },

  init_: function(doc) {
    // find all canvas elements
  },
  initElement: function(el) {
    if (!el.getContext) {
      el.getContext = getContext;

      // Add namespaces and stylesheet to document of the element.
      addNamespacesAndStylesheet(el.ownerDocument);
    }
  };
```



## 3. 盒模型介绍

### IE6怪异盒模型

> 在IE6下，会以盒模型的宽度 = margin + width或者padding+border（两者取其大者）

解决方法：

1. 将页面设为“标准模式”。添加对应的dtd标识，如：<!DOCTYPE html**>**--推荐 


2. 使用hack或者在外面套上一层wrapper。 

### !important研究

并不是IE6不支持!important，而是它会按照样式声明顺序后出现的覆盖前面的，此时它已经不认识!important了，它六亲不认了。这正是广为流传的IE6 hack之一。

### Block Formatting Context 

创建了BFC的元素规定了内部的块级盒如何布局，并且使该盒子在页面上形成一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然。

#### 创建块格式化上下文

- 根元素或其它包含它的元素
- 浮动 (元素的 float 不为 none)
- 绝对定位元素 (元素的 position 为 absolute 或 fixed)
- 行内块 inline-blocks (元素的 display: inline-block)
- 表格单元格 (元素的 display: table-cell，HTML表格单元格默认属性)
- 表格标题 (元素的 display: table-caption, HTML表格标题默认属性)
- overflow 的值不为 visible的元素
- 弹性盒子 flex boxes (元素的 display: flex 或 inline-flex)

#### 块格式化上下文表现

- 内部的Box会在垂直方向，从顶部开始一个接一个地放置。
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生叠加
- 每个元素的margin 子元素盒子的左边， 与包含块border 父元素盒的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
- BFC的区域不会与float box叠加。
- 计算BFC的高度时，浮动元素也参与计算。

> **margin塌陷的解释** 
>
> 所谓的塌陷其实是两个BFC的相邻盒或者父子盒相互作用时产生的。在形成BFC的两个盒子会取两个盒子相邻边的最大margin作为相邻边的共用maring。



## 4. title2.0页面加载流程问题

### 问题

```css
/* Basic.htm中: */
body{
	background-color:#1c1c1c;
}

/* 和BasicPreview.htm中*/

body{
	background-color:#000000;
}
```

权值是相同的，理论上后加载的BasicPreview.htm中的css的优先级高于Basic.htm中css样式。但现在却出现了Basic.htm优先级高的问题。

### 分析

经排查：2016年12月20日，Jenkins服务器修复了不能调用Java的问题，导致压缩后的htm文件js、css和html各部分顺序发生变化。原来没有压缩或者tftp传输没有问题。预览页面背景色涉及Basic.htm和BasicPreview.htm。原来这两个htm中的顺序是js->css->html,压缩后抓包看到顺序变为html->js->css。

压缩后的Basic.htm先加载，html在页面中生成div容器等dom，css在html后面自然也在div的后面；BasicPreview.htm中的html和css载到Basic.htm的div容器中。所以此时BasicPreview.htm中的css出现在Basic.htm的css前，导致了Basic.htm中css样式优先级高于BasicPreview.htm中的css。

解决方法：在时BasicPreview.htm中的相关css使用“!important”规则，来提高优先级。

### HTML页面加载和解析流程

1. 用户输入网址（假设是个html页面，并且是第一次访问），浏览器向服务器发出请求，服务器返回html文件。
2. 浏览器开始载入html代码，发现<head>标签内有一个<link>标签引用外部CSS文件。
3. 浏览器又发出CSS文件的请求，服务器返回这个CSS文件。
4. 浏览器继续载入html中<body>部分的代码，并且CSS文件已经拿到手了，可以开始渲染页面了。
5. 浏览器在代码中发现一个<img>标签引用了一张图片，向服务器发出请求。此时浏览器不会等到图片下载完，而是继续渲染后面的代码。
6. 服务器返回图片文件，由于图片占用了一定面积，影响了后面段落的排布，因此浏览器需要回过头来重新渲染这部分代码。
7. 浏览器发现了一个包含一行Javascript代码的`<script>`标签，赶快运行它。
8. Javascript脚本执行了这条语句，它命令浏览器隐藏掉代码中的某个`<style>`（style.display=”none”）。杯具啊，突然就少了这么一个元
  素，浏览器不得不重新渲染这部分代码。
9. 终于等到了`</html>`的到来，浏览器泪流满面…… 
10. 等等，还没完，用户点了一下界面中的“换肤”按钮，Javascript让浏览器换了一下＜link＞标签的CSS路径。
11. 浏览器召集了在座的各位<div><span><ul><li>们，“大伙儿收拾收拾行李，咱得重新来过……”，浏览器向服务器请求了新的CSS文件，重新渲染页面。

### 如何加快HTML页面加载速度

1. 页面减肥：
  a. 页面的肥瘦是影响加载速度最重要的因素。
  b. 删除不必要的空格、注释。
  c. 将inline的script和css移到外部文件。
  d. 可以使用HTML Tidy来给HTML减肥，还可以使用一些压缩工具来给JavaScript减肥。
2. 减少文件数量：
  a. 减少页面上引用的文件数量可以减少HTTP连接数。
  b. 许多JavaScript、CSS文件可以合并最好合并，把自己的JavaScript. functions和Prototype.js合并到一个base.js文件里去了。
3. 减少域名查询：
  a. DNS查询和解析域名也是消耗时间的，所以要减少对外部JavaScript、CSS、图片等资源的引用，不同域名的使用越少越好。
4. 缓存重用数据：
  a. 对重复使用的数据进行缓存。


5. 优化页面元素加载顺序：
  a. 首先加载页面最初显示的内容和与之相关的JavaScript和CSS，然后加载HTML相关的东西，像什么不是最初显示相关的图片、flash、视频等很肥的资源就最后加载。
6. 减少inline JavaScript的数量：
  a. 浏览器parser会假设inline JavaScript会改变页面结构，所以使用inline JavaScript开销较大。
  b. 不要使用document.write()这种输出内容的方法，使用现代W3C DOM方法来为现代浏览器处理页面内容。
7. 使用现代CSS和合法的标签：
  a. 使用现代CSS来减少标签和图像，例如使用现代CSS+文字完全可以替代一些只有文字的图片。
  b. 使用合法的标签避免浏览器解析HTML时做“error correction”等操作，还可以被HTML Tidy来给HTML减肥。
8. Chunk your content：
  a. 不要使用嵌套table，而使用非嵌套table或者div。将基于大块嵌套的table的layout分解成多个小table，这样就不需要等到整个页面（或
  大table）内容全部加载完才显示。
9. 指定图像和table的大小：
  a. 如果浏览器可以立即决定图像或table的大小，那么它就可以马上显示页面而不要重新做一些布局安排的工作。
  b. 这不仅加快了页面的显示，也预防了页面完成加载后布局的一些不当的改变。
  c. image使用height和width。




## 5. 自动化测试

### Selenium

- Selenium测试直接运行在浏览器中，就像真正的用户在操作一样。它支持**Firefox**、**ie**、**Mozilla**等众多浏览器。它同时支持**JAVA**、**C#**、**Ruby**、**Python**、**PHP**、**Perl**等众多的主流语言开发测试用例。

### webdriver

- 属于selenium的工具包
- WebDriver通过原生浏览器支持或者浏览器扩展直接控制浏览器。WebDriver针对各个浏览器而开发，取代了嵌入到被测Web应用中的JavaScript。

### 样例

```python
#!/usr/bin/env python 
# -*- coding: UTF-8 -* 
import time 
from selenium import webdriver 
from selenium.webdriver.common.keys import Keys 
print '----- STEP1: Open the browser -----' 
browser = webdriver.Chrome() 
print '----- STEP2: Get the url:http://tp-link.net/ -----' 
browser.get('http://tp-link.net/') 
assert u'内网导航' in browser.title 
print '----- STEP3: Find the element -----' 
elem = browser.find_element_by_name('searchword')  # Find the search box 
print '----- STEP4: Send keys to the element -----' 
elem.send_keys('seleniumhq' + Keys.RETURN) 
time.sleep(30) 
print '----- STEP5: Close the browser -----' 
browser.quit()
```

程序说明: 打开chrome浏览器，跳转内网http://tp-link.net/,  输入’seleniumhq’，按下ENTER

### 定位元素

从html中提取控件的属性，然后通过webdriver的接口去定位这些控件，从而对控件进行各种操作。

WebDriver提供八种定位方法：

- find _element_by_id()
- ...

### 关键对象

- driver
- element




## 6. **Cookie & Session & Cache **机制介绍

### Cookie

#### Cookie格式

```http
Set－Cookie: NAME=VALUE；Expires=DATE；Path=PATH；Domain=DOMAIN_NAME；SECURE
```

- 在字符串“NAME=VALUE”中，不含分号、逗号和空格等字符。
- DATE必须以特定的格式来书写：星期几，DD－MM－YY HH:MM:SS GMT
- 如果Path属性的值为“/”，则Web服务器上所有的WWW资源均可读取该Cookie。
- Secure：在Cookie中标记该变量，表明只有当浏览器和Web Server之间的通信协议为加密认证协议时，浏览器才向服务器提交相应的Cookie。

#### Cookie介绍

- Cookie是保存在客户端的一组数据，主要用来保存用户名等个人信息。
- 我们可以存放非敏感的用户信息，保存时间可以根据需要设置
- 如果没有设置Cookie失效日期，它的生命周期保存到关闭浏览器为止
- Cookie对象的Expires属性设置为MinValue表示永不过期
- Cookie存储的数据量受限制，大多数的浏览器为4K因此不要存放大数据，很多浏览器都限制一个站点最多保存20个cookie。

#### Cookie的用法

Cookie是用于维持服务端会话状态的。

客户端写Cookie：

```javascript
document.cookie = "Authorization="+escape(auth)+";path=/";
document.cookie = “SubType=pcSub;path=/";
```

客户端读Cookie：

```javascript
var startIndex = document.cookie.indexOf(“SubType=");
var  cookieSubType = document.cookie.substring(startIndex);
var startIndex = cookieLoginTime.indexOf("=");
cookieSubType = cookieLoginTime.substring(startIndex +1);
var endIndex = cookieLoginTime.indexOf(";");			
cookieSubType = cookieSubType.substring(startIndex, endIndex);
```

### Session

#### Session介绍

Session是由应用服务器维持的一个服务器端的存储空间，用SessionID作为标识符来存取服务器端的Session存储空间。

#### Session作用

Session 对象用于存储用户的信息，存储于 session 对象中的变量持有单一用户的信息，并且对于一个应用程序中的所有页面都是可用的。

#### Session用法

##### 借助Cookie

SessionID这一数据则是保存到客户端，用Cookie保存的，用户提交页面时，会将这一SessionID提交到服务器端，来存取Session数据。

##### url重写

  通过向 URL 连接添加参数，把 session ID 作为值包含在连接中。

```javascript
location.href = "linkStatus.htm?clrStats=1&session_id=" + session_id;

w('<input name="session_id" id="session_id" type="hidden" value="'+ session_id +'">');
```
### 为什么会有cookie和session

- http是无状态的协议，客户每次读取web页面时，服务器都打开新的会话，而且服务器也不会自动维护客户的上下文信息。
- session就是一种保存上下文信息的机制，它是针对每一个用户的，变量的值保存在服务器端，通过SessionID来区分不同的客户,session是以cookie或URL重写为基础的，默认使用cookie来实现，系统会创造一个名为JSESSIONID的输出cookie，我们叫做session cookie,以区别persistent cookies,也就是我们通常所说的cookie。
- 注意session cookie是存储于浏览器内存中的，并不是写到硬盘上的，这也就是我们刚才看到的JSESSIONID，我们通常情是看不到JSESSIONID的，但是当我们把浏览器的cookie禁止后，web服务器会采用URL重写的方式传递Sessionid，我们就可以在地址栏看到sessionid=KWJHUG6JJM65HS2K6之类的字符串。 

### 为什么Session比cookie更安全？

session针对某一次会话而言，会话结束session也就随着消失了，而(persistent) cookie只是存在于客户端硬盘上的一段文本（通常是加密的），而且可能会遭到cookie欺骗以及针对cookie的跨站脚本攻击，自然不如session安全了。 

### 跨窗口使用

通常session cookie是不能跨窗口使用的，当你新开了一个浏览器窗口进入相同页面时，系统会赋予你一个新的sessionid，这样我们信息共享的目的就达不到了，此时我们可以先把sessionid保存在persistent cookie中，然后在新窗口中读出来，就可以得到上一个窗口SessionID了，这样通过session cookie和persistent cookie的结合我们就实现了跨窗口的session tracking（会话跟踪）。 

### Session vs Cookie总结

- cookie存放在客户的浏览器上，session放在服务器上
- cookie不是很安全，考虑到安全应当使用session。
- session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用cookie 。

建议：将登陆信息等重要信息存放为SESSION，其他信息如果需要保留，可以放在COOKIE中

### Cache

#### Cache简介

##### 浏览器缓存（IE为例）

为了提高访问网页的速率，IE浏览器会采用累积式加速的方法，将曾经访问的网页内容存放在电脑里。这个存放空间，就称它为IE缓存。以后每次访问网站时，IE会首先搜索这个目录，其中已经有访问过的内容，那IE就不必从网上下载，而直接从缓存中调出来，从而提高了访问网站的速率

##### 代理缓存

代理缓存是共享缓存的一种，不是只有一个人正在使用它们，而是同时又大量的用户，因为他们可以非常好的节约了带宽和网页延迟。

#### 网站缓存的工作原理

- header响应头部分可以设置了是否进行缓存.

- 如果请求是经过HTTP 认证或是SSL安全链接 (i.e., HTTPS),缓存也无法工作.

- 如果符合以下条件，缓存机制的启用是通过页面的刷新引起(不需要经过检测服务器就可以直接发送到客户端)：

- - 时间没有超过已设置的缓存页面过期时间或者其它类似 age-controlling（指示客户机可以接收生存期不大于指定时间的响应） 的设置。
  - 缓存是最近请求时保存的，或是缓存修改的时间也是新的。

- 当资源过期时（使用Cache-Control标识的max-age），发现资源具有Last-Modified（Etag）声明，则再次向web服务器请求时带上头 **If-Modified-Since**（**If-None-Match**），表示客户端请求时间。**web**服务器收到请求后发现有头**If-Modified-Since**（**If-None-Match**）则与被请求资源的最后修改时间进行比对。若最后修改时间较新，说明资源又被改动过，则响应整片资源内容（写在响应消息包体内），HTTP 200；若最后修改时间较旧，说明资源无新修改，则响应HTTP 304 (无需包体，节省浏览)，告知浏览器继续使用所保存的cache。

- 特定情况— 比如, 与服务器断开连接 — 缓存会直接作为请求的响应.




## 7. settimeout介绍

### this

setInterval()，setTimeout() 方法是浏览器 window 对象提供，所以第一个参数函数中的this指向window对象，这跟变量的作用域有关。 

Function.prototype.bind() 方法，该方法允许显式地指定函数调用时 this 所指向的值 。

### js事件线程机制

浏览器的内核是多线程的，它们在内核制控下相互配合以保持同步，一个浏览器至少实现三个常驻线程：javascript引擎线程，GUI渲染线程，浏览器事件触发线程。

-  javascript引擎是基于事件驱动单线程执行的，JS引擎一直等待着任务队列中任务的到来，然后加以处理，浏览器无论什么时候都只有一个JS线程在运行JS程序。js的工作机制是：当线程中没有执行任何同步代码的前提下才会执行异步代码
-  GUI渲染线程负责渲染浏览器界面，当界面需要重绘（Repaint）或由于某种操作引发回流(reflow)时,该线程就会执行。但需要注意 GUI渲染线程与JS引擎是互斥的，当JS引擎执行时GUI线程会被挂起，GUI更新会被保存在一个队列中等到JS引擎空闲时立即被执行。
-  事件触发线程，当一个事件被触发时该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理。这些事件可来自JavaScript引擎当前执行的代码块如setTimeout、也可来自浏览器内核的其他线程如鼠标点击、AJAX异步请求等，但由于JS的单线程关系所有这些事件都得排队等待JS引擎处理。（当线程中没有执行任何同步代码的前提下才会执行异步代码）




## 8. jquery选择器

### 选择器分类

《jquery选择器.ppt》P7

### 应用

```javascript
//如果硬盘格式化中，无法进行格式化操作
$("td:contains('+label.formatting+')").next().children().css("display:none")

//点击格式化按钮，呈现格式化进度条动画
$("#diskTable tbody tr:eq('+(index+1)+') td:last-child") 
```



## 9.页面布局

### 布局模型

1. 流动模型（Flow）
2. 浮动模型 (Float)
3. 层模型（Layer）

### 布局模型：Flow

流动是默认的网页布局模式，也是HTML页面默认的布局模型。

特征：

1. 块状元素都会在父元素内自上而下按照顺序垂直分布，宽度为100%，每个块状元素均占据一行。


2. 内联元素都会在父元素中从左到右水平分布显示，宽度由内容撑开。
3. 内联块状元素在父元素中同一行分布，可设置宽度、高度、padding、margin。

常用的块状元素有：

```html
<div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<blockquote> 、<form>
```

常用的内联元素有：

```html
<a>、<span>、<br>、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>
```

常用的内联块状元素有：

```html
<img>、<input>
```

### 布局模型：Float

使用浮动的元素必然是inline-block;

### 布局模型：Layer

层模型有三种形式：

1. **绝对定位**(position: absolute)
2. **相对定位**(position: relative)
3. **固定定位**(position: fixed)

#### 布局模型：绝对定位

**使用绝对定位的元素必然是inline-block;**

使用position:absolute可以为元素设置层模型中的绝对定位，该样式的作用：

1. 将元素从文档流中拖出来；
2. 使用left、right、top、bottom属性**相对于其最接近的一个具有定位属性的父包含块进行绝对定位。**如果不存在这样的包含块，则相对于body元素，即相对于浏览器窗口。

#### 布局模型：相对定位

它通过left、right、top、bottom属性确定元素在**正常文档流中**的偏移位置。

相对定位完成的过程是首先按static(float)方式生成一个元素(并且元素像层一样浮动了起来)，然后相对于**以前的位置移动**，移动的方向和幅度由left、right、top、bottom属性确定，偏移前的位置保留不动。

#### 布局模型：固定定位

它的相对移动的坐标是视图（**屏幕内的网页窗口**）本身。由于视图本身是固定的，它不会随浏览器窗口的滚动条滚动而变化，因此固定定位的元素会始终位于浏览器窗口内视图的某个位置，不会受文档流动影响

### 居中效果的实现

1. 水平居中
   - 行内或类行内元素：text-align:center
   - 块级元素：设置 margin-left 和 margin-right 为 auto（前提是已经为元素设置了适当的 `width` 宽度，否则块级元素的宽度会被拉伸为父级容器的宽度）
   - float + left

2. 垂直居中
   - 行内或类行内元素 + 父元素高度确定
     - 单行行内或者文本元素：
       - 等值的 padding-top 和 padding-bottom
       - `line-height` 和 `center` 相等
     - 多行文本
       - 等值 `padding-top` 和 `padding-bottom`
       - `table-cell` 的父级容器 +  `vertical-align`
       - 父级容器为 `display: flex` 
       - 幽灵元素：在垂直居中的元素上添加伪元素，设置伪元素的高等于父级容器的高，然后为文本添加 vertical-align: middle;
   - 块级元素 + 已知元素的高度：top+height+margin-top
   - 块级元素 + 未知元素的高度：top+`translate` 属性
   - flexbox
3. 水平且垂直居中

- 宽高固定元素：设定父级容器为相对定位的容器，设定子元素绝对定位的位置 `position: absolute; top: 50%; left: 50%`，最后使用负向 `margin` 实现水平和垂直居中，`margin` 的值为宽高（具体的宽高需要根据实际情况计算 `padding`）的一半。
- 宽高不固定元素：设定父级容器为相对定位的容器，设定子元素绝对定位的位置 position: absolute; top: 50%; left: 50%。不同的是，接下来需要使用 transform: translate(-50%, -50%); 实现垂直居中
- flexbox：justify-content+align-items




## 10. Vue.js

### Vue Vs React

相同点：

- React采用特殊的JSX语法，Vue.js在组件开发中也推崇编写.vue特殊文件格式，对文件内容都有一些约定，两者都需要编译后使用。
- 中心思想相同：一切都是组件，组件实例之间可以嵌套。
- 都提供合理的钩子函数，可以让开发者定制化地去处理需求。
- 都不内置列数AJAX，Route等功能到核心包，而是以插件的方式加载。
- 在组件开发中都支持mixins的特性。

不同点：

- React依赖Virtual DOM,而Vue.js使用的是DOM模板。React采用的Virtual DOM会对渲染出来的结果做脏检查。
- Vue.js在模板中提供了指令，过滤器等，可以非常方便，快捷地操作DOM。

### 组件通信

- v-on  事件监听
- v-bind 元素属性绑定

在 Vue.js 中，父子组件的关系可以总结为 **props down, events up** 。

- 使用 $on(eventName) 监听事件
- 使用 $emit(eventName) 触发事件




## 11. 正则表达式介绍

### 实战

合法路径检测：

```javascript
/^[^\\/:*?"'<>|%]+$/gi.test(val)
```



## 12. IPC浏览器兼容性问题

### 1. firefox兼容性问题

#### 1.1 firefox中float造成的异常

清除浮动

#### 1.2 firefox下event缺失造成的异常

函数接口缺失event所致，window.event只能在IE下使用，而不能用在Firefox下，导致代码在stopProp处中止执行下面为body绑定点击事件的代码。

#### 1.3 firefox下iframe造成的异常

firefox下iframe造成的异常，本质上是浏览器的bug。目前没有什么好的方法解决，只能避免采用iframe同时再使用某些属性。

##### 1.3.1 设置透明度被视频遮挡

```css
opacity:0.95;
filter:alpha(opacity=95);
```

##### 1.3.2 设置圆角被视频截成矩形

```css
border-radius:12px;;
```

#### 1.4 冒泡事件

```javascript
/*绑定事件函数*/
this.attachEvnt = function(target, event, handle)
{
	if (event.indexOf("on") == 0)
	{
		event = event.substring(2);
	}

	if (document.body.attachEvent)
	{
		target.attachEvent("on"+event, handle);
	}
	else
	{
		target.addEventListener(event, handle, false);
	}
};
```

#### 1.5 阻止事件传播

```javascript
/*stop propagation of event */
this.stopProp = function (event)
{
	event = event || window.event;
	if (undefined == event)
	{
		return;
	}

	if (event.stopPropagation)
	{
		event.stopPropagation();
	}
	else
	{
		event.cancelBubble = true;
	}
};

```

### 2. IE8兼容性问题

#### 2.1 div 设为z-index经常无法覆盖下面元素

当div使用transparent时，或不使用时（默认透明）IE8下z-index经常会出现无效问题。无法起到覆盖作用。

解决方法：将div的背景颜色设为背景色。

#### 2.2 变量未声明

变量未声明在IE11下为全局变量。但在IE8下会在此中断往下执行javascript，导致页面异常。

#### 2.3 使用absolute

使用absolute进行绝对定位时，元素的位置未通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

#### 2.4 日期显示NaN问题

IE8下使用new Date()。设初始值时，日期会显示NaN

可使用parseISO8601()解决

#### 2.5 NAN和数字相加减导致的问题

解决方案：针对不同的变量，建议采用不同的方法来解决。

- 当被设置的值不是必需时如line-height，加减运算前可先进行判断，不为NAN再进行运算。
- 若控件中已经定义默认值，判断为NAN时可采用默认值来替代。

#### 2.6 width不包括边框导致的缩放问题

IE8/Firefox下缩放，拖动条最后一个元素输入框会异位，呈现在下一行。

原因：IE8/Firefox下缩放按照width缩放，测试发现width不包括边框，这样会带来：如果有border，那么放大后，实际的宽度会比正常放大的比例小。而缩小相反。此时父元素的width按同等比例缩小，会小于实际宽度。

解决方案：父元素的宽度不需要设置。

#### 2.7 innerHTML写操作权限问题

 在IE8上修改table的innerHTML时，会出现“未知的运行时错误”。

原因：IE8在对innerHTML进行写操作的时候会检查element是否满足作为这些内容的容器的要求，在IE8上<tbody>,<table>,<tr>标签不满足这种要求。

解决方案：避免直接对table的innerHTML进行操作，改为对其父容器进行操作

### 3. IE6兼容性问题

#### 3.1 插件object占空间导致的布局异常

IE6预览界面object对象没有设置宽和高，但会有默认的宽度和高度。

#### 3.2 float折行/错位问题

解决方法：

1. 尽量不使用浮动定位；
2. 使用position定位，记得父元素使用相对定位，子元素使用绝对定位；
3. 浮动的div尝试使用display:inline和overflow:hidden属性；
4. 将使用右浮动的元素放到没有浮动元素的左边；将使用左浮动的元素放到没有使用浮动元素的右边。

#### 3.3 经典问题:双边距问题

给样式增加一条css样式：_display:inline;

#### 3.4 缩放页面，页面换行重合问题

原因：在body上设置最小宽度在IE6下没有生效,测试发现expression表达式在IE6下可以正常执行（会不断重复计算），但是body属性只执行了一次，后续resize不会再执行expression表达式，转而设置body宽度为页面宽度，所以不能很好缩放。

解决：绕过该问题，改为在body的子元素Con上设置expression，可以解决问题，它会不断计算当前的最小宽度。

#### 3.5 text-indent位置偏移问题

在IE6/IE7中，text-indent会导致inline- block元素出现向左(text-indent的值为负时)或向右(text-indent值为正时)的偏移。

解决方法：在基本布局不变情况下，做IE6/7 hack：在CSS中添加 *margin-left:-14px;

#### 3.6 z-index无论设置多高都无效问题

这种情况发生的条件有三个：

1. 父标签 position属性为relative；
2. 问题标签无position属性（不包括static）；
3. 问题标签含有浮动(float)属性。

解决方法：

1. position:relative改为position:absolute；
2. 去除浮动；
3. 浮动元素添加position属性（如relative，absolute等）。

原因：父标签position属性为relative或absolute时，子标签的absolute属性是相对于父标签而言的。而在IE6下，层级的表现有时候不是看子标签的z-index多高，而要看它们的父标签的z-index谁高谁低。所以在满足上述条件同时，要保证父标签的z-index高。

### 4. 各浏览器共有性的兼容性问题

#### 4.1 父元素为inline-block，子元素之间会多出4px的间距

原因： inline 元素中间的空白符引起的，解决办法：就是去掉空白符
解决方法：

方法一：改变HTML结构。具体方式较多，但需要修改dom结构，不实用。

方法二：负的margin。如果你的父元素设置的字号不一样，可能你的“-4px”就不能解决问题。况且在Chrome中你需要另外设置一个负的margin值才能实现同等的效果，不实用。

方法三：设置父元素的字体为“0”，然后在“inline-block”元素上重置字体需要的大小。这样处理在Firexfox,chrome等浏览器下是达到了效果，可是在Safari下可问题依然存在，根据ipc支持性，认为相对较好。

方法四：丢失结束标签。这种方法又回到了方法一，还是有一个前提，那就是“DOCTYPE”要选择对，在“XHTML”下可就问题又出来了，不实用。

方法五：通过jQuery来改变“nodeType”的值。不实用

**全兼容的样式解决方法：在父元素中设置font-size:0,用来兼容chrome，而使用letter-space:-N px来兼容safari:**



## 13. 遮挡框

```html
<div style="position:relative;*z-index:1;">
	<div id="hidebg"></div>
</div>
```

```css
#hidebg{
	position:absolute;
	background-color:#F5F6FA;
	left:0px;
	width:100%;
	filter:alpha(opacity=50);
	opacity:0.5;
	display:none;
	z-Index:1000;
}
```

```javascript
function exSwitchHd(state)
{
  var hideobj = document.getElementById("hidebg");

  if(0 == state)
  {
    hideobj.style.display = "block";
    hideobj.style.height = getoffset(id("save")).top - getoffset(id("actionBarDiv")).top + "px";
  }
  else
  {
  	hideobj.style.display = "none";
  }
}
```



## 14. 闭包

```javascript
(function(path)
 {
  	table.rows[parseInt(indexArry[downIndex],10) + 1].cells[6].childNodes[1].onclick = function()
  {
    plugin.openFileStorageFolder(path);
  }
}(path));
```



## 15. canvas实现图表

```javascript
function createChart(chartStyle, chartInfo)
{
	var chart =
	{
		/*用于配置图表的必选参数*/
		canvasId:undefined,//绘制的图表所在的canvas的ID
		chartStyle : chartStyle,//当前选中的图表类型 barChart || lineChart
		xyData:[],//用于绘制图形的XY轴数据

		/*可选，假设在同一张图上绘制折线图和柱形图，则折线图的点对应于柱形图顶部中心点*/
		fontSize:0.017,//填充的字体大小和canvas整体长度的比例，包括XY轴数据，柱形图和折线图上的信息
		graphName:undefined,//图表标题
		xValuePosition:"center",//假设绘制柱形图，X轴数据需要和柱形中轴线还是右边对齐 right || center

		xyColor:"#606060",//XY轴的颜色
		lineColor:"#006ACC",//折线图线条的颜色
		barColorFill:"#006ACC",//柱形图填充的颜色
		barColorStroke:"#006ACC",//柱形图边框颜色
		dotColor:"#006ACC",//折线图中数据点的颜色
		xyDataColor:"#909090",//XY轴上数值以及文字信息的颜色
		dataColor:"#E1E1E1",//数据点上数值的颜色
		chartBackColor:"transparent",//图表背景颜色

		chartPaddingLeft:0.044,//图表左边距和图表总宽度的比例
		chartPaddingRight:0.022,//图表右边距和图表总宽度的比例
		chartPaddingTop:0.07,//图表上边距和图表总高度的比例
		chartPaddingBottom:0.101,//图表下边距和图表总高度的比例
		barWidth:0.8,//柱形宽度和相邻柱形间距的比例，默认为0.8
		leftDis:0.5,//最左端柱形离Y轴距离和相邻柱形之间的距离的比例，默认为0.5
		smallPaddingWithY:0,//用于微调柱形和Y轴之间的距离的像素值
		smallPaddingBetween:0,//用于微调柱形之间的距离的像素值

		init:function(chartInfo)
		{
			for (var prop in chartInfo)
			{
				if (undefined != chartInfo[prop])
				{
					this[prop] = chartInfo[prop];
				}
			}

			/*支持动态生成的canvas*/
			var browser = navigator.appName;

			if ("Microsoft Internet Explorer" == browser)
			{
				var b_version = navigator.appVersion;
				var version = b_version.split(";");
				var trim_Version = version[1].replace(/[ ]/g,"");

				if (("MSIE6.0" == trim_Version) || ("MSIE7.0" == trim_Version) || ("MSIE8.0" == trim_Version))
				{
					var els = document.getElementsByTagName('canvas');
				    for (var i = 0; i < els.length; i++)
				    {
				        if (this.canvasId == els[i].id)
				        {
				        	this.can = window.G_vmlCanvasManager.initElement(els[i]);
				        }
				    }
				}
				else
			    {
			    	this.can = document.getElementById(this.canvasId);
			    }
			}
		    else
		    {
		    	this.can = document.getElementById(this.canvasId);
		    }

		    this.can.style.display = "block";
		    this.canctx = this.can.getContext("2d");

		    /*比例转化为像素值*/
			this.chartPaddingLeft = parseInt(this.chartPaddingLeft * this.can.width);
			this.chartPaddingRight = parseInt(this.chartPaddingRight * this.can.width);
			this.chartPaddingTop = parseInt(this.chartPaddingTop * this.can.height);
			this.chartPaddingBottom = parseInt(this.chartPaddingBottom * this.can.height);
			this.fontSize = parseInt(this.fontSize * this.can.width);

		    /*设置用于调节fillText中文字位置的字体宽度和高度以及字体颜色*/
		    this.fontWidth = this.fontSize;
		    this.fontHeight = this.fontSize * 1.2;
		    this.canctx.font = this.fontSize + "px 微软雅黑";

		    /*清除原有画布内容*/
			this.canctx.clearRect(0, 0, this.can.width, this.can.height);

			this.drawGraph(this.xyData, this.chartStyle, this.chartPaddingLeft, this.chartPaddingRight, this.chartPaddingTop, this.chartPaddingBottom, this.barWidth, this.leftDis, this.xValuePosition, this.can);
		},

		/*绘制XY轴以及坐标轴上的数值、图表的标题、图表的背景*/
		drawGraph:function(data, chartStyle, paddingLeft, paddingRight, paddingTop, paddingBottom, barWidth, leftDis, xValuePosition, can)
		{
			/*获取绘图需要的数据*/
			var perwidth = this.getXWidth(data, can.width, paddingLeft, paddingRight, barWidth, leftDis);//x 轴上两个数据点之间距离
			var floatExist = this.checkFloatExist(data);//搜索data中value值中是否有小数
			var yEmptyHeight = parseInt(0.07 * can.height);//用于放置Y轴备注信息的位置高度
			var yInfo = this.getYInfo(this.getMax(data), can.height, paddingBottom, paddingTop, yEmptyHeight, floatExist);
			var maxY = yInfo.maxY;//图表中Y轴所能表示的最大值
			var perY = yInfo.perY;//Y轴上每maxY所表示的像素值
			var tenTimes = yInfo.tenTimes;//Y轴显示的数据需要乘以十的倍数

			/*修改图表背景颜色*/
			if ("transparent" != this.chartBackColor)
			{
				can.style.background = this.chartBackColor;
			}

			this.drawCoordinate(data, paddingLeft, paddingRight, paddingTop, paddingBottom, barWidth, leftDis, xValuePosition, can, tenTimes, perwidth, yEmptyHeight, maxY, perY);

			/*绘制标题*/
			if (undefined != chartInfo["graphName"])
			{
				this.drawGraphName(perY, can, paddingTop, paddingLeft, paddingRight);
			}

			/*绘制柱形或折线*/
			if ("lineChart" == chartStyle)
			{
				this.drawLine(data, paddingLeft, paddingBottom, perwidth, perY, barWidth, leftDis, tenTimes);
			}
			else if ("barChart" == chartStyle)
			{
				this.drawBar(data, paddingLeft, paddingBottom, perwidth, perY, barWidth, leftDis, tenTimes);
			}
		},

		drawCoordinate:function(data, paddingLeft, paddingRight, paddingTop, paddingBottom, barWidth, leftDis, xValuePosition, can, tenTimes, perwidth, yEmptyHeight, maxY, perY)
		{
			var moveDis = 0;//根据不同的xValuePosition设置的文字偏移距离

			/*绘制XY轴*/
			this.canctx.beginPath();
			this.canctx.lineWidth = "1";
			this.canctx.strokeStyle = this.xyColor;
			this.canctx.moveTo(paddingLeft, paddingTop);
			this.canctx.lineTo(paddingLeft, can.height - paddingBottom);
			this.canctx.lineTo(can.width - paddingRight, can.height - paddingBottom);
			this.canctx.stroke();
			this.canctx.closePath();

			this.canctx.beginPath();
			this.canctx.fillStyle= this.xyDataColor;

			/*绘制X轴上的数据*/
			if ("right" == xValuePosition)
			{
				moveDis = perwidth * barWidth / 2;
			}

			for (var i = 0; i < data.length; i++)
			{
				var nameValue = data[i].name;
				var dataLengthByNumber = this.getTotalLength((!isNaN(nameValue))?nameValue.toString():nameValue);

				var x = this.getCoordX(paddingLeft, perwidth, i, leftDis, barWidth) + moveDis - this.fontWidth / 4 * dataLengthByNumber;
				var y = can.height - paddingBottom + this.fontHeight;
				this.canctx.fillText(data[i].name, x, y);
			}

			/*绘制Y轴上的数字*/
			for (var i = 1; i <= 10; i ++)
			{
				var valueOfY = maxY * i / 10;
				var dataLengthByNumber = this.getTotalLength(valueOfY.toString());

				var x = paddingLeft - this.fontWidth / 2 * dataLengthByNumber - 10;
				var y = this.getCoordY(paddingBottom, perY, valueOfY) + this.fontHeight / 2;
				this.canctx.fillText(valueOfY, x, y);
			}

			/*绘制坐标轴左下角的0*/
			if (0 == moveDis)
			{
				var x = paddingLeft - this.fontWidth / 2 * 1.2 - 2;
				var y = this.getCoordY(paddingBottom, perY, 0) + this.fontHeight / 2;
			}
			else
			{
				var x = paddingLeft - this.fontWidth / 2;
				var y = can.height - paddingBottom + this.fontHeight;
			}
			this.canctx.fillText(0, x, y);

			/*绘制Y轴上方表示10的倍数的值*/
			if (1 != tenTimes)
			{
				var content = "( ×" + tenTimes + " )";
				var dataLengthByNumber = this.getTotalLength(content);
				var x = paddingLeft - dataLengthByNumber * (this.fontWidth / 2) / 2;
				var y = paddingTop - this.fontHeight / 4;
				//var y = paddingTop + parseInt(yEmptyHeight / 2) + parseInt(this.fontHeight / 2);
				this.canctx.fillText(content, x, y);
			}
		},

		drawGraphName:function(perY, can, paddingTop, paddingLeft, paddingRight)
		{
			/*绘制标题*/
			var dataLengthByNumber = this.getTotalLength(this.graphName);
			var x = (can.width - paddingLeft - paddingRight) / 2 + paddingLeft - (this.fontWidth / 2) * (dataLengthByNumber / 2);
			var y = paddingTop / 2 + this.fontHeight / 2;
			this.canctx.fillStyle= this.xyDataColor;

			this.canctx.fillText(this.graphName, x, y);
		},

		/*折线图：根据数据在坐标轴中添加点和线*/
		drawLine:function(data, paddingLeft, paddingBottom, perwidth, perY, barWidth, leftDis, tenTimes)
		{
			var x = this.getCoordX(paddingLeft, perwidth, 0, leftDis, barWidth);
			var y = this.getCoordY(paddingBottom, perY, data[0].value / tenTimes);

			this.canctx.lineWidth = "2";
			this.canctx.strokeStyle = this.lineColor;
			this.canctx.beginPath();
			this.canctx.moveTo(x, y);

			for (var i = 1; i < data.length; i++)
			{
				var x = this.getCoordX(paddingLeft, perwidth, i, leftDis, barWidth);
				var y = this.getCoordY(paddingBottom, perY, data[i].value / tenTimes);
				this.canctx.lineTo(x, y);
			}
			this.canctx.stroke();

			/*画折线上的点*/
			this.canctx.fillStyle = this.dotColor;

			for (var i = 0; i < data.length; i++)
			{
				var x = this.getCoordX(paddingLeft, perwidth, i, leftDis, barWidth);
				var y = this.getCoordY(paddingBottom, perY, data[i].value / tenTimes);

				this.canctx.beginPath();
				this.canctx.arc(x, y, 3, 0, Math.PI*2, true);
				this.canctx.fill();
			}

			/*填充每一点对应的值*/
			this.canctx.fillStyle= this.dataColor;

			for (var i = 0; i < data.length; i++)
			{
				var dataLengthByNumber = this.getTotalLength(data[i].value.toString());
				var x = this.getCoordX(paddingLeft, perwidth, i, leftDis, barWidth) - (this.fontWidth / 2) * (dataLengthByNumber / 2);
				var y = this.getCoordY(paddingBottom, perY, data[i].value / tenTimes);

				this.canctx.fillText(data[i].value, x, y - this.fontWidth / 2);
			}
		},

		/*柱形图：根据数据在坐标轴中添加框*/
		drawBar:function(data, paddingLeft, paddingBottom, perwidth, perY, barWidth, leftDis, tenTimes)
		{
			/*此处绘制柱形图
			先绘制柱形*/
			this.canctx.lineWidth = "2";
			this.canctx.fillStyle = this.barColorFill;

			/*先绘制第一个柱形*/
			var x = this.getCoordX(paddingLeft, perwidth, 0, leftDis, barWidth) - perwidth * barWidth / 2;
			var y = this.getCoordY(paddingBottom, perY, data[0].value / tenTimes);
			this.canctx.beginPath();
			this.canctx.rect(x + this.smallPaddingWithY, y, perwidth * barWidth - this.smallPaddingBetween - this.smallPaddingWithY, data[0].value / tenTimes * perY);
			this.canctx.fill();
			var smallPadding = 0;

			/*绘制第2到n个柱形*/
			for (var i = 1; i < data.length; i++)
			{
				var x = this.getCoordX(paddingLeft, perwidth, i, leftDis, barWidth) - perwidth * barWidth / 2;
				var y = this.getCoordY(paddingBottom, perY, data[i].value / tenTimes);

				this.canctx.beginPath();
				this.canctx.rect(x + this.smallPaddingBetween, y, perwidth * barWidth - 2 * this.smallPaddingBetween, data[i].value / tenTimes * perY);
				this.canctx.fill();
			}

			/*再绘制柱形上的数值*/
			this.canctx.fillStyle = this.dataColor;
			for (var i = 0; i < data.length; i++)
			{
				var dataLengthByNumber = this.getTotalLength(data[i].value.toString());
				var x = this.getCoordX(paddingLeft, perwidth, i, leftDis, barWidth) - (this.fontWidth / 2) * (dataLengthByNumber / 2);
				var y = this.getCoordY(paddingBottom, perY, data[i].value / tenTimes);

				this.canctx.fillText(data[i].value, x, y - this.fontWidth / 4);
			}
		},

		/*获取value值占据的总宽度(像素)相对于单个数字宽度(像素)的倍数*/
		getTotalLength:function(value)
		{
			var totalLength = 0;

			for (var i = 0; i < value.length; i++)
			{
				if (/[\u4E00-\u9FA5]/g.test(value[i]))
				{
					totalLength += 2;
				}
				else
				{
					totalLength += 1;
				}
			}

			return totalLength;
		},

		/*搜索data中value值中是否有小数*/
		checkFloatExist:function(data)
		{
			for (var i = 0; i < data.length; i++)
			{
				if (data[i].value != parseInt(data[i].value))
				{
					return 1;
				}
			}

			return 0;
		},

		/*x 轴每一个数据占据的宽度*/
		getXWidth:function(data, width, paddingLeft, paddingRight, barWidth, leftDis)
		{
			return ((width - paddingLeft - paddingRight) / (data.length + leftDis - (1 - barWidth) / 2));
		},

		/*根据pindex获取X轴上相邻两个坐标点之间的距离，表示柱形图中轴线或者折线图的点的横坐标*/
		getCoordX:function(paddingLeft, perwidth, ptindex, leftDis, barWidth)
		{
			return paddingLeft + perwidth * (leftDis + barWidth / 2 + ptindex);
		},

		/*根据y的值获取对应的坐标*/
		getCoordY:function(paddingBottom, perY, yValue)
		{
			return (this.can.height - paddingBottom - perY * yValue);
		},

		/*返回Y轴上数值能够显示的最大值和Y轴单位数值占有的像素值*/
		getYInfo:function(maxYNumber, height, paddingBottom, paddingTop, yEmptyHeight, floatExist)
		{
			var tenTimes = 1;
			var maxY = undefined;

			while (maxYNumber > 100)
			{
				tenTimes *= 10;
				maxYNumber /= 10;
			}

			if (10 >= maxYNumber)
			{
				maxY = 10;
			}
			else if (10 < maxYNumber)
			{
				maxY = (parseInt(maxYNumber / 10) + 1) * 10;
			}

			return {perY:(height - paddingBottom - paddingTop - yEmptyHeight) / maxY, maxY:maxY, tenTimes:tenTimes};
		},

		/*返回最大值*/
		getMax:function(data)
		{
			var maxYNumber = data[0].value;
			var length = data.length;
			for (var i = 1; i < length; i++)
			{
				if (maxYNumber < data[i].value)
				{
					maxYNumber = data[i].value;
				}
			}
			return maxYNumber;
		}
	};

	chart.init(chartInfo);
}
```



## 16. 输入框滚动

```javascript
this.getWheelDelta = function(event){
  event = event || window.event;
  if (event.wheelDelta)
  {
    return window.opera&&window.opera.version < 9.5?-event.wheelDelta:event.wheelDelta;
  }
  else
  {
    return -event.detail*40;
  }
};

this.scrollWheelHd = function(event)
{
  if(gTimeEditInputId == null)
  {
    return;
  }

  var targerObj = id(gTimeEditInputId);
  var tmpValue = parseInt(targerObj.value, 10);
  var direct = (this.getWheelDelta(event) > 0) ? 0 : 1;

  switch (gTimeEditInputId)
  {
    case "timeHour":
      if(direct == 0)
      {
        if (tmpValue == 23)
        {
          targerObj.value = "00";
        }
        else
        {
          tmpValue++;
          targerObj.value = midifyTimeStr(tmpValue);
        }
      }
      else
      {
        if (tmpValue == 0)
        {
          targerObj.value = "23";
        }
        else
        {
          tmpValue--;
          targerObj.value = midifyTimeStr(tmpValue);
        }
      }
      break;
    case "timeMinute":
    case "timeSecond":
      if(direct == 0)
      {
        if (tmpValue == 59)
        {
          targerObj.value = "00";
        }
        else
        {
          tmpValue++;
          targerObj.value = midifyTimeStr(tmpValue);
        }
      }
      else
      {
        if (tmpValue == 0)
        {
          targerObj.value = "59";
        }
        else
        {
          tmpValue--;
          targerObj.value = midifyTimeStr(tmpValue);
        }
      }
      break;
    default:
      break;
  }

  clearSelection();
}

this.bindScrollWheel = function()
{
  if (document.attachEvent)
  {
    document.body.attachEvent("onmousewheel", function(event){
      event = event || window.event;scrollWheelHd(event)});
  }
  else
  {
    document.body.addEventListener("mousewheel",
                                   function(event){event = event || window.event;scrollWheelHd(event)}, false);
    document.body.addEventListener("DOMMouseScroll",
                                   function(event){event = event || window.event;scrollWheelHd(event)}, false);
  }
}
```

onmousewheel +  getWheelDelta 测试滚动距离

...