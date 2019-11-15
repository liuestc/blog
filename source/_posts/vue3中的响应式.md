---
title: vue3中的响应式
date: 2019-10-16 20:20:44
tags:
---

```js

```

get 依赖收集

set->notify->virtualDOM->render

// 依赖收集 指数据绑定的数组


// proxy 不能操作源对象  最好将源对象重写  a=new Proxy()
1 全局监听,definProperty 只能监听属性
2 监听数组
3 省去for in  

检验类型
私有变量

Reflect.set 

双向绑定
虚拟DOM 选项合并 patch