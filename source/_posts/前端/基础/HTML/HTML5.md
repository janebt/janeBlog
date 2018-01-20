---
title: HTML5总结
date: 2017-01-01 11:33:34
updated: 2017-12-16 15:25:00
tags:
  - HTML5
categories:
  - 基础
toc: true
---
总结H5。后续补上demo。

<!-- more -->

# 提纲

- HTML5概念
- 多媒体
- 拖放
- Canvas & SVG
- 地理定位
- Web存储
- 应用缓存
- Web Workers
- 服务器发送事件
- 表单
- Hisyory API




# 知识点

## HTML5概念

### 什么是HTML5

最新的HTML标准，主要目标是提供所有内容而不需要任何的像flash，silverlight等的额外插件

### SGML、XML、HTML关系

SGML（标准通用标记语言）是一个标准，告诉我们怎么去指定文档标记。他是只描述文档标记应该是怎么样的元语言，HTML（超文本标记语言）是被用SGML描述的标记语言。

因此利用SGML创建了HTML参照和必须共同遵守的DTD（文档类型定义），你会经常在HTML页面的头部发现“DOCTYPE”属性，用来定义用于解析目标DTD

XML（可扩展标记语言）使用了SGML，例如：在SGML中你必须使用起始和结束标签，但是在XML你可以有自动关闭的结束标签。

XHTML创建于XML，他被使用在HTML4.0中。

总之，SGML是所有类型的父类，较旧的HTML利用SGML，HTML4.0使用派生自XML的XHTML

### 为什么HTML5不需要DTD

HTML5没有使用SGML或者XHTML

### HTML5的页面结构变化

一个典型的WEB页面包含头部，脚部，导航，中心区域，侧边栏。HTML4要使用DIV标签。HTML5中为这些区域创建元素名称：`<header>`, `<footer>`等

### HTML5 文档类型和字符集

```html
<!doctype html>
<meta charset=”UTF-8″>
```

### 废弃的标签

- frame
- frameset
- noframe
- applet
- big
- center
- basefront

### 新的 API

- Media API
- Text Track API
- Application Cache API
- User Interaction
- Data Transfer API
- Command API
- Constraint Validation API
- History API




## 多媒体

### 嵌入音频

```html
<audio controls>
    <source src=”jamshed.mp3″ type=”audio/mpeg”>
    Your browser does’nt support audio embedding feature.
</audio>
```

### 嵌入视频

```html
<video width=”450″ height=”340″ controls>
  <source src=”jamshed.mp4″ type=”video/mp4″>
   Your browser does’nt support video embedding feature.
</video>
```

### 其他媒体标签

`<embed>` 标签定义嵌入的内容，比如插件

```html
<embed type=”video/quicktime” src=”Fishing.mov”>
```

`<source>` 对于定义多个数据源很有用

```html
<video width=”450″ height=”340″ controls>
     <source src=”jamshed.mp4″ type=”video/mp4″>
     <source src=”jamshed.ogg” type=”video/ogg”>
</video>
```

`<track>` 标签为诸如 video 元素之类的媒介规定外部文本轨道。 用于规定字幕文件或其他包含文本的文件，当媒介播放时，这些文件是可见的。

```html
<video width=”450″ height=”340″ controls>
     <source src=”jamshed.mp4″ type=”video/mp4″>
     <source src=”jamshed.ogg” type=”video/ogg”>
     <track kind=”subtitles” label=”English” src=”jamshed_en.vtt” srclang=”en” default></track>
      <track kind=”subtitles” label=”Arabic” src=”jamshed_ar.vtt” srclang=”ar”></track>
</video>
```



## 拖放

### 代码

