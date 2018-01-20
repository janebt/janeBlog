---
title: Ajax
date: 2017-08-30 10:35:34
updated: 2017-08-30 10:35:34
tags:
  - JavaScript
categories:
  - 基础
toc: true
---
本文介绍Ajax知识体系。

参考一篇非常好的文章[《Ajax知识体系大梳理》](https://segmentfault.com/a/1190000008697448)，内容比较多，我就做一点关注点总结。

<!-- more -->
# 知识点

## 导读

Ajax 全称 Asynchronous JavaScript and XML, 即异步JS与XML. 

## 浏览器为ajax做了什么

1.标准浏览器通过 `XMLHttpRequest` 对象实现了ajax的功能. 只需要通过一行语句便可创建一个用于发送ajax请求的对象.

```javascript
var xhr = new XMLHttpRequest();
```

2.IE浏览器通过 `XMLHttpRequest` 或者 `ActiveXObject` 对象同样实现了ajax的功能.

### MSXML

IE6及更低版本浏览器只能使用 `ActiveXObject` 对象来创建 XMLHttpRequest 对象实例. 创建时需要指明一个类似"Microsoft.XMLHTTP"这样的ProgID. 

IE5及之后的浏览器均可以通过如下语句获取xhr对象:

```javascript
var xhr = new ActiveXObject("Msxml2.XMLHTTP");// 即MSXML3
var xhr = new ActiveXObject("Microsoft.XMLHTTP");// 很老的api,虽然浏览器支持,功能可能不完善,故不建议使用
```

### 全平台兼容的XMLHttpRequest对象

```javascript
function getXHR(){
  var xhr = null;
  if(window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
  } else if (window.ActiveXObject) {
    try {
      xhr = new ActiveXObject("Msxml2.XMLHTTP");
    } catch (e) {
      try {
        xhr = new ActiveXObject("Microsoft.XMLHTTP");
      } catch (e) { 
        alert("您的浏览器暂不支持Ajax!");
      }
    }
  }
  return xhr;
}
```

## ajax有没有破坏js单线程机制

一般情况下, 浏览器有如下四种线程:

- GUI渲染线程
- javascript引擎线程
- 浏览器事件触发线程
- HTTP请求线程

通常, 它们的线程间交互以事件的方式发生, 通过事件回调的方式予以通知. 而事件回调, 又是以先进先出的方式添加到`任务队列` 的末尾 , 等到js引擎空闲时, `任务队列` 中排队的任务将会依次被执行. 这些事件回调包括 setTimeout, setInterval, click, ajax异步请求等回调.

**浏览器中, js引擎线程会循环从 任务队列 中读取事件并且执行, 这种运行机制称作 Event Loop (事件循环).**

对于一个ajax请求, js引擎首先生成 `XMLHttpRequest` 实例对象, open过后再调用send方法. 至此, 所有的语句都是同步执行. 但从send方法内部开始, 浏览器为将要发生的网络请求创建了新的http请求线程, 这个线程独立于js引擎线程, 于是网络请求异步被发送出去了. 另一方面, js引擎并不会等待 ajax 发起的http请求收到结果, 而是直接顺序往下执行.

当ajax请求被服务器响应并且收到response后, 浏览器事件触发线程捕获到了ajax的回调事件 `onreadystatechange` (当然也可能触发onload, 或者 onerror等等) . 该回调事件并没有被立即执行, 而是被添加到 `任务队列` 的末尾. 直到js引擎空闲了, `任务队列` 的任务才被捞出来, 按照添加顺序, 挨个执行, 当然也包括刚刚append到队列末尾的 `onreadystatechange` 事件.

在 `onreadystatechange` 事件内部, 有可能对dom进行操作. 此时浏览器便会挂起js引擎线程, 转而执行GUI渲染线程, 进行UI重绘(repaint)或者回流(reflow). 当js引擎重新执行时, GUI渲染线程又会被挂起, GUI更新将被保存起来, 等到js引擎空闲时立即被执行.

以上整个ajax请求过程中, 有涉及到浏览器的4种线程. 其中除了 `GUI渲染线程` 和 `js引擎线程` 是互斥的. 其他线程相互之间, 都是可以并行执行的. 通过这样的一种方式, ajax并没有破坏js的单线程机制.

## ajax与setTimeout排队问题

由于ajax异步, setTimeout回调本应该最先被执行, 然而实际上, 一次ajax请求, 并非所有的部分都是异步的, 至少**"readyState==1"的 `onreadystatechange` 回调以及 `onloadstart` 回调就是同步执行的**. 因此它们的输出排在最前面.

## XMLHttpRequest 属性解读

追根溯源, XMLHttpRequest 实例对象具有如下的继承关系. (下面以a<<b表示a继承b)

`xhr` << `XMLHttpRequest.prototype` << `XMLHttpRequestEventTarget.prototype` << `EventTarget.prototype` << `Object.prototype`

通常, 一个xhr实例对象拥有10个普通属性+9个方法.

### readyState

只读属性, readyState属性记录了ajax调用过程中所有可能的状态.

### onreadystatechange

onreadystatechange事件回调方法在readystate状态改变时触发, 在一个收到响应的ajax请求周期中, onreadystatechange 方法会被触发4次. 因此可以在 onreadystatechange 方法中绑定一些事件回调

### status

只读属性, status表示http请求的状态, 初始值为0. 如果服务器没有显式地指定状态码, 那么status将被设置为默认值, 即200.

### statusText

只读属性, statusText表示服务器的响应状态信息, 它是一个 UTF-16 的字符串, 请求成功且status==20X时, 返回大写的 `OK` . 请求失败时返回空字符串. 其他情况下返回相应的状态描述. 比如: 301的 `Moved Permanently` , 302的 `Found` , 303的 `See Other` , 307 的 `Temporary Redirect` , 400的 `Bad Request` , 401的 `Unauthorized` 等等.

### onloadstart

onloadstart事件回调方法在ajax请求发送之前触发, 触发时机在 `readyState==1` 状态之后, `readyState==2` 状态之前.

### onprogress

onprogress事件回调方法在 `readyState==3` 状态时开始触发, 默认传入 ProgressEvent 对象, 可通过 `e.loaded/e.total` 来计算加载资源的进度, 该方法用于获取资源的下载进度.

### onload

onload事件回调方法在ajax请求成功后触发, 触发时机在 `readyState==4` 状态之后.

可用于捕捉到一个ajax异步请求的成功状态, 并且执行回调。

### onloadend

onloadend事件回调方法在ajax请求完成后触发, 触发时机在 `readyState==4` 状态之后(收到响应时) 或者`readyState==2` 状态之后(未收到响应时).

### timeout

timeout属性用于指定ajax的超时时长. 

### ontimeout

ontimeout方法在ajax请求超时时触发, 通过它可以在ajax请求超时时做一些后续处理.

### response responseText

均为只读属性, response表示服务器的响应内容, 相应的, responseText表示服务器响应内容的文本形式.

### responseXML

只读属性, responseXML表示xml形式的响应数据, 缺省为null, 若数据不是有效的xml, 则会报错.

### responseType

responseType表示响应的类型, 缺省为空字符串, 可取 `"arraybuffer"` , `"blob"` , `"document"` , `"json"` , and `"text"` 共五种类型.

### responseURL

responseURL返回ajax请求最终的URL, 如果请求中存在重定向, 那么responseURL表示重定向之后的URL.

### withCredentials

withCredentials是一个布尔值, 默认为false, 表示跨域请求中不发送cookies等信息.

### abort

abort方法用于取消ajax请求, 取消后, readyState 状态将被设置为 `0` (`UNSENT`). 

### getResponseHeader

getResponseHeader方法用于获取ajax响应头中指定name的值. 

### getAllResponseHeaders

getAllResponseHeaders方法用于获取所有安全的ajax响应头, 响应头以字符串形式返回. 

### setRequestHeader

设置请求头

### onerror

onerror方法用于在ajax请求出错后执行. 通常只在网络出现问题时或者ERR_CONNECTION_RESET时触发

### upload

upload属性默认返回一个 `XMLHttpRequestUpload` 对象, 用于上传资源

### overrideMimeType

overrideMimeType方法用于强制指定response 的 MIME 类型, 即强制修改response的 `Content-Type` . 

## XHR一级

XHR1时, xhr对象具有如下缺点:

- 仅支持文本数据传输, 无法传输二进制数据.
- 传输数据时, 没有进度信息提示, 只能提示是否完成.
- 受浏览器 `同源策略` 限制, 只能请求同域资源.
- 没有超时机制, 不方便掌控ajax请求节奏.

## XHR二级

XHR2针对XHR1的上述缺点做了如下改进:

- 支持二进制数据, 可以上传文件, 可以使用FormData对象管理表单.
- 提供进度提示, 可通过 `xhr.upload.onprogress` 事件回调方法获取传输进度.
- 依然受 `同源策略` 限制, 这个安全机制不会变. XHR2新提供 `Access-Control-Allow-Origin` 等headers, 设置为 `*` 时表示允许任何域名请求, 从而实现跨域CORS访问
- 可以设置timeout 及 ontimeout, 方便设置超时时长和超时后续处理.

目前, 主流浏览器基本上都支持XHR2, 除了IE系列需要IE10及更高版本. 因此IE10以下是不支持XHR2的.

## XDomainRequest

XDomainRequest 对象是IE8,9折腾出来的, 用于支持CORS请求非成熟的解决方案. 以至于IE10中直接移除了它, 并重新回到了 XMLHttpRequest 的怀抱.

## $.ajax

### 参数列表

### 支持promise

$.ajax() 方法返回jqXHR对象(jq1.5起), 如果使用的不是XMLHttpRequest对象时, 如jsonp请求, 返回的jqXHR对象将尽可能模拟原生的xhr. 从jq1.5起, 返回的jqXHR对象实现了promise接口.

### 使用转换器

$.ajax() 的转换器可以将支持的数据类型映射到其它数据类型. 如果需要将自定义数据类型映射到已知的类型. 需要使用 `contents` 选项在响应的 "Content-Type" 和实际数据类型之间添加一个转换函数.

### 事件触发顺序

## Axios

Axios Vs jquery

- Axios支持node, jquery并不支持.
- Axios基于promise语法, jq3.0才开始全面支持.
- Axios短小精悍, 更加适合http场景, jquery大而全, 加载较慢.
- vue作者尤大放弃推荐vue-resource, 转向推荐Axios. 以下为尤大原话.

语法上Axios基本就和promise一样, 在then方法中处理回调, 在catch方法中处理异常. 如下:

```javascript
axios.get("https://api.github.com/users/louiszhai")
  .then(function(response){
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

除了get, 它还支持post, delete, head, put, patch, request请求. 

## Fetch

[Fetch进阶指南](http://louiszhai.github.io/2016/11/02/fetch/) .

## ajax跨域请求

### 什么是CORS

实际上, 浏览器不会拦截不合法的跨域请求, 而是拦截了他们的响应, 因此即使请求不合法, 很多时候, 服务器依然收到了请求.(Chrome和Firefox下https网站不允许发送http异步请求除外)

### 移动端CORS兼容性

当前几乎所有的桌面浏览器(Internet Explorer 8+, Firefox 3.5+, Safari 4+和 Chrome 3+)都可通过名为跨域资源共享的协议支持ajax跨域调用.

 CORS的技术在IOS Safari7.1及Android webview2.3中就早已支持, 即使低版本下webview的canvas在使用跨域的video或图片时会有问题, 也丝毫不影响CORS的在移动端的使用. 

### CORS有关的headers

### CORS请求

CORS请求分为两种, ① 简单请求; ② 非简单请求.

对于简单请求, 浏览器将发送一次http请求, 同时在Request头域中增加 `Origin` 字段, 用来标示请求发起的源, 服务器根据这个源采取不同的响应策略. 若服务器认为该请求合法, 那么需要往返回的 HTTP Response 中添加 `Access-Control-*` 等字段.

对于非简单请求, 比如Method为`POST`且Content-Type值为 `application/json` 的请求或者Method为 `PUT` 或 `DELETE` 的请求, 浏览器将发送两次http请求. 第一次为preflight预检(Method: OPTIONS),主要验证来源是否合法. 值得注意的是:OPTION请求响应头同样需要包含 `Access-Control-*` 字段等. 第二次才是真正的HTTP请求. 所以服务器必须处理OPTIONS应答(通常需要返回20X的状态码, 否则xhr.onerror事件将被触发).

### HTML启用CORS

```html
<meta http-equiv="Access-Control-Allow-Origin" content="*">
```

### 图片启用CORS

通常, 图片允许跨域访问, 也可以在canvas中使用跨域的图片, 但这样做会污染画布, 一旦画布受污染, 将无法读取其数据. 浏览器的这种安全机制规避了未经许可的远程服务器图片被滥用的风险.

因此如需在canvas中使用跨域的图片资源, 请参考如下apache配置片段(来自[HTML5 Boilerplate Apache server configs](https://github.com/h5bp/server-configs-apache/blob/fc379c45f52a09dd41279dbf4e60ae281110a5b0/src/.htaccess#L36-L53)).

## ajax文件上传

1) 为了上传文件, 我们得先选中一个文件. 一个type为file的input框就够了.

```html
<input id="input" type="file">
```

2) 然后用FormData对象包裹📦选中的文件.

```javascript
var input = document.getElementById("input"),
    formData = new FormData();
formData.append("file",input.files[0]);//key可以随意定义,只要后台能理解就行
```

3) 定义上传的URL, 以及方法. github上我搭建了一个 [node-webserver](https://github.com/Louiszhai/node-webserver), 根据需要可以自行克隆下来npm start后便可调试本篇代码.

```javascript
var url = "http://localhost:10108/test",
    method = "POST";
```

### js文件上传

4.1) 封装一个用于发送ajax请求的方法.

```javascript
function ajax(url, method, data){
  var xhr = null;
  if(window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
  } else if (window.ActiveXObject) {
    try {
      xhr = new ActiveXObject("Msxml2.XMLHTTP");
    } catch (e) {
      try {
        xhr = new ActiveXObject("Microsoft.XMLHTTP");
      } catch (e) { 
        alert("您的浏览器暂不支持Ajax!");
      }
    }
  }
  xhr.onerror = function(e){
    console.log(e);
  }
  xhr.open(method, url);
  try{
    setTimeout(function(){
      xhr.send(data);
    });
  }catch(e){
    console.log('error:',e);
  }
  return xhr;
}
```

4.2) 上传文件并绑定事件.

```javascript
var xhr = ajax(url, method, formData);
xhr.upload.onprogress = function(e){
  console.log("upload progress:", e.loaded/e.total*100 + "%");
};
xhr.upload.onload = function(){
  console.log("upload onload.");
};
xhr.onload = function(){
  console.log("onload.");
}
```

### fetch上传

5) fetch只要发送一个post请求, 并且body属性设置为formData即可. 遗憾的是, fetch无法跟踪上传的进度信息.

```javascript
fetch(url, {
  method: method,
  body: formData
  }).then(function(res){
  console.log(res);
  }).catch(function(e){
  console.log(e);
});
```

### jquery文件上传

6) jq的ajax提供了xhr属性用于自定义各种事件.

```javascript
$.ajax({
  type: method,
  url: url,
  data: formData,
  processData : false,
  contentType : false ,//必须false才会自动加上正确的Content-Type
  xhr: function(){
    var xhr = $.ajaxSettings.xhr();//实际上就是return new window.XMLHttpRequest()对象
    if(xhr.upload) {
      xhr.upload.addEventListener("progress", function(e){
        console.log("jq upload progress:", e.loaded/e.total*100 + "%");
      }, false);
      xhr.upload.addEventListener("load", function(){
        console.log("jq upload onload.");
      });
      xhr.addEventListener("load", function(){
        console.log("jq onload.");
      });
      return xhr;
    }
  }
});
```

## ajax请求二进制文件

### FileReader

处理二进制文件主要使用的是H5的FileReader.

## 如何等待多个ajax请求完成

原生js可以使用ES6新增的Promise.

这里先提供一个解析responses的函数.

```javascript
function todo(responses){
  responses.forEach(function(response){
    response.json().then(function(res){
      console.log(res);
    });
  });
}
```

原生js使用 `Promise.all` 方法. 如下:

```javascript
var p1 = fetch("http://localhost:10108/test1"),
    p2 = fetch("http://localhost:10108/test2");
Promise.all([p1, p2]).then(function(responses){
  todo(responses);
  //TODO do somethings
});
//"test1"
//"test2"
```

jquery可以使用$.when方法. 该方法接受一个或多个Deferred对象作为参数, 只有全部成功才调用resolved状态的回调函数, 但只要其中有一个失败，就调用rejected状态的回调函数. 其实, jq的Deferred是基于 Promises/A规范实现, 但并非完全遵循. (传送门: [jQuery 中的 Deferred 和 Promises (2)](http://www.css88.com/archives/4750/comment-page-1) ).

```javascript
var p1 = $.ajax("http://localhost:10108/test1"),
    p2 = $.ajax("http://localhost:10108/test2");
$.when(p1, p2).then(function(res1, res2){
  console.log(res1);//["test1", "success", Object]
  console.log(res2);//["test2", "success", Object]
  //TODO do somethings
});
```

## ajax与history的兼容

如今, H5普及, pjax大行其道. pajax 就是 ajax+history.pushState 组合的一种技术. 使用它便可以无刷新通过浏览器前进和后退来改变页面内容.

IE8,9并不能使用 H5的history. 需要使用垫片 [HTML5 History API expansion for browsers not supporting pushState, replaceState](https://github.com/devote/HTML5-History-API) .

### pjax

pjax简单易用, 仅需要如下三个api:

- history.pushState(obj, title, url) 表示往页面history末尾新增一个历史项(history entry), 此时history.length会+1.
- history.replaceState(obj, title, url) 表示替换当前历史项为新的历史项. 此时history.length保持不变.
- window.onpopstate 仅在浏览器前进和后退时触发(history.go(1), history.back() 及location.href="xxx" 均会触发), 此时可在history.state中拿到刚刚塞进去的state, 即obj对象(其他数据类型亦可).

## ajax缓存处理

js中的http缓存没有开关, 受制于浏览器http缓存策略. 原生xhr请求中, 可通过如下设置关闭缓存.

```javascript
xhr.setRequestHeader("If-Modified-Since","0");
xhr.setRequestHeader("Cache-Control","no-cache");
//或者 URL 参数后加上  "?timestamp=" + new Date().getTime()
```

## ajax的错误处理

前面已经提过, 通常只要是ajax请求收到了http状态码, 便不会进入到错误捕获里.(Chrome中407响应头除外)

实际上, `$.ajax `方法略有区别, jquery的ajax方法还会在类型解析出错时触发error回调. 最常见的便是: dataType设置为json, 但是返回的data并非json格式, 此时 `$.ajax` 的error回调便会触发.

## ajax调试技巧

### 编码问题

## 后端接口测试技巧

### 使用命令测试OPTIONS请求

### postman

## ajax移动端兼容性

移动端的支持性比较弱, 使用需谨慎. 



# 问题

## 什么是AJAX

AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。简短地说，在不重载整个网页的情况下，AJAX 通过后台加载数据，并在网页上进行显示。

## 为什么要用ajax

Ajax应用程序的优势在于：

1. 通过异步模式，提升了用户体验
2. 优化了浏览器和服务器之间的传输，减少不必要的数据往返，减少了带宽占用
3. Ajax引擎在客户端运行，承担了一部分本来由服务器承担的工作，从而减少了大用户量下的服务器负载。

## 介绍XMLhttprequest对象

Ajax的核心是JavaScript对象XmlHttpRequest。该对象在Internet Explorer 5中首次引入，它是一种支持异步请求的技术。简而言之，XmlHttpRequest使您可以使用JavaScript向服务器提出请求并处理响应，而不阻塞用户。通过XMLHttpRequest对象，Web开发人员可以在页面加载以后进行页面的局部更新。

## AJAX请求总共有多少种CALLBACK

Ajax请求总共有八种Callback

- onSuccess
- onFailure
- onUninitialized
- onLoading
- onLoaded
- onInteractive
- onComplete
- onException

## 在浏览器端如何得到服务器端响应的XML数据

XMLHttpRequest对象的responseXMl属性

## XMLHttpRequest对象在IE和Firefox中创建方式

IE中通过new ActiveXObject()得到，Firefox中通过newXMLHttpRequest()得到

## XMLHttpRequest对象的常用方法和属性

**方法**

`open(“method”,”URL”)` 建立对服务器的调用，第一个参数是HTTP请求方式可以为GET，POST或任何服务器所支持的您想调用的方式。第二个参数是请求页面的URL。

`send()`方法，发送具体请求

`abort()`方法，停止当前请求

**熟悉**

`readyState`属性：请求的状态，有5个可取值

- 0=未初始化
- 1=正在加载
- 2=以加载
- 3=交互中
- 4=完成

`responseText`属性：服务器的响应，表示为一个串

`reponseXML` 属性：服务器的响应，表示为XML

`status`属性：服务器的HTTP状态码，200对应ok，400对应error

## AJAX有哪些优点和缺点？

**优点**

1. 最大的一点是页面无刷新，用户的体验非常好。
2. 使用异步方式与服务器通信，具有更加迅速的响应能力。
3. 可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，ajax的原则是“按需取数据”，可以最大程度的减少冗余请求，和响应对服务器造成的负担。
4. 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序。

**缺点**

1. ajax不支持浏览器back按钮。
2. 安全问题 AJAX暴露了与服务器交互的细节。
3. 对搜索引擎的支持比较弱。
4. 破坏了程序的异常机制。
5. 不容易调试。


## jquery ajax实现

```javascript
$.ajax({
    url:'/comm/test1.php',
    type:'POST', //GET
    async:true,    //或false,是否异步
    data:{
        name:'yang',age:25
    },
    timeout:5000,    //超时时间
    dataType:'json',    //返回的数据格式：json/xml/html/script/jsonp/text
    beforeSend:function(xhr){
        console.log(xhr)
        console.log('发送前')
    },
    success:function(data,textStatus,jqXHR){
        console.log(data)
        console.log(textStatus)
        console.log(jqXHR)
    },
    error:function(xhr,textStatus){
        console.log('错误')
        console.log(xhr)
        console.log(textStatus)
    },
    complete:function(){
        console.log('结束')
    }
})
```

## 原生js ajax实现

```javascript
$('#send').click(function(){
    //请求的5个阶段，对应readyState的值
        //0: 未初始化，send方法未调用；
        //1: 正在发送请求，send方法已调用；
        //2: 请求发送完毕，send方法执行完毕；
        //3: 正在解析响应内容；
        //4: 响应内容解析完毕；
    var data = 'name=yang';
    var xhr = new XMLHttpRequest();   //创建一个ajax对象
    xhr.onreadystatechange = function(event){    //对ajax对象进行监听
        if(xhr.readyState == 4){    //4表示解析完毕
            if(xhr.status == 200){    //200为正常返回
                console.log(xhr)
            }
        }
    };
    xhr.open('POST','url',true);    //建立连接，参数一：发送方式，二：请求地址，三：是否异步，true为异步
    xhr.setRequestHeader('Content-type','application/x-www-form-urlencoded');    //可有可无
    xhr.send(data);        //发送
});
```

## 与$.post , $.get ,$.getJSON 有什么不同

\$.post，这个函数其实就是对\$.ajax进行了更进一步的封装，减少了参数，简化了操作，但是运用的范围更小了。\$.post简化了数据提交方式，只能采用POST方式提交。只能是异步访问服务器，不能同步访问，不能进行错误处理。在满足这些情况下，我们可以使用这个函数来方便我们的编程，它的主要几个参数，像method，async等进行了默认设置，我们不可以改变的。

##  jquery ajax中都支持哪些数据类型

> DataType
> 类型：String
> 预期服务器返回的数据类型。如果不指定，jQuery 将自动根据 HTTP 包 MIME 信息来智能判断，比如 XML MIME 类型就被识别为 XML。

可用值: json/xml/html/script/jsonp/text

## json 和 jsonp 的区别

SON是一种数据交换格式，而JSONP是一种s创造出的一种非官方跨域数据交互协议。 



# 参考文章

- [Ajax知识体系大梳理](https://segmentfault.com/a/1190000008697448)
- [经典的20道AJAX面试题](http://blog.csdn.net/chow__zh/article/details/9149811)