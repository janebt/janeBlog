---
title: IPC项目Websocketd服务器总结
date: 2018-01-20 18:37:34
updated: 2018-01-20 18:37:34
tags:
  - websocket
  - IPC
categories:
  - 项目
toc: true
---
工作需要，在16年写了个websocket服务器做实时推送音量。当时还没有找到C版的websocket，于是自己参考java写了个，功能比较简单，协议特性实现了实时，但是同步功能是靠开多线程实现，大PV可能不稳定，这块可以再优化管理。

这篇主要介绍一下原理，实现比较简单可参考我的github，如果使用主流服务器语言库也很多。

<!-- more -->

# C版websocketd服务器总结

## websocketd

### 背景简介

#### 1. WebSocket API简介

```javascript
var ws = new WebSocket(“ws://echo.websocket.org”);  
ws.onopen = function(){ws.send(“Test!”); };  
ws.onmessage = function(evt){console.log(evt.data);ws.close();};  
ws.onclose = function(evt){console.log(“WebSocketClosed!”);};  
ws.onerror = function(evt){console.log(“WebSocketError!”);};
```

#### 2. 为什么引入WebSocket协议

##### 1. Polling

- 既浪费了网络带宽，又浪费了CPU的利用率
- 实时性

##### 2. Long Polling

- Browser显示实时数据最快的时间为2×RTT（往返时间）,网络拥塞
- 网络带宽浪费

##### 3. WebSocket

![](http://img.my.csdn.net/uploads/201202/27/0_133032795020RR.gif)

1. Browser与WebSocket服务器通过TCP三次握手建立连接，如果这个建立连接失败，那么后面的过程就不会执行，Web应用程序将收到错误消息通知。
2. 在TCP建立连接成功后，Browser/UA通过http协议传送WebSocket支持的版本号，协议的字版本号，原始地址，主机地址等等一些列字段给服务器端。

3. WebSocket服务器收到Browser/UA发送来的握手请求后，如果数据包数据和格式正确，客户端和服务器端的协议版本号匹配等等，就接受本次握手连接，并给出相应的数据回复，同样回复的数据包也是采用http协议传输。
4. Browser收到服务器回复的数据包后，如果数据包内容、格式都没有问题的话，就表示本次连接成功，触发onopen消息，此时Web开发者就可以在此时通过send接口想服务器发送数据。否则，握手连接失败，Web应用程序会收到onerror消息，并且能知道连接失败的原因。

总结为：握手阶段 + 通信阶段

### 握手阶段

#### 原理

客户端握手请求报头：

```http
GET /chat HTTP/1.1  // 请求行
Host: server.example.com
Upgrade: websocket  // required
Connection: Upgrade // required
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ== // required
Origin: http://example.com  // 用于防止未认证的跨域脚本使用浏览器 websocket api 与服务端进行通信
Sec-WebSocket-Protocol: chat, superchat  // optional, 子协议协商字段
Sec-WebSocket-Version: 13
```

服务端响应报头:

```http
HTTP/1.1 101 Switching Protocols  // 状态行
Upgrade: websocket   // required
Connection: Upgrade  // required
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo= // required
Sec-WebSocket-Protocol: chat // 表明选择的子协议
```

握手阶段，具体来讲，就是当浏览器脚本 new WebSocket(url) 后，浏览器对服务器发送一个协议升级的请求，请求中带有 Sec-WebSocket-Key 字段。

服务端接收到协议提升请求后对这个字段加上一个特定的 **GUID** 后做一次 sha1 运算，然后再获取结果的 base64 格式摘要，**作为 Sec-WebSocket-Accept 响应头的值响应回客户端浏览器**，就完成了握手。

### ws帧解码与编码

...

### 实现

initSocket()，包括：

```c
initSocket()
{
  gethostname
  socket
  setsockopt
  bind(curr_sock)
}
```



mainLoop()，包括：

```c
while
{
  	listen(curr_sock)
 	select	//非阻塞，等待多个fd中至少一个就绪,测超时，测fd
    FD_ISSET(curr_sock)	//检查哪一个fd就绪
    {
      	fork_sock = accept(curr_sock)	//用于读缓冲区
        fork()
        close(curr_sock);
        startChild()
    }
}
```

startChild()，包括：

```c
startChild()
{
  while(1)
  {
    read
    1.握手：shakeHand
      
    2.解包
    decodeReceiveData
    switch(opcode)
      case：handleTextMsg + encodeSendData //组payload，前者是处理json，后者编码响应报文
    write(fork_sock)
  }
}
```

### 一些浅显的思考

#### 为什么websocket一直不火？

现在是18年了，距离websocket出现也有3年多了，可是国内很多公司好像都不待见。我认为可能有以下一些原因：

- 技术框架不成熟，国内除了BAT很多担心技术壁垒踩坑，很多公司http切https都很少，更不要说http2、websocket这些了。
- 使用场景少，很少有需要实时+不断推送+安全性的数据流的场景，有兼容性等考虑。
- B/S架构想变为C/S架构，已经不是http切成https那么简单了，有一种颠覆性的认识，需要改动的东西很多，参考java为什么还在存活且存活的最好。反而我认为ajax切fetch这种会更easy。

#### websocket使用场景？

- 长连接+实时
- 个人认为多用在少交互多展示的场景，倒不是协议的问题，比如后台要处理5s才能响应siri并普推，那么这5s的间隔是要优化的，这不是主要问题，主要问题是后台也要对延时性响应的要求怎么处理，一般放到队列处理那么最后一个被处理的websocket请求就会爆炸，当然普通的交互也有这个问题，但是普通交互没有实时性的要求。

### TODO

- 深入细化+优化
- 搭一套高效的框架，其中开一个websocket端口来专门分发实时展示页