```html
<!DOCTYPE HTML>
<html>
<head>
<script type="text/javascript">
function allowDrop(ev)
{
  ev.preventDefault();
}

function drag(ev)
{
  ev.dataTransfer.setData("Text",ev.target.id);
}

function drop(ev)
{
  ev.preventDefault();
  var data=ev.dataTransfer.getData("Text");
  ev.target.appendChild(document.getElementById(data));
}
</script>
</head>
<body>

<div id="div1" ondrop="drop(event)" ondragover="allowDrop(event)"></div>
<img id="drag1" src="img_logo.gif" draggable="true" ondragstart="drag(event)" width="336" height="69" />

</body>
</html>
```

### 知识点

**设置元素为可拖放 - draggable**

draggable 属性设置为 true 

**拖动什么 - ondragstart 和 setData()**

ondragstart 属性调用了一个函数，drag(event)，它规定了被拖动的数据。

dataTransfer.setData() 方法设置被拖数据的数据类型和值

**放到何处 - ondragover**

默认地，无法将数据/元素放置到其他元素中。如果需要设置允许放置，我们必须阻止对元素的默认处理方式。

```javascript
event.preventDefault()
```

**进行放置 - ondrop**

ondrop 属性调用了一个函数，drop(*event*)



## Canvas & SVG

### 知识点

#### Canvas API

##### 绘图方法

```javascript
<canvas id="myCanvas" width="400" height="200">
  您的浏览器不支持canvas！
</canvas>

var canvas = document.getElementById('myCanvas');
if (canvas.getContext) {
  var ctx = canvas.getContext('2d');
}

//（1）绘制路径
ctx.beginPath(); // 开始路径绘制
ctx.moveTo(20, 20); // 设置路径起点，坐标为(20,20)
ctx.lineTo(200, 20); // 绘制一条到(200,20)的直线
ctx.lineWidth = 1.0; // 设置线宽
ctx.strokeStyle = '#CC0000'; // 设置线的颜色
ctx.stroke(); // 进行线的着色，这时整条线才变得可见
//最后，还可以使用closePath方法，自动绘制一条当前点到起点的直线，形成一个封闭图形，省却使用一次lineto方法。

//（2）绘制矩形
//实心矩形
ctx.fillStyle = 'yellow';
ctx.fillRect(50, 50, 200, 100); 
//空心矩形
ctx.strokeRect(10,10,200,100);
//清除某个矩形区域的内容
ctx.clearRect(100,50,50,50);  

//（3）绘制文本
// 设置字体
ctx.font = "Bold 20px Arial"; 
// 设置对齐方式
ctx.textAlign = "left";
// 设置填充颜色
ctx.fillStyle = "#008600"; 
// 设置字体内容，以及在画布上的位置,绘制文本,不支持文本断行
ctx.fillText("Hello!", 10, 50); 
// 绘制空心字
ctx.strokeText("Hello!", 10, 100); 

//（4）绘制圆形和扇形
//绘制扇形,startAngle和endAngle则是扇形的起始角度和终止角度（以弧度表示），anticlockwise表示做图时应该逆时针画（true）还是顺时针画（false）
ctx.arc(x, y, radius, startAngle, endAngle, anticlockwise);
//绘制实心的圆形
ctx.beginPath(); 
ctx.arc(60, 60, 50, 0, Math.PI*2, true); 
ctx.fillStyle = "#000"; 
ctx.fill();
//绘制空心圆形
ctx.beginPath(); 
ctx.arc(60, 60, 50, 0, Math.PI*2, true); 
ctx.lineWidth = 1.0; 
ctx.strokeStyle = "#000"; 
ctx.stroke();

//（5）设置渐变色
var myGradient = ctx.createLinearGradient(0, 0, 0, 160); 
myGradient.addColorStop(0, "#BABABA"); 
myGradient.addColorStop(1, "#636363");
//使用方法如下：
ctx.fillStyle = myGradient;
ctx.fillRect(10,10,200,100);

//（6）设置阴影
ctx.shadowOffsetX = 10; // 设置水平位移
ctx.shadowOffsetY = 10; // 设置垂直位移
ctx.shadowBlur = 5; // 设置模糊度
ctx.shadowColor = "rgba(0,0,0,0.5)"; // 设置阴影颜色
ctx.fillStyle = "#CC0000"; 
ctx.fillRect(10,10,200,100)
```

