---
title: 快速搭建Hexo
date: 2016-10-01 11:33:34
updated: 2017-10-1 15:25:00
tags:
  - Hexo
categories:
  - 工具
toc: true
thumbnail: 
---
这篇文章主要是提醒自己半年更一次博客忘了要怎么做。当然如果想**迅速**建站，可是可以参考的。

<!-- more -->

# 搭建hexo

首先需要搭建hexo，包括：

- 安装node.js
- 安装Hexo
- 后期部署
- hexo配置

## 安装Hexo

```shell
npm install -g hexo-cli --no-optional
npm install hexo-deployer-git --save
cd ~/Document/hexo
hexo init
npm install
```
## 后期部署

```
hexo new "blogName"		//添加文章
hexo new page "pageName" #新建页面
hexo clean
hexo algolia	//algolia搜索
hexo 

hexo generate	//生成静态页面
hexo sever		//本地启动
hexo deploy		//同步到github
```

## hexo配置

主要是配置下github账户信息：

```
deploy: 
     type: git 
     repo: https://github.com/用户名/用户名.github.io.git 
     branch: master
```
其他配置信息可以参考各个主题文档



# 主题配置

用过material、yilla、next等，也用过自定义的主题，其实内容都是大同小异，主要就是_config.yml修改，插入图片，配置rss、评论、搜索、统计等信息。详细见各个主题Doc。

我最近使用的是material( [Material Theme Doc](https://mt.viosey.com/docs/#/) )，也是我推荐的。它的主题是简洁，看着很干净。

具体流程：

- 安装主题
- 启用主题
- 主题配置

## 启用主题

在站点 _config.yml 中设置theme

## 主题配置

### 头像等信息

### 添加分类

这块也是因主题而异。material主题只需要在post的md中首行添加

```
categories:
  - 博客栈
```

### 设置favicon

选择一个favicon制作网站完成制作，例如[比特虫](http://www.bitbug.net/)是一个免费的在线制作ico图标网站。




# 插件第三方

## 添加版权声明

- [为Hexo博客添加版权说明和打赏功能](http://www.tuicool.com/articles/m6bIJfq) 

## 添加canvas_nest特效

- [hexo文章添加版权声明及一些特效](http://tc9011.com/2017/02/02/hexo%E6%96%87%E7%AB%A0%E6%B7%BB%E5%8A%A0%E7%89%88%E6%9D%83%E5%A3%B0%E6%98%8E%E5%8F%8A%E4%B8%80%E4%BA%9B%E7%89%B9%E6%95%88/) 

## algolia搜索

- [hexo+next添加algolia搜索](http://www.jianshu.com/p/fa2354d61e37)
- 修改文章需要执行：
```
  hexo algolia
```

## 添加文章阅读量

- [leanCloud,实现文章阅读量统计](http://www.joryhe.com/2016-05-29-how_to_create_leancloud_read_Counter.html) 

## 打赏功能

- [hexo主题下的开启打赏功能具体是怎么实现的呀？](https://segmentfault.com/q/1010000008403584) 

## 音乐外链

- [Hexo中播放网易云音乐的实践](http://weqeo.com/2016/10/11/Hexo%E4%B8%AD%E6%92%AD%E6%94%BE%E7%BD%91%E6%98%93%E4%BA%91%E9%9F%B3%E4%B9%90%E7%9A%84%E5%AE%9E%E8%B7%B5/) 

## 网易云跟帖

- [Hexo博客（Next主题）放弃多说，接入网易云跟贴](http://blog.csdn.net/yingpaixiaochuan/article/details/68954103)

## 首页文章以摘要形式显示

- 打开**主题配置文件**，找到如下位置，其中`length`代表显示摘要的截取字符长度。修改
```
  auto_excerpt:
    enable: true
  length: 150
```

## 设置首页文章显示篇数

### 安装插件

```
  npm install --save hexo-generator-index
  npm install --save hexo-generator-archive
  npm install --save hexo-generator-tag
```

### 修改站点配置文件

```
index_generator:
  per_page: 5

archive_generator:
  per_page: 20
  yearly: true
  monthly: true

tag_generator:
  per_page: 10
```

## 设置404公益页面

在**站点目录**的source文件夹下，新建`404.html`文件，将下面的代码复制进去保存即可。

```html
<!DOCTYPE HTML>
<html>
<head>
    <title>404 - arao'blog</title>
    <meta name="description" content="404错误，页面不存在！">
    <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
    <meta name="robots" content="all" />
    <meta name="robots" content="index,follow"/>
</head>
<body>
    <script type="text/javascript" src="http://qzonestyle.gtimg.cn/qzone_v6/lostchild/search_children.js" charset="utf-8"></script>
</body>
</html>
```


# 常见问题

## github clone速度慢

```shell
git config --global http.postBuffer 524288000
```
## Hexo Next的文章目录形式异常

### 反引号问题

用typora生成文章并传给next，中间需要注意的是代码注释段(反引号)，typora和next解析逻辑不一样，注意去掉反引号前面的空格,可以使toc匹配
如果去除反引号和上面的空行，可以使代码段格式和上面内容对齐

### Head错位

比如上一层是H2，内部是H4，就会出现错位。

## Hexo Next的文章不显示内容

引用中设置字体，会导致整个文档无法解析。

## Hexo g 无法生成html

排查发现对以下解析问题：

```
`{{{ xxx }}}`
`{{* foo }}`
`{{ }}`
```

-----

基本这就是搭建一个github blog需要包括的内容了。当然，最快的方式也许是找一个官方demo直接clone修改配置。