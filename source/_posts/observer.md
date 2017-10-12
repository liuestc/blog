---
title: observer
date: 2017-10-12 10:41:45
tags:
---

关于这篇文章还有诸多不理解。
[自己动手实现vue](http://www.w3cplus.com/vue/vue-two-way-binding.html)

###  主要不理解的地方

1. watcher那里是要实现什么功能？
2. 订阅器，观察者（watcher）概念的理解
3. 观察者模式的理解

#### 观察者模式理解


```
var pubsub = {};
(function (q) {

    var topics = {}, // 回调函数存放的数组
        subUid = -1;
    // 发布方法
    q.publish = function (topic, args) {

        if (!topics[topic]) {
            return false;
        }

        setTimeout(function () {
            var subscribers = topics[topic],
                len = subscribers ? subscribers.length : 0;

            while (len--) {
                subscribers[len].func(topic, args);
            }
        }, 0);

        return true;

    };
    //订阅方法
    q.subscribe = function (topic, func) {

        if (!topics[topic]) {
            topics[topic] = [];
        }

        var token = (++subUid).toString();
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    };
    //退订方法
    q.unsubscribe = function (token) {
        for (var m in topics) {
            if (topics[m]) {
                for (var i = 0, j = topics[m].length; i < j; i++) {
                    if (topics[m][i].token === token) {
                        topics[m].splice(i, 1);
                        return token;
                    }
                }
            }
        }
        return false;
    };
} (pubsub));
```


下面代码展示了观察者模式的基本逻辑，suscribe方式其实就是忘topics里面添加函数,然后topics就类似下面这样。

```

topics:{
    example:[{
        token:0,
        func:function(){
            //
        },{
            token:1,
            func:function(){

            }
        }
        }]
}
```




然后publish方法相当于执行一遍subscrib里的方法在while里循环遍历数组


定义和调用

```
//来，订阅一个
pubsub.subscribe('example1', function (topics, data) {
    console.log(topics + ": " + data);
});

//发布通知
pubsub.publish('example1', 'hello world!');
pubsub.publish('example1', ['test', 'a', 'b', 'c']);
pubsub.publish('example1', [{ 'color': 'blue' }, { 'text': 'hello'}]);
```


#### 理解：
观察者模式相当于封装一层。封装的方法包括定义、调用、解绑。

定义的时候执行`subscribe`方法，传入函数名和函数，调用的时候传入方法名和参数。


更高级的订阅

