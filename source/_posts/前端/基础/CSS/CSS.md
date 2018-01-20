---
title: CSS
date: 2017-01-02 17:57:34
updated: 2017-01-02 17:57:34
tags:
  - CSS
categories:
  - 基础
toc: true
---
整理CSS知识点。

<!-- more -->
# 知识点

## 布局

### 流动布局

需了解实现块居中常见的几种方式

### 浮动布局

- 仍处于标准文档流中。
- 没设置宽度值，设置了浮动属性，元素的宽度随内容的变化而变化。
- 设置为浮动属性，会对紧邻之后的元素造成影响，紧邻之后的元素会紧挨着该元素显示。
- 当父元素包含块缩成一条时，用clear:both方法无效，它一般用于紧邻后面的元素的清除浮动，要用overflow属性。
- 清除浮动的方法有两种:
  - 使用clear属性 - clear: both; 这种方法需要在浮动元素后面添加一个空的节点。兼容IE6需要添加zoom:1;
  - 同时设置width:100%(或固定宽度) + overflow:hidden。浮动元素得有一个父元素。
  - 使用after伪类，这种方式公认最好
- 浮动布局可实现横向多列布局；文字环绕效果。

### 绝对定位布局

#### static

- static是默认值
- 它表示块保留在原本应该在的位置，不会重新定位

#### relative

- 相对于自身原有位置进行偏移
- 仍处于标准文档流中
- 随即拥有偏移属性和z-index属性

#### fixed

- 一个固定定位（position属性的值为fixed）元素会相对于视窗来定位,这意味着即便页面滚动，它还是会停留在相同的位置。
- 一个固定定位元素不会保留它原本在页面应有的空隙。

#### absolute

- 相对于最近的已定位的祖先元素, 有已定位(指position不是static的元素)祖先元素, 以最近的祖先元素为参考标准。如果无已定位祖先元素, 以body元素为偏移参照基准, 并且它会随着页面滚动而移动。
- 完全脱离了标准文档流。
- 随即拥有偏移属性和z-index属性。

### Flex布局

Flexbox又叫弹性盒模型。它可以简单使用一个元素居中（包括水平垂直居中），可以让扩大和收缩元素来填充容器的可利用空间，可以改变源码顺序独立布局，以及还有其他的一些功能。合理使用它能够大大减少布局方面的工作。此外在移动端使用flex也比较常见。

#### 使用

- display:flex;
- justify-content (水平对齐方式)
- align-items (垂直对齐方式)

使用请参考:

