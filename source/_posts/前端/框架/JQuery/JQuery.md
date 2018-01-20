---
title: JQuery
date: 2017-12-04 10:35:34
updated: 2017-12-04 10:35:34
tags:
  - JQuery
categories:
  - 框架
toc: true
---
jquery知识点整理。

jquery ajax没整理。

<!-- more -->

# 知识点

## $()

$()函数是JQuery函数的别名

$()函数用于将任何对象包裹成jQuery对象

## jQuery入口函数与Js入口函数的区别

js入口函数指的是：window.onload = function(){};

jQuery入口函数是：$(document).ready()

| window.onload               |      | $(document).ready()                      |
| --------------------------- | ---- | ---------------------------------------- |
| 必须等待网页中所有的内容加载完毕后（包括图片）才能执行 | 执行时机 | 网页中所有DOM结构绘制完毕后就执行，可能DOM元素关联的东西并没有加载完    |
| 不能同时编写多个 ，出现多次会存在事件覆盖的问题。   | 编写个数 | 能同时编写多个                                  |
| 无                           | 简化写法 | $(document).ready(function(){});可以简写成$(function(){}); |
| 很差                          | 兼容性  | 好                                        |

## jQuery选择器

### 基本选择器

- ID选择器：#
- 类选择器： .
- 标签选择器： element

### 层级选择器

- 后代选择器：空格
- 子代选择器：>

### 基本过滤选择器

索引号为index：**:eq(index)**

索引号为奇数：**:odd**

索引号为偶数：**:even**



## jQuery操作

### 筛选

查找指定元素的所有后代元素（子子孙孙）：find(selector)

查找指定元素的直接子元素（亲儿子元素）：children()

查找所有兄弟元素（不包括自己）：siblings()

查找父元素（亲的）：parent()

查找指定元素的第index个元素，index是索引号，从0开始：eq(index)

### 选择网页元素

```javascript
$('a:first')//选择网页中第一个a元素  
$('tr:odd')//选择表格的奇数行  
$('#myForm :input')//选择表单中的input元素  
$('div:visible') //选择可见的div元素  
$('div:gt(2)')//选择所有的div元素，除了前三个  
$('div:animated')//选择当前处于动画状态的div元素  
```

### 改变结果集

```javascript
$('div').has('p'); //选择包含p元素的div元素  
$('div').not('.myClass'); //选择class不等于myClass的div元素  
$('div').filter('.myClass'); //选择class等于myClass的div元素  
$('div').first(); //选择第1个div元素  
$('div').eq(5); //选择第6个div元素  
$('div').next('p'); //选择div元素后面的第一个p元素  
$('div').parent(); //选择div元素的父元素  
$('div').closest('form'); //选择离div最近的那个form父元素  
$('div').children(); //选择div的所有子元素  
$('div').siblings(); //选择div的同级元素 
```

### 链式操作

.end()：回到最近的一个"破坏性"操作之前。如果之前没有破坏性操作，则返回一个空集。所谓的"破坏性"就是指任何改变所匹配的jQuery元素的操作。

### 元素的操作：取值和赋值

常见的取值和赋值函数如下：

1. html() 返回或设置被选元素的内容 (inner HTML)    
2. text() 取出或设置text内容      
3. **attr()** 取出或设置某个属性的值    
4. width() 取出或设置某个元素的宽度    
5. height() 取出或设置某个元素的高度  
6. val() 取出或设置html内容 取出某个表单元素的值  
7. css() 设置css样式

需要注意的是，如果结果集包含多个元素，那么赋值的时候，将对其中所有的元素赋值；取值的时候，则是只取出第一个元素的值（.text()例外，它取出所有元素的text内容）。

### 元素的操作：移动

1. .insertAfter()和.after()：在现存元素的外部，从后面插入元素  
2. .insertBefore()和.before()：在现存元素的外部，从前面插入元素  
3. .appendTo()和.append()：在现存元素的内部，从后面插入元素  
4. .prependTo()和.prepend()  ：在现存元素的内部，从前面插入元素

### 元素的操作：复制、删除和创建

复制元素使用.clone()

删除元素使用.remove()和.detach()。两者的区别在于，**前者不保留被删除元素的事件，后者保留，有利于重新插入文档时使用。**

清空元素内容（但是不删除该元素）使用.empty()。