##### 图像处理方法

```javascript
//drawImage方法:将图像文件插入画布,drawImage方法只能在图像完全载入后才能调用
var image = new Image();
image.onload = function() {
  var canvas = document.createElement('canvas');
  canvas.width = image.width;
  canvas.height = image.height;
  canvas.getContext('2d').drawImage(image, 0, 0);
  // 插入页面底部
  document.body.appendChild(image);
  return canvas;
}
image.src = 'image.png';

//getImageData方法，putImageData方法:读取Canvas的内容，返回一个对象，包含了每个像素的信息。该数组的值，依次是每个像素的红、绿、蓝、alpha通道值，因此该数组的长度等于 图像的像素宽度 x 图像的像素高度 x 4，每个值的范围是0–255。这个数组不仅可读，而且可写，因此通过操作这个数组的值，就可以达到操作图像的目的。修改这个数组以后，使用putImageData方法将数组内容重新绘制在Canvas上。
var imageData = context.getImageData(0, 0, canvas.width, canvas.height);
context.putImageData(imageData, 0, 0);

//toDataURL方法:将Canvas数据重新转化成一般的图像文件形式。
function convertCanvasToImage(canvas) {
  var image = new Image();
  image.src = canvas.toDataURL('image/png');
  return image;
}

//save方法，restore方法:save方法用于保存上下文环境，restore方法用于恢复到上一次保存的上下文环境。
ctx.save();
ctx.fillStyle = '#CC0000';
ctx.fillRect(10,10,150,100);
ctx.restore();
ctx.fillStyle = '#000000';
ctx.fillRect(180,10,150,100);
```

##### 应用

