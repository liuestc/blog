---
title: websocket
date: 2017-12-21 10:12:53
tags:
---

### websocket是什么（what?）

### websocket解决了什么问题(why?)

通常的http请求只能由客户端发起，服务器是不能推消息给客户端的。要实时查询服务器就得轮询，这样效率比较低。为解决双向通信问题，websocket就诞生了。

### 如何使用(how?)

1.简单例子

    var ws = new WebSocket("wss://echo.websocket.org");

    ws.onopen = function(evt) { 
      console.log("Connection open ..."); 
      ws.send("Hello WebSockets!");
    };

    ws.onmessage = function(evt) {
      console.log( "Received Message: " + evt.data);
      ws.close();
    };

    ws.onclose = function(evt) {
      console.log("Connection closed.");
    };  

2.客户端

    var wx=new WebSocket("wx://xxx")  //与服务器建立连接

服务器数据可能是文本，也可能是二进制数据（blob对象或Arraybuffer对象）。

>ArrayBuffer对象、TypedArray对象、DataView对象是JavaScript操作二进制数据的一个接口。(what)

>这些对象原始的设计目的，与WebGL项目有关。所谓WebGL，就是指浏览器与显卡之间的通信接口，为了满足JavaScript与显卡之间大量的、实时的数据交换，它们之间的数据通信必须是二进制的，而不能是传统的文本格式。文本格式传递一个32位整数，两端的JavaScript脚本与显卡都要进行格式转化，将非常耗时。这时要是存在一种机制，可以像C语言那样，直接操作字节，将4个字节的32位整数，以二进制形式原封不动地送入显卡，脚本的性能就会大幅提升。
二进制数组就是在这种背景下诞生的。它很像C语言的数组，允许开发者以数组下标的形式，直接操作内存，大大增强了JavaScript处理二进制数据的能力，使得开发者有可能通过JavaScript与操作系统的原生接口进行二进制通信。(why?)

>ArrayBuffer对象代表原始的二进制数据，TypedArray对象代表确定类型的二进制数据，DataView对象代表不确定类型的二进制数据。它们支持的数据类型一共有9种（DataView对象支持除Uint8C以外的其他8种）。


很多浏览器操作的API，用到了二进制数组操作二进制数据，下面是其中的几个。

+ File API
+ XMLHttpRequest
+ Fetch API
+ Canvas
+ WebSockets


具体数据格式[戳这里](http://javascript.ruanyifeng.com/stdlib/arraybuffer.html)

