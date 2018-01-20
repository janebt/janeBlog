---
title: 面试重点之AJAX
date: 2018-01-01 11:33:34
updated: 2018-01-01 15:25:00
tags:
  - 面试攻略
  - AJAX
  - JavaScript
categories:
  - 面试
toc: true
---
AJAX问题在面试中必问。但其实又很简单。本文介绍一些手写代码。

面试的AJAX部分，需要掌握：

> - 能手写，知道一些重要参数

TODO：

> - 补充fetch等并对比

<!-- more -->

# 面试重点之AJAX

## 什么是AJAX

AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。简短地说，在不重载整个网页的情况下，AJAX 通过后台加载数据，并在网页上进行显示。

## XMLHttpRequest对象的常用方法和属性

**方法**

`open(“method”,”URL”)` 建立对服务器的调用，第一个参数是HTTP请求方式可以为GET，POST或任何服务器所支持的您想调用的方式。第二个参数是请求页面的URL。

`send()`方法，发送具体请求

`abort()`方法，停止当前请求

**属性**

`readyState`属性：请求的状态，有5个可取值

- 0=未初始化
- 1=正在加载
- 2=已加载
- 3=交互中
- 4=完成

`responseText`属性：服务器的响应，表示为一个串

`reponseXML` 属性：服务器的响应，表示为XML

`status`属性：服务器的HTTP状态码，200对应ok，400对应error

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