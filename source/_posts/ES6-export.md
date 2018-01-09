---
title: ES6-export
date: 2017-12-28 11:02:30
tags:
---
js模块相关知识整理

### what?

什么是模块？
通常指编程中的代码组织机制，利用此机制可以将程序分为独立且通用的代码单元。

### why?

为什么要模块化？
远古时代，js只运用于表单和页面效果之类的简单功能，但随着web的复杂性越来越高，未模块化的代码会出现难以复用，全局变量污染，命名冲突，依赖管理难以维护等诸多问题。

###how

如何模块化？

#### CommonJS

  特点：服务器端解决方案，同步加载

  代码示例

    function SayModule () {
        this.hello = function() {
             console.log('hello');
        };
        this.goodbye = function () {
           console.log('goodbye');
        };
    }
    module.exports = SayModule;
    //main.js 引入sayModule.js
    var Say= require('./sayModule.js');
    var sayer = new Say();
    sayer.hello(); 
    //hello

node实现就是这种方式
