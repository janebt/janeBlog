---
title: webpack篇
date: 2017-10-02 09:57:34
updated: 2017-10-02 09:57:34
tags:
  - Webpack
categories:
  - 构建
toc: true
---
本文介绍webpack结构

<!-- more -->

# webpack篇

# 概念

不像大多数的模块打包机，webpack是收把项目当作一个整体，通过一个给定的的主文件，webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包成一个或多个浏览器可识别的js文件

# install

webpack是我们需要的模块打包机，webpack-dev-server用来创建本地服务器，监听你的代码修改，并自动刷新修改后的结果。这些是有关**devServer的配置**

```javascript
contentBase,  // 为文件提供本地服务器
port, // 监听端口，默认8080
inline, // 设置为true,源文件发生改变自动刷新页面
historyApiFallback  // 依赖HTML5 history API,如果设置为true,所有的页面跳转指向index.html

devServer:{
    contentBase: './src' // 本地服务器所加载的页面所在的目录
    historyApiFallback: true, // 不跳转
    inline: true // 实时刷新
}
```

然后我们在根目录下创建一个'webpack.config.js'，在'package.json'添加两个命令用于本地开发和生产发布

```javascript
"scripts": {
  	"start": "webpack-dev-server",
	"build": "webpack"
}
```


# webpack.config.js

## entry

entry: 用来写入口文件，它将是整个依赖关系的根，当我们需要多个入口文件的时候，可以把entry写成一个对象

```javascript
var baseConfig = {
        entry: {
            main: './src/index.js'
        }
    }
```

## output

output: 即使入口文件有多个，但是只有一个输出配置

```javascript
var path = require('path')
var baseConfig = {
	entry: {
		main: './src/index.js'
	},
	output: {
		filename: 'main.js',
		path: path.resolve('./build')
    }
}
module.exports = baseConfig
```

如果你定义的入口文件有多个，那么我们需要使用**占位符**来确保输出文件的唯一性

```javascript
output: {
  	filename: '[name].js',
    path: path.resolve('./build')
}
```

注：filename应该比较好理解，就是对应于entry里面生成出来的文件名。

## Loader

**loader的作用**： 

1. 实现对不同格式的文件的处理，比如说将scss转换为css，或者typescript转化为js
2. 转换这些文件，从而使其能够被添加到依赖图中

loader是webpack最重要的部分之一，通过使用不同的Loader，我们能够调用外部的脚本或者工具，实现对不同格式文件的处理，loader需要在webpack.config.js里边单独用module进行配置，需要的loader需要通过npm 进行安装。配置如下：

```javascript
var baseConfig = {
  // ...
  module: {
    rules: [
      {
        test: /*匹配文件后缀名的正则*/,
        use: [
        loader: /*loader名字*/,
        query: /*额外配置*/
        ]
      }
    ]
  }
}
```

几个常用的loader：

- babel-loader： 让下一代的js文件转换成现代浏览器能够支持的JS文件。babel有些复杂，所以大多数都会新建一个.babelrc进行配置
- css-loader,style-loader:两个建议配合使用，用来解析css文件，能够解释@import,url()如果需要解析less就在后面加一个less-loader
- file-loader: 生成的文件名就是文件内容的MD5哈希值并会保留所引用资源的原始扩展名
- url-loader: 功能类似 file-loader,但是文件大小低于指定的限制时，可以返回一个DataURL事实上，在使用less,scss,stylus这些的时候，npm会提示你差什么插件，差什么，你就安上就行了

## Plugins

**plugins和loader区别**

**loaders负责的是处理源文件的如css、jsx，一次处理一个文件。而plugins并不是直接操作单个文件，**它直接对整个构建过程起作用。下面列举了一些我们常用的plugins和他的用法

- ExtractTextWebpackPlugin: 它会将入口中引用css文件，都打包都独立的css文件中，而不是内嵌在js打包文件中。
- HtmlWebpackPlugin:它依据一个简单的index.html模版，生成一个自动引用你打包后的js文件的新index.html
- HotModuleReplacementPlugin: 它允许你在修改组件代码时，自动刷新实时预览修改后的结果注意永远不要在生产环境中使用HMR。这儿说一下一般情况分为开发环境，测试环境，生产环境。

Case.webapck.config.js的全部内容:

```javascript
const webpack = require("webpack")
const HtmlWebpackPlugin = require("html-webpack-plugin")
var ExtractTextPlugin = require('extract-text-webpack-plugin')
var lessRules = {
  use: [
    {loader: 'css-loader'},
    {loader: 'less-loader'}
  ]
}
module.exports = {
  entry: {
    main: './src/index.js'
  },
  output: {
    filename: '[name].js',
    path: path.resolve('./build')
  },
  devServer: {
    contentBase: '/src',
    historyApiFallback: true,
    inline: true,
    hot: true
  },
  module: {
    rules: [
      {test: /\.less$/, use: ExtractTextPlugin.extract(lessRules)}
    ]
  },
  plugins: [
    new ExtractTextPlugin('main.css')
  ]
}
```



# 产品阶段的构建

在产品阶段，还需要对资源进行别的
处理，例如压缩，优化，缓存，分离css和js。首先我们来定义产品环境

```javascript
var ENV = process.env.NODE_ENV
var baseConfig = {
  // ... 
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(ENV)
    })
  ]
}
```

然后还需要修改我们的script命令

```javascript
"scripts": {
	"start": "NODE_ENV=development webpack-dev-server",
	"build": "NODE_ENV=production webpack"
}
```

process.env.NODE_ENV 将被一个字符串替代，它运行压缩器排除那些不可到达的开发代码分支。
当你引入那些不会进行生产的代码，下面这个代码将非常有用。

```javascript
if (process.env.NODE_ENV === 'development') {
  console.warn('这个警告会在生产阶段消失')
}
```

# 优化插件

- OccurenceOrderPlugin: 为组件分配ID,通过这个插件webpack可以分析和优先考虑使用最多 的模块，然后为他们分配最小的ID
- UglifyJsPlugin: 压缩代码

使用方法

```javascript
var baseConfig = {
  // ...
  new webpack.optimize.OccurenceOrderPlugin()
  new webpack.optimize.UglifyJsPlugin()
}
```

然后在我们使用npm run build会发现代码是压缩的