### 工具方法

 $.trim() 去除字符串两端的空格。
 $.each() 遍历一个数组或对象。  
 $.inArray() 返回一个值在数组中的索引位置。如果该值不在数组中，则返回-1。  
 $.grep() 返回数组中符合某种标准的元素。   
 $.extend() 将多个对象，合并到第一个对象。   
 $.makeArray() 将对象转化为数组。  
 $.type() 判断对象的类别（函数对象、日期对象、数组对象、正则对象等等）。  
 $.isArray() 判断某个参数是否为数组。  
 $.isEmptyObject() 判断某个对象是否为空（不含有任何属性）。  
 $.isFunction() 判断某个参数是否为函数。  
 $.isPlainObject() 判断某个参数是否为用"{}"或"new Object"建立的对象。  
 $.support() 判断浏览器是否支持某个特性。 

### 事件操作

 .blur() 表单元素失去焦点。  
 .change() 表单元素的值发生变化  
 .click() 鼠标单击  
 .dblclick() 鼠标双击  
 .focus() 表单元素获得焦点  
 .focusin() 子元素获得焦点  
 .focusout() 子元素失去焦点  
 .hover() 同时为mouseenter和mouseleave事件指定处理函数  
 .keydown() 按下键盘（长时间按键，只返回一个事件）  
 .keypress() 按下键盘（长时间按键，将返回多个事件）  
 .keyup() 松开键盘  
 .load() 元素加载完毕  
 .mousedown() 按下鼠标  
 .mouseenter() 鼠标进入（进入子元素不触发）  
 .mouseleave() 鼠标离开（离开子元素不触发）  
 .mousemove() 鼠标在元素内部移动  
 .mouseout() 鼠标离开（离开子元素也触发）  
 .mouseover() 鼠标进入（进入子元素也触发）  
 .mouseup() 松开鼠标  
 .ready() DOM加载完成  
 .resize() 浏览器窗口的大小发生改变  
 .scroll() 滚动条的位置发生变化  
 .select() 用户选中文本框中的内容  
 .submit() 用户递交表单  
 .toggle() 根据鼠标点击的次数，依次运行多个函数  
 .unload()  用户离开页面  

**特别的**

- .bind()：以上这些事件在jQuery内部，都是.bind()的便捷方式。使用.bind()可以更灵活地控制事件
- .one()：只想让事件运行一次
- .unbind()：解除事件绑定

所有的事件处理函数，都可以接受一个事件对象(event object)作为参数，这个事件对象有一些很有用的属性和方法：

event.pageX 事件发生时，鼠标距离网页左上角的水平距离 　　
event.pageY 事件发生时，鼠标距离网页左上角的垂直距离
event.type 事件的类型（比如click） 　　
event.which 按下了哪一个键 　　
event.data在事件对象上绑定数据，然后传入事件处理函数 　　
event.target 事件针对的网页元素
event.preventDefault() 阻止事件的默认行为（比如点击链接，会自动打开新页面）
event.stopPropagation() 停止事件向上层元素冒泡

有两种方法，可以自动触发一个事件。一种是直接使用事件函数，另一种是使用.trigger()或.triggerHandler()。

```javascript
$('a').click();
$('a').trigger('click');
```

### 特殊效果

.fadeIn() 淡入
.fadeOut() 淡出
.fadeTo() 调整透明度
.hide() 隐藏元素
.show() 显示元素
.slideDown() 向下展开
.slideUp() 向上卷起
.slideToggle() 依次展开或卷起某个元素
.toggle() 依次展示或隐藏某个元素

除了.show()和.hide()，所有其他特效的默认执行时间都是400ms(毫秒)，但是你可以改变这个设置。

更复杂的特效，可以用.animate()自定义。

.stop()和.delay()用来停止或延缓特效的执行。

$.fx.off如果设置为true，则关闭所有网页特效。

几个常见的筛选选择器：

filter()：筛选出与指定表达式匹配的元素集合。这个方法用于缩小匹配的范围。用逗号分隔多个表达式

```javascript
//保留子元素中不含有ol的元素。
//<p><ol><li>Hello</li></ol></p><p>How are you?</p>
$("p").filter(function(index) {
 return $("ol", this).length == 0;
});
//<p>How are you?</p>
```

silce()：选取一个匹配的子集

```javascript
//选择第一个p元素
//<p>Hello</p><p>cruel</p><p>World</p>
$("p").slice(0, 1).wrapInner("<b></b>");
//<p><b>Hello</b></p> 
```



# 问题