[Canvas API 详解](http://blog.csdn.net/gertyy/article/details/53637951)

### 问题

#### Canvas和SVG

##### 什么是SVG？

SVG（Scalable Vector Graphics可缩放矢量图形）表示可缩放矢量图形。他是基于文本的图形语言，使用文本，线条，点等来进行图像绘制，这使得他轻便，显示更加迅速

```html
<svg id="svgelem" height="[object SVGAnimatedLength]" xmlns="http://www.w3.org/2000/svg">
<line style="stroke: rgb(255, 0, 0); stroke-width: 2px;" y2="[object SVGAnimatedLength]" x2="[object SVGAnimatedLength]" y1="[object SVGAnimatedLength]" x1="[object SVGAnimatedLength]">
</line>
```

##### Canvas和SVG图形的区别是什么？

| SVG                                      | Canvas                          |
| ---------------------------------------- | ------------------------------- |
| 这个就好像绘制和记忆，换句话说任何使用SVG绘制的形状都能被记忆和操作，浏览器可以再次显示 | Canvas就像绘制和忘记，一旦绘制完成你不能访问像素和操作它 |
| SVG对于创建图形例如CAD软件是良好的，一旦东西绘制，用户就想去操作它     | Canvas在绘制和忘却的场景例如动画和游戏是良好的      |
| 因为为了之后的操作，需要记录坐标，所以比较缓慢                  | 因为没有记住以后事情的意向，所以更快              |
| 我们可以用绘制对象的相关事件处理                         | 我们不能使用绘制对象的相关事件处理，因为我们没有他们的参考   |
| 分辨率无关                                    | 分辨率相关                           |

#### Canvas使用

##### 画线

- 定义Canvas区域
- 获取访问canvas上下文区域
- 绘制图形

```html
<body  onload="DrawMe();">
<canvas id="mycanvas" width="600" height="500" style="border:1px solid #000000;"></canvas>
</body>
<script>
function DrawMe()
{
  var c=document.getElementById("mycanvas");
  var ctx=c.getContext("2d");
  ctx.moveTo(10,10);
  ctx.lineTo(200,100);
  ctx.stroke();
}
```

##### Canvas和SVG画矩形

SVG绘制矩形

```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
<rect style="fill: rgb(0, 0, 255); stroke-width: 1px; stroke: rgb(0, 0, 0);" height="[object SVGAnimatedLength]" width="[object SVGAnimatedLength]">
</rect>
```

Canvas绘制矩形

```javascript
var c=document.getElementById("mycanvas");
var ctx=c.getContext("2d");
ctx.rect(20,20,150,100);
ctx.stroke();
```



## [地理定位](http://www.w3school.com.cn/html5/html_5_geolocation.asp)

### 知识点

getCurrentPosition() 方法

```javascript
navigator.geolocation.getCurrentPosition(showPosition);
```

getCurrentPosition() 方法的第二个参数用于处理错误。它规定当获取用户位置失败时运行的函数



## Web存储

### localStorage的特点

1. localStorage拓展了cookie的4K限制。


2. 数据直接存储到本地，5M大小，相比于cookie可以节约带宽。

   注意：遵循同源策略的，所以不同的网站直接是不能共用相同的localStorage

3. localStorage只支持string类型的存储。

### API

```javascript
//添加
localStorage["a"]=1;
localStorage.b=1;
localStorage.setItem(“c”,”India”);

//读取
var a = localStorage["a"];
var b = localStorage.b;
var country = localStorage.getItem(“Key001”);

//改
localStorage["a"]=2;

//删
localStorage.clear();	//全删
localStorage.removeItem("a");	//部分删

//获取键值
for(i=0;i<localStorage.length;i++){
  var key=localStorage.key(i);
}
```

可以存JSON,注意**stringify** 和 **parse**

```javascript
var data={
  name:'xiecanyong',
  sex:'man',
  hobby:'program'
};

var d=JSON.stringify(data);

storage.setItem("data",d);

//将JSON字符串转换成为JSON对象输出
var json=storage.getItem("data");
var jsonObj=JSON.parse(json);
```

### 本地存储和cookies区别

|         | Cookies                                | Local storage                            |
| ------- | -------------------------------------- | ---------------------------------------- |
| 客户端/服务端 | 客户端和服务端都能访问数据。Cookie的数据通过每一个请求发送到服务端   | 只有本地浏览器端可访问数据，服务器不能访问本地存储直到故意通过POST或者GET的通道发送到服务器 |
| 大小      | 每个cookie有4K                            | 每个域5MB                                   |
| 过期      | Cookies有有效期，所以在过期之后cookie和cookie数据会被删除 | 没有过期数据，无论最后用户从浏览器删除或者使用Javascript程序删除，我们都需要删除 |



## 应用缓存

### [知识点](http://www.w3school.com.cn/html5/html_5_app_cache.asp)

#### 应用缓存是什么

应用缓存可以帮助你指定哪些文件需要缓存，哪些不需要。

#### 应用程序缓存优势

- 离线浏览 - 用户可在应用离线时使用它们
- 速度 - 已缓存资源加载得更快
- 减少服务器负载 - 浏览器将只从服务器下载更新过或更改过的资源。

#### 如何实现

首先我们需要指定”manifest”文件，Mainfest文件的内容类型应是“text/cache-manifest”.

```http
CACHE MANIFEST
# version 1.0
CACHE :
Login.aspx
```

```php
Response.ContentType = "text/cache-manifest";
Response.Write("CACHE MANIFEST \n");
Response.Write("# 2012-02-21 v1.0.0 \n");
Response.Write("CACHE : \n");
Response.Write("Login.aspx \n");
Response.Flush();
Response.End();
```

创建一个缓存manifest文件以后，接下来的事情实在HTML页面中提供mainfest连接

### 问题

#### 应用缓存中的回退是什么

应用缓存中的回退帮助你指定在服务器不可访问的时候，将会显示某文件。例如在下面的manifest文件中，我们说如果谁敲击了”/home”同时服务器不可到达的时候，”homeoffline.html”文件应送达

```http
FALLBACK:
/home/ /homeoffline.html
```

#### 应用缓存中的网络是什么

网络命令描述不需要缓存的文件，例如以下代码中，我们说”home.aspx”永远都不应该被缓存或者离线访问。

```http
NETWORK:
home.aspx
```

#### 应用程序缓存和浏览器缓存有什么区别

与传统浏览器缓存相比，它不强制用户访问的网站内容被缓存。



## Web Workers

### 知识点

#### 什么是Web Workers，有什么用

web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。帮助我们用异步执行Javascript文件

#### 浏览器支持

除了 Internet Explorer

#### API

##### 检测 Web Worker 支持

```javascript
if(typeof(Worker)!=="undefined")
{
  // Yes! Web worker support!
  // Some code.....
}
else
{
  // Sorry! No Web Worker support..
}
```

##### 如何创建

**主线程**通过Javascript文件名创建**worker**对象

```javascript
var worker = new Worker("MyHeavyProcess.js");
```

**子线程**需要使用“**PostMessage**”发送信息给worker对象

```javascript
worker.postMessage();
```

**主线程**当worker线程发送数据的时候，我们在调用结束的时候，通过”**onMessage**”事件获取

```javascript
worker.onmessage = function (e) 
{
document.getElementById("txt1").value = e.data;
};
```

出错处理。**onerror**监听，错误消息可以通过**e.message**来获取。

##### 如何中止

**主线程**。注意：一旦terminate后，无法重新启用，只能另外创建。

```javascript
w.terminate();
```

### 问题

#### Web Worker线程的限制是什么

Web worker线程不能修改HTML元素，全局变量和Window.Location一类的窗口属性。可以自由使用Javascript数据类型，XMLHttpRequest调用等。

简而言之，就是**允许JavaScript创建多个线程，但是子线程完全受主线程控制，且不得操作DOM**。

> 原因：worker.js执行的上下文，与主页面html执行时的上下文并不相同，最顶层的对象并不是window，woker.js执行的全局上下文，是个叫做**WorkerGlobalScope**的东西，所以无法访问window、与window相关的DOM API，但是可以与setTimeout、setInterval等协作。



## 服务器发送事件

### 知识点

#### API

##### EventSource，onmessage

```javascript
var source=new EventSource("demo_sse.php");

source.onmessage=function(event){
  document.getElementById("result").innerHTML+=event.data + "<br />";
};
```

##### 服务器端代码实例

**把 "Content-Type" 报头设置为 "text/event-stream"。**

##### ASP 代码 (VB) (demo_sse.asp):

```asp
<%
Response.ContentType="text/event-stream"
Response.Expires=-1
Response.Write("data: " & now())
Response.Flush()
%>
```

##### EventSource 对象

| 事件        | 描述           |
| --------- | ------------ |
| onopen    | 当通往服务器的连接被打开 |
| onmessage | 当接收到消息       |
| onerror   | 当错误发生        |

#### 浏览器支持

除了IE。



## WebSQL

### 知识点

#### 什么是WebSQL

WebSQL是一个在浏览器客户端的结构关系数据库，这是浏览器内的本地RDBMS(关系型数据库系统)，你可以使用SQL查询

#### 如何使用

第一步我们需要做的是使用如下所示的“OpenDatabase”方法打开数据库

```javascript
var db=openDatabase('dbCustomer','1.0','Customer app’, 2 * 1024 * 1024);
```

为了执行SQL，我们需要使用“transaction”方法，并调用”executeSql”方法来使用SQL

```javascript
db.transaction(function (tx) 
{
  tx.executeSql('CREATE TABLE IF NOT EXISTS tblCust(id unique, customername)');
  tx.executeSql('INSERT INTO tblcust (id, customername) VALUES(1, "shiv")');
  tx.executeSql('INSERT INTO tblcust (id, customername) VALUES (2, "raju")');
  tx.executeSql('SELECT * FROM tblcust', [], function (tx, results) {
   for (i = 0; i < len; i++)
	{
     msg = "<p><b>" + results.rows.item(i).log + "</b></p>";
     document.querySelector('#customer).innerHTML +=  msg;
	}
 }, null);
});
```

### 问题

#### WebSql是HTML5的一个规范吗

不是，许多人把它标记为HTML5，但是他不是HTML5的规范的一部分，这个规范是基于SQLite的



## History

### 知识点

#### 浏览器支持

```javascript
return !!(window.history && history.pushState);
```

#### History API

##### 两个新方法

history.pushState(); 和 history.replaceState();

两个方法的主要区别就是：pushState()是在history栈中添加一个新的条目，replaceState()是替换当前的记录值。

pushState()和replaceState()参数一样，参数说明如下：

1. state：存储JSON字符串，可以用在popstate事件中。
2. title：现在大多数浏览器不支持或者忽略这个参数，最好用null代替
3. url：任意有效的URL，用于更新浏览器的地址栏，并不在乎URL是否已经存在地址列表中。更重要的是，它不会重新加载页面。

##### history对象常见用法

- history.back();//返回上一页，相当于浏览器上后退功能。
- history.forward();//去到下一页，相当于浏览器的前进功能。
- histoty.go(int);//去到指定的浏览历史记录页面。int是正的时候，就是向前int个历史记录，如果没有那么多，就没有行为。int是负数的时候那么就会向后，为0页面即没有任何变化。



## 表单元素

### 知识点

#### keygen 元素

keygen 元素的作用是提供一种验证用户的可靠方法。

#### 新的表单属性

##### 新的 form 属性

- autocomplete
- novalidate

##### 新的 input 属性

- autocomplete
- autofocus
- form
- form overrides (formaction, formenctype, formmethod, formnovalidate, formtarget)
- height 和 width
- list
- min, max 和 step
- multiple
- pattern (regexp)
- placeholder
- required


#### datalist

HTML5中的Datalist元素有助于提供文本框自动完成特性

datalist 元素规定输入域的选项列表。列表是通过 datalist 内的 option 元素创建的。

如需把 datalist 绑定到输入域，请用输入域的 list 属性引用 datalist 的 id：

```html
<input list="Country">
<datalist id="Country">
  <option value="India">
  <option value="Italy">
  <option value="Iran">
  <option value="Israel">
  <option value="Indonesia">
</datalist>
```

> 提示：option 元素永远都要设置 value 属性。

#### 新的表单元素

1. Color
2. Date
3. Datetime-local
4. Email
5. Time
6. Url
7. Range
8. Telephone
9. Number
10. Search

```html
如果你想显示颜色选择对话框
<input type="color" name="favcolor">

如果你想显示日历对话框
<input type="date" name="bday">

如果你想显示含有本地时间的日历
<input type="datetime-local" name="bdaytime">

如果你想创建一个含有email校验的HTML文本框，我们可以设置类型为“email”
<input type="email" name="email">

对于URL验证设置类型为”url”
<input type="url" name="sitename">

如果你想限定数字范围，你可以设置类型为“number”
<input type="number" name="quantity" min="1" max="5">

如果你想显示范围控制，你可以使用类型”range”
<input type="range" min="0" max="10" step="2" value="6">

想让文本框作为搜索引擎
<input type="search" name="googleengine">

想只能输入时间
<input type="time" name="usr_time">

如果你想使用文本框接受电话号码
<input type="tel" name="mytel">
```

#### 输出元素

当你需要计算两个输入的和值到一个标签中的时候你需要输出元素。

例如你有两个文本框，你想将来自这两个输入框中的数字求和并放到标签中。

```html
<form onsubmit="return false"  oninput="o.value = parseInt(a.value) + parseInt(b.value)">
  <input name="a" type="number"> +
  <input name="b" type="number"> =
  <output name="o" />
</form>
```