- [CSS flex完全指南](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [flex历险记](http://gold.xitu.io/entry/57675f8f80dda4005f9bbe0c)
- [Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)
- [3分钟看懂flex布局](http://www.cnblogs.com/lixuemin/p/6110434.html)

### 响应式布局

响应式布局是指，网页可以自动识别设备屏幕宽度，根据不同的宽度采用不同的CSS的样式，从而达到兼容各种设备的效果。

响应式布局使用媒体查询(CSS3 Media Queries), 根据不同屏幕分辨率采用不同CSS规则, 使用方式如下:

```css
@media screen and (max-width:1024px) {
  /* 视窗宽度小于1024px时 */
  ....
}
```

#### Bootstrap grid系统的实现

它提供了一套响应式，移动优先的流式栅格系统(grid system)，将屏幕分成12列来实现响应式的。它的实现原理非常简单，Media Query加上float布局，如果想了解实现细节，请参考我另外一篇博客[Boostrap网格系统](https://segmentfault.com/a/1190000005771076)。



## 盒子模型

每一个盒子有四条边界：外边距边界margin, 边框边界border, 内边距边界padding与内容边界content。

**IE盒模型和W3C盒模型区别**：

- 在W3C模型中: 总宽度 = margin-left + border-left + padding-left + width + padding-right + border-right + margin-right
- 在IE模型中: 总宽度 = margin-left + width + margin-right

在CSS3中引入了**box-sizing属性**, 它可以允许改变默认的CSS盒模型对元素宽高的计算方式.共包括两个选项：

- **content-box：标准盒模型，CSS定义的宽高只包含content的宽高**
- **border-box：IE盒模型，CSS定义的宽高包括了content，padding和border**




## 定位机制

css有三种基本定位机制: 标准文档流, 浮动和绝对定位。

### 标准文档流

- 从左到右，从上向下，输出文档内容
- 由块级元素和行级元素组成

#### 行内元素

- 行内元素不能设置宽高，默认没高度和宽度，宽度由其内容决定。
- 行内元素虽然不能设置宽高，但是设置成绝对定位后，可以设置宽高。
- 行内元素：`a font(em strong i) img span`

#### 块元素

- 块元素默认没高度，可以设置宽高，默认占据一行。
- **一行只有一个块元素**
- **空的块元素将在布局中消失**
- 块元素：`div p ul li table form html5(header section footer)`

### 浮动

- 设置为浮动的元素将会往左(float:left)或者往右(float:right)漂移, 直到遇到阻挡 - 其他浮动元素或者父元素的边框。浮动元素不在标准文档流中占据空间,但会对其后的浮动元素造成影响。

### 绝对定位

- 设置为绝对定位的元素(posistion:absolute)将从标准文档流中删除，其所占据的标准流空间也不存在。然后通过top,left,right,bottom属性对其相对父元素进行定位。




## 选择器优先级及使用

### css选择器分类　

1. 标签选择器(如：body,div,p,ul,li)
2. 类选择器(如：class="head",class="head_logo")
3. ID选择器(如：id="name",id="name_txt")
4. 全局选择器(如：*号)
5. 组合选择器(如：.head .head_logo,注意两选择器用空格键分开)
6. 后代选择器 (如：#head .nav ul li 从父集到子孙集的选择器)
7. 群组选择器 div,span,img {color:Red} 即具有相同样式的标签分组显示
8. 继承选择器(如：div p,注意两选择器用空格键分开)
9. 伪类选择器(如：就是链接样式,a元素的伪类，4种不同的状态：link、visited、active、hover。)
10. 字符串匹配的属性选择符(^ $ *三种，分别对应开始、结尾、包含)
11. 子选择器 (如：div>p ,带大于号>)
12. CSS 相邻兄弟选择器器 (如：h1+p,带加号+)

### 选择器优先级

#### 不同级别

1. 在属性后面使用 !important 会覆盖页面内任何位置定义的元素样式。
2. 作为style属性写在元素内的样式
3. id选择器
4. 类选择器
5. 标签选择器
6. 通配符选择器
7. 浏览器自定义或继承

总结排序：!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性

#### 同一级别

同一级别中后写的会覆盖先写的样式

#### 多个级别

计算方法（权值实际并不是按十进制，用数字表示只是说明思想，一万个class也不如一个id权值高）

- 内联样式表的权值为 1000
- ID 选择器的权值为 100
- Class 类选择器的权值为 10
- HTML 标签选择器的权值为 1

css选择器使用强烈建议采用低权重原则，利于充分发挥css的继承性，复用性，模块化、组件化。

### CSS选择器的解析原则

郑重的声明选择器定位DOM元素是从右往左的方向，这样的好处是尽早的过滤掉一些无关的样式规则和元素 。[为什么CSS选择器是从右往左解析 ？？？](http://blog.csdn.net/jinboker/article/details/52126021)

简要概括：

- 遍历节点树小很多
- 匹配的是dom元素的父节点，避免HTML、CSS没下载完需要等待



## CSS3新特性及应用

### 新的元素选择器

E:nth-last-child(n), E:nth-of-type(n), E:nth-last-of-type(n), E:last-child, E:first-of-type, E:only-child, E:only-of-type, E:empty, E:checked, E:enabled, E:disabled, E::selection, E:not(s)

### @font-face

confont是指使用字体文件取代图片文件，来展示图标和一些特殊字体等元素。它使用CSS3中的@font-face属性，它允许加载自定义字体样式，而且它还能够加载服务器端的字体文件，让客户端显示客户端所没有安装的字体。

它有很多优势: 首先它的体积要比图片小得多; 特定的属性(颜色,大小,透明)等修改起来就像是操作字体一样简单;iconfont具有矢量性, 放大缩小不会失真;

### border-radius

又称圆角属性，通常使用该属性将图片圆角化，如头像。

```css
border-radius: 50%;
```

border-radius另外一个常用的手段是CSS动画。

### word-wrap & text-overflow

word-wrap属性用来指出浏览器在单词内进行断句，防止字符串太长而找不到它的自然断句点时产生的溢出。

```css
word-wrap: break-word;
```

text-overflow用于文本溢出:

```css
text-overflow: ellipsis; //对溢出部分加上...
```

### background

主要是以下三个属性:

- background-clip 规定背景的绘制区域, 取值为border-box | padding-box | content-box | no-clip
- background-origin 规定背景的定位区域, 取值为border | padding | content
- background-size 规定背景图片的尺寸, 取值为[<length> | <percentage> | auto]{1,2} | cotain




## 兼容性

### 通用浏览器

#### 不同浏览器的标签默认的外补丁和内补丁不同

**问题症状**：随便写几个标签，不加样式控制的情况下，各自的margin 和padding差异较大。

**解决方案**：CSS里加 *{margin:0;padding:0;},**但不推荐，而且它也并不完善。**

淘宝的样式初始化：

```css
body, h1, h2, h3, h4, h5, h6, hr, p, blockquote, dl, dt, dd, ul, ol, li, pre, form, fieldset, legend, button, input, textarea, th, td { margin:0; padding:0; }
    body, button, input, select, textarea { font:12px/1.5tahoma, arial, \5b8b\4f53; }
    h1, h2, h3, h4, h5, h6{ font-size:100%; }
    address, cite, dfn, em, var { font-style:normal; }
    code, kbd, pre, samp { font-family:couriernew, courier, monospace; }
    small{ font-size:12px; }
    ul, ol { list-style:none; }
    a { text-decoration:none; }
    a:hover { text-decoration:underline; }
    sup { vertical-align:text-top; }
    sub{ vertical-align:text-bottom; }
    legend { color:#000; }
    fieldset, img { border:0; }
    button, input, select, textarea { font-size:100%; }
    table { border-collapse:collapse; border-spacing:0; }
```

#### 上下margin重合问题

**问题症状**：相邻的两个div margin-left margin-right 不会重合，但相邻的margin-top margin-bottom会重合。有两种情况：

1. 兄弟级的块之间，margin这个属性上下边距，经常会发生重叠的情况，以数值大的为准，而不会相加。
2. 父子级的块之间，子级的上下margin会与父级上下margin重叠，以数值大的为准，而不会相加。

**解决方案**：

第一种情况：

1. float浮动
2. inline-block

第二种情况：

1. 父级加overflow:hidden


2. 父级加加padding 
3. 父级加加border 
4. 子级加position:absolute

#### img默认有间距

**问题症状**：几个img标签放在一起的时候，有些浏览器会有默认的间距，加了问题一中提到的通配符也不起作用。

**解决方案**：使用float属性为img布局

备注：因为img标签是行内属性标签，所以只要不超出容器宽度，img标签都会排在一行里，但是部分浏览器的img标签之间会有个间距。

#### 标签最低高度设置min-height不兼容

**问题症状**：因为min-height本身就是一个不兼容的CSS属性，所以设置min-height时不能很好的被各个浏览器兼容

**解决方案**：如果我们要设置一个标签的最小高度200px，需要进行的设置为：

```css
.box{min-height:200px; height:auto !important; height:200px; overflow:visible;}
```

备注：在B/S系统前端开时，有很多情况下我们又这种需求。当内容小于一个值（如300px）时。容器的高度为300px；当内容高度大于这个值时，容器高度被撑高，而不是出现滚动条。这时候我们就会面临这个兼容性问题。

#### 超链接访问过后样式就混乱了，hover样式不出现

**问题症状**：主要是其CSS属性的排序问题。

**解决方案**：一般来说，最好按照这个顺序：L-V-H-A

```css
a:link{}  a:visited{}  a:hover{}  a:active{}
```

#### chrome默认会将小于12px的文本强制按照12px来解析

**解决方案**：给其添加属性：

```css
-webkit-text-size-adjust: none; 
```

#### 鼠标的手势问题

**问题症状**：FireFox的cursor属性不支持hand，但是支持pointer，IE两个都支持；

**解决方案**：为了兼容都用pointer

#### 消除ul、ol等列表的缩进

**解决方案**：样式应写成:

```css
list-style:none;margin:0px;padding:0px; 
```

其中margin属性对IE有效，padding属性对FireFox有效

#### 透明度问题

**解决方案**：

```css
opacity: 0.6 ; 	//一般 
filter: alpha(opacity=60)	//IE
filter:progid:DXImageTransform.Microsoft.Alpha(style=0,opacity=60);		//IE6
```

### IE6常见BUG

#### IE6双倍边距bug(1)

**问题症状**：块属性标签添加了浮动float之后，若在浮动方向上也有margin值，则margin值会加倍。其实这种问题主要就是会把某些元素挤到了第二行

**解决方案**：

- 给float元素添加display：inline
- 就是hack处理了，对IE6进行 _margin-left:5px;(原先为10px)

#### IE6双倍边距bug(2)

**问题症状**：行内属性标签，为了设置宽高，我们经常就会设置成display：block; 后采用float布局，又有横行的margin的情况这样一来就产生上述的问题。

**解决方案**：在display:block;后面加入display:inline;display:table;

#### IE6中3像素问题

**问题症状**：当元素使用float浮动后，元素与相邻的元素之间会产生3px的间隙。诡异的是如果右侧的容器没设置高度时3px的间隙在相邻容器的内部，当设定高度后又跑到容器的相反侧了。

**解决方案**：需要使布局在同一行的元素都加上float浮动。

#### IE6中奇数宽高的BUG

**问题症状**：IE6中奇数的高宽显示大小与偶数高宽显示大小存在一定的不同。其中要问题是出在奇数高宽上。

**解决方案**：需要尽量将外部定位的div高宽写成偶数即可。

#### IE6和FF中图片链接的下方有间隙

**问题症状**：IE6中图片的下方会存在一定的间隙，尤其在图片垂直挨着图片的时候，即可看到这样的间隙。

**解决方案**：

1. 需要将img标签定义为display:block 
2. 定义vertical-align对应的属性。
3. 为img对应的样式写入font-size:0

#### IE6下空元素的高度BUG

**问题症状**：如果一个元素中没有任何内容，当在样式中为这个元素设置了0-19px之间的高度时。此元素的高度始终为19px。

**解决方案**：

1. 在元素的css中加入：overflow:hidden
2. 在元素中插入html注释：<!– >
3. 在元素中插入html的空白符：&nbsp;
4. 在元素的css中加入：font-size:0

#### 重复文字的BUG

**问题症状**：在某些比较复杂的排版中，有时候浮动元素的最后一些字符会出现在clear清除元素的下面。

**解决方案**：

1. 确保元素都带有display:inline
2. 在最后一个元素上使用“margin-right:-3px
3. 为浮动元素的最后一个条目加上条件注释，<!–[if !IE]>xxx<![endif]–>
4. 在容器的最后元素使用一个空白的div，为这个div指定不超过容器的宽度。

#### IE6中 z-index失效

**问题症状**：具体BUG为，元素的父级元素设置的z-index为1，那么其子级元素再设置z-index时会失效，其层级会继承父级元素的设置，造成某些层级调整上的BUG

原因：z-index起作用有个小小前提，就是元素的position属性要 是relative，absolute或是fixed。

**解决方案**：

1. position:relative改为position:absolute；
2. 去除浮动；
3. 浮动元素添加position属性（如relative，absolute等）。

**IE6结语：实际上中，很多BUG的解决方法都可以使用display:inline、font-size:0、float解决。因此我们在书写代码时要记住，一旦使用了float浮动，就为元素增加一个display:inline样式，可以有效的避免浮动造成的样式错乱问题。使用空DIV时，为了避免其高度影响布局美观，也可以为其加上font-size:0 这样就很容易避免一些兼容上的问题。**

#### png24位的图片在IE6下面会出现背景

**解决方案**：使用png8格式的

#### IE6下在使用margin:0 auto;无法使其居中       

**解决方案**：为其父容器设置`text-align:center;`

#### IE6/7标签高度无法小于10px

**问题症状**：IE6、7和遨游里这个标签的高度不受控制，超出自己设置的高度

**解决方案**：

- 给超出高度的标签设置overflow:hidden;
- 或者设置行高line-height 小于你设置的高度
- 或设置fontsize大小为高度大小

备注：这种情况一般出现在我们设置小圆角背景的标签里。出现这个问题的原因是IE8之前的浏览器都会给标签一个最小默认的行高的高度。即使你的标签是空的，这个标签的高度还是会达到默认的行高。



## Hack

我们为了让页面形成统一的效果，要针对不同的浏览器或不同版本写出对应可解析的CSS样式，所以我们就把这个针对不同浏览器/版本而写CSS的过程叫做 CSS hack.

CSS hack主要有三种：IE条件注释法、CSS属性前缀法、选择器前缀法。

### IE条件注释法

在正常代码之外添加判别IE浏览器或对应版本的条件注释，符合条件的浏览器或者版本号才回执行里边的代码。

```html
<!-- [if IE]>
   你想要执行的代码 
<![endif]-->

<!-- [if lt IE 8]>
   你想要执行的代码 
<![endif]-->

<!-- [if ! IE 8]>
   你想要执行的代码 
<![endif]-->
```

### CSS属性前缀法

给css的属性添加前缀。比如 \* 可以被IE6/IE7识别，但 _ 只能被IE6识别，IE6-IE10都可以识别 "\9"，IE6不能识别!important ,FireFox不能识别 * _  \9

```css
//可以先使用“\9"标记，将IE分离出来，再用”*"分离出IE6/IE7，最后可以用“_”分离出IE6
.type{
        color: #111; /* all */
        color: #222\9; /* IE */
        *color: #333; /* IE6/IE7 */
        _color: #444; /* IE6 */
        }

//所以可以按着优先级就能给特定的版本捎上特定颜色
```

说明：在标准模式中

- “-″减号是IE6专有的hack
- “\9″ IE6/IE7/IE8/IE9/IE10都生效
- “\0″ IE8/IE9/IE10都生效，是IE8/9/10的hack
- “\9\0″ 只对IE9/IE10生效，是IE9/10的hack

### 选择器前缀法

```css
*div{color:red;}  			//IE6可识别 
*+div{color:red;}			//IE7可识别 
@media screen\9{...}		//只对IE6/7生效
@media \0screen {body { background: red; }}				//只对IE8有效
@media \0screen\,screen\9{body { background: blue; }}	//只对IE6/7/8有效
@media screen\0 {body { background: green; }} 			//只对IE8/9/10有效
@media screen and (min-width:0\0) {body { background: gray; }} 	//只对IE9/10有效
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {body { background: orange; }} 		//只对IE10有效 等等
```



## 移动端适应



# 问题

## 兼容性

### 谈谈兼容性的问题

自己遇到的最严重的兼容性case，需求是什么，一开始怎么做的，怎么发现有问题，怎么找到问题的原因，有哪些解决方案，最后选了哪一种，为什么选这种方式，这个过程里学到了什么，再做一次类似case会有什么改变，将来怎么避免等等。

## 布局

### CSS中使用列布局是什么？

```css
<style>
//我们需要把text划分为多少列
.magazine
{
  -moz-column-count:3; /* Firefox */
  -webkit-column-count:3; /* Safari and Chrome */
  column-count:3;

  //两列之间我们想要多少差距
  -moz-column-gap:40px; /* Firefox */
  -webkit-column-gap:40px; /* Safari and Chrome */
  column-gap:20px;

  //你想在这些列之间画一条线么？如果是，那么多厚呢？
  -moz-column-rule:4px outset #ff00ff; /* Firefox */
  -webkit-column-rule:4px outset #ff00ff; /* Safari and Chrome */
  column-rule:6px outset #ff00ff;
}
</style>
```

### 清除浮动

```css
.clearfix:after {
  content:"."; 
  display:block; 
  height:0; 
  visibility:hidden; 
  clear:both; }
.clearfix { *zoom:1; }
```

## 垂直水平居中实现

### 水平居中

#### 行内或类行内元素

在块级父容器中让行内元素居中，只需使用 `text-align: center;`

#### 块级元素

设置 `margin-left` 和 `margin-right` 为 `auto`（前提是已经为元素设置了适当的 `width` 宽度，否则块级元素的宽度会被拉伸为父级容器的宽度）

#### `float`

float 属性是不能简单实现元素居中。

可以使用伪元素充当占位符

#### 多个块级元素

修改`display` 值。使用了 `inline-block` 的显示方式，另一个使用了 `flexbox` 的显示方式

### 垂直居中

#### 行内或类行内元素

- 等值的 `padding-top` 和 `padding-bottom`
- `line-height` 和 `center` 相等

#### 多行

- 等值的 `padding-top` 和 `padding-bottom`
- `table-cell` 的父级容器，然后使用 [`vertical-align`](http://www.w3cplus.com/css/what-is-vertical-align.html) 属性
- `flexbox` 实现垂直居中，对于父级容器为 `display: flex` 的元素来说，它的每一个子元素都是垂直居中
- 幽灵元素，在垂直居中的元素上添加伪元素，设置伪元素的高等于父级容器的高，然后为文本添加 `vertical-align: middle;` 样式

#### 块级元素

##### 已知元素的高度

```css
position: absolute;
top: 50%;
height: 100px;
margin-top: -50px;
```

##### 未知元素的高度

需要先将元素定位到容器的中心位置，然后使用 `transform` 的 `translate` 属性，将元素的中心和父容器的中心重合，从而实现垂直居中

```css
position: absolute; 
top: 50%; 
transform: translateY(-50%);
```

#### flexbox

```css
display: flex; 
flex-direction: column; 
justify-content: center; 
```

### 水平且垂直居中

#### 宽高固定元素

设定父级容器为相对定位的容器，设定子元素绝对定位的位置 position: absolute; top: 50%; left: 50%，最后使用负向 margin 实现水平和垂直居中，margin 的值为宽高（具体的宽高需要根据实际情况计算 padding）的一半。

```css
width: 300px; 
height: 100px;
padding: 20px; 
position: absolute; 
top: 50%; 
left: 50%; 
margin: -70px 0 0 -170px;
```

#### 宽高不固定元素

设定父级容器为相对定位的容器，设定子元素绝对定位的位置 `position: absolute; top: 50%; left: 50%`。不同的是，接下来需要使用 `transform: translate(-50%, -50%);` 实现垂直居中

```css
position: absolute; 
top: 50%; 
left: 50%; 
transform: translate(-50%, -50%);
```

使用 `transform` 有一个缺陷，就是当计算结果含有小数时（比如 `0.5`），会让整个元素看起来是模糊的，一种解决方案就是为父级元素设置 `transform-style: preserve-3d;` 样式

#### flexbox

```css
display: flex; 
justify-content: center; 
align-items: center;
```

## CSS3

### CSS3中的文本效果

阴影文本效果

```css
.specialtext
{
	text-shadow: 5px 5px 5px #FF0000;
}
```

文字包装效果：允许长单词换行到下一行

```css
<style>
.breakword
	{word-wrap:break-word;}
</style>
```

### 动画实现

```css
div
{
  animation:mymove 5s infinite;
  -webkit-animation:mymove 5s infinite; /* Safari 和 Chrome */
}
```



# 参考文章

- [前端面试之CSS总结(上)](https://segmentfault.com/a/1190000006890725)
- [前端总结·基础篇·CSS（一）布局](http://www.cnblogs.com/bergwhite/p/6417800.html)
- [关于margin上下重叠的问题](http://blog.csdn.net/icesschen/article/details/52443364)
- [CSS常见兼容性问题总结](http://www.cnblogs.com/guhui1994/p/5906268.html)