## Case1.jQuery对象和DOM对象的相互转换

```javascript
var btn = document.getElementById(“btnShow”);
var $btn = $(“#btnShow”); // $btn就是一个jQuery对象
             
//DOM对象转换成jQuery对象
var $btn1 = $(btn);

//jQuery对象转换成DOM对象
var btn1 = $btn[0]; 
var btn2 = $btn.get(0);
```



## Case2.如何找到所有 HTML select 标签的选中项？

```javascript
$('[name=NameOfSelectedTag] :selected')
```



## Case3. jQuery 里的 each() 是什么函数？你是如何使用它的？

each() 函数就像是 Java 里的一个 Iterator，它允许你遍历一个元素集合。你可以传一个函数给 each() 方法，被调用的 jQuery 对象会在其每个元素上执行传入的函数。

```javascript
$('[name=NameOfSelectedTag] :selected').each(function(selected) {
    alert($(selected).text());
});
```



## Case4.$(this) 和 this 关键字在 jQuery 中有何不同？

$(this) 返回一个 jQuery 对象，你可以对它调用多个 jQuery 方法

而 this 代表当前元素，它是 JavaScript 关键词中的一个，表示上下文中的当前 DOM 元素。



## Case5.你如何使用jQuery来提取一个HTML 标记的属性 例如. 链接的href?

```javascript
$('a').each(function(){
   alert($(this).attr('href'));
});
```



## Case6.jQuery中 detach() 和 remove() 方法的区别是什么?

尽管 detach() 和 remove() 方法都被用来移除一个DOM元素, 两者之间的主要不同在于 detach() 会保持对过去被解除元素的跟踪, 因此它可以被取消解除, 而 remove() 方法则会保持过去被移除对象的引用. 



## Case7.你如何利用jQuery来向一个元素中添加和移除CSS类? 

利用 addClass() 和 removeClass() 这两个 jQuery 方法。



## Case8.使用 CDN 加载 jQuery 库的主要优势是什么 ?

- 节省服务器带宽
- 更快的下载速度
- 只需要下载一次。如果浏览器已经从同一个CDN下载类相同的 jQuery 版本, 那么它就不会再去下载它一次.



## Case9.jQuery.get() 和 jQuery.ajax() 方法之间的区别是什么?

ajax() 方法更强大，更具可配置性, 让你可以指定等待多久，以及如何处理错误。get() 方法是一个只获取一些数据的专门化方法。



### Case10. 要是在一个 jQuery 事件处理程序里返回了 false 会怎样？

通常用于阻止事件向上冒泡。



### Case11. 哪种方式更高效：document.getElementbyId("myId") 还是 $("#myId")？

第一种，因为它直接调用了 JavaScript 引擎。



## Case12.当CDN上的jQuery文件不可用时，该怎么办？

```javascript
<script type="text/javascript" language="Javascript" src="http://ajax.aspnetcdn.com/ajax/jquery/jquery-1.4.1.min.js "></script>

<script type='text/javascript'>//<![CDATA[
if (typeof jQuery == 'undefined') {
document.write(unescape("%3Cscript src='/Script/jquery-1.4.1.min.js' type='text/javascript' %3E%3C/script%3E"));
}//]]>
</script>
```



## Case13.jQuery中的Delegate()函数有什么作用？

delegate()会在以下两个情况下使用到：

1. 如果你有一个父元素，需要给其下的子元素添加事件，这时你可以使用delegate()了，代码如下：

```javascript
$("ul").delegate("li", "click", function(){
	$(this).hide();
});
```

2、当元素在当前页面中不可用时，可以使用delegate()



## Case14.jQuery编码和解码URL？

encodeURIComponent(url) and decodeURIComponent(url)



### Case15. 如何用jQuery禁用浏览器的前进后退按钮？

```javascript
<script type="text/javascript" language="javascript">
$(document).ready(function() {
     window.history.forward(1);	// 原理就是产生一个“前进”的动作，以抵消后退功能
     //OR
     window.history.forward(-1);
});
</script>
```



# 参考文章

- [jQuery基础知识点总结(必看)](http://www.jb51.net/article/85471.htm)
- [jquery学习总结（超级详细）](http://www.jb51.net/article/54745.htm)
- [最常见的 20 个 jQuery 面试问题及答案](http://www.cnblogs.com/mehjb/p/6095230.html)
- [jQuery经典面试题及答案精选](http://www.cnblogs.com/vanone/p/5257157.html)