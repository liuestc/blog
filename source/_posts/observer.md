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
观察者模式相当于封装一层容器。封装的方法包括定义、调用、解绑。这个容器中你可以定义一些方法，然后并在合适的时间调用。


比如有两段 功能1和功能2模块，你可以把功能1和功能2都注册入容器。然后你就可以在任意地方调用了。

定义的时候执行`subscribe`方法，传入函数名和函数，调用的时候传入方法名和参数。

****

读书笔记：

发布订阅模式相当于去售楼部买房子，买房人去售楼部登记，售楼小姐有消息就通知所有登记的人。

DOM事件也是发布-订阅模式
何为发布者，何为订阅者？用户操作相当于一个发布者，DOM点击事件相当于订阅者

    document.body.addEventListener("click",function(){
        alert("1")
    })
    //  添加多个订阅者
    document.body.addEventListener("click",function(){
        alert("2")
    })
    document.body.addEventListener("click",function(){
        alert("3")
    })
    document.body.click()  //  模拟点击

//  自己理解，发布-订阅模式相当于   a事件触发  b群体事件(b中的某个方法)

动手实现一个发布-订阅模式

从上例买房例子说起
1. 确定发布者，即售楼部
2. 在发布者里缓存一个列表，存放回调函数来通知订阅者，即售楼部花名册  //  存的是订阅者列表还是发布者函数
3. 发布消息，发布者遍历列表中存放的订阅者回调函数，即售楼部遍历花名册发短信

另外我们可以在订阅回调函数中传入一些参数，订阅者可以接受这些参数。即短信中加入面积、单价等信息。

    var salesOffices={}  // 售楼部
    salesOffices.clientList=[]  // 购房回调函数
    salesOffices.listener=function(fn){
        salesOffices.clientList.push(fn)
    }
    salesOffices.trigger=function(){
        for(var i=0,fn;fn=this.clientList[i++];){  //  这里的for循环只有两项 fn为空时循环结束
            fn.apply(this,arguments);
        }
    }
    //  下面是一些实例  a订阅消息
    salesOffices.listener(function(price,squareMeter){
        console.log("价格："+price)
        console.log("面积："+squareMeter)
        })
    // b订阅消息
    salesOffices.listener(function(price,squareMeter){
        console.log("价格："+price)
        console.log("面积："+squareMeter)
        })
    //  发布消息
    salesOffices.trigger(2000,80)
    salesOffices.trigger(1000,60)         

上面代码有问题么？可以看到a只订阅了一个消息却收到两条消息，如何避免？可以在clientList存入对象，调用时根据所订阅的key来执行相应方法。

    var salesOffices={}  // 售楼部
    salesOffices.clientList={}  // 购房回调函数
    salesOffices.listener=function(key,fn){
        if(!this.clientList[key]){
            this.clientList[key]=[]
        }
        this.clientList[key].push(fn)
    }
    salesOffices.trigger=function(){
        var key=Array.prototype.shift.call(arguments)
        var fns=this.clientList[key]
        if(!fns||fns.length==0){
            return false
        }
        for(var i=0,fn;fn=fns[i++];){
            fn.apply(this,arguments)
        }
        for(var i=0,fn;fn=this.clientList[i++];){  //  这里的for循环只有两项 fn为空时循环结束
            fn.apply(this,arguments);
        }
    }
    //  下面是一些实例  a订阅消息
    salesOffices.listener("key1",function(price,squareMeter){
        console.log("价格："+price)
        console.log("面积："+squareMeter)
        })
    // b订阅消息
    salesOffices.listener("key2",function(price,squareMeter){
        console.log("价格："+price)
        console.log("面积："+squareMeter)
        })
    //  发布消息
    salesOffices.trigger("key1",80000,20)
    salesOffices.trigger("key2",60000,10)

发布订阅通用模板

    var event={
        clientList:[],
        listener:function(key,fn){
            if(!this.clientList[key]){
                this.clientlist[key]=[]
            }
            this.clientList[key].push(fn)
        },
        trigger:function(){
            var key=Array.prototype.shift(arguments)
            var fns=this.clientList[key];
            if(!fns||fns.length==0){
                return false;
            }
            for(var i=0,fn;fn=fns[i++];){
                fn.apply(this,arguments)
            }
        },
        remove:function(key,fn){
            var fns=this.clientList[key]
            if(!fns){
                return false;
            }
            if(!fn){
                fns&&(fns.length=0)  //  置空
            }
            else{
                for(var l=fns.length-1;l>=0;l--){
                    var _fn=fns[l]
                    if(_fn==fn){
                        fns.splice(l,1)
                    }
                }
            }
        }
    }
    var installEvent=function(obj){
        for(var i in event){
            obj[i]=event[i]
        }
    }

项目中的应用，思考在ajax中的回调函数中我们有很多事件处理,登录模块是你维护的，登陆成功后给各个模块设置东西，假如新加了c模块，你就要继续在login模块中加入c模块代码。然后你不在，这段代码就不好维护，登录模块和其他模块解耦该如何做？

    login.success(function(res)=>{
        header.setAvatar(data)
        footer.setAttribue(data)
        })

可以利用发布订阅模式实现解耦

    $.ajax("url",function(data){
        login.trigger("loginSucc",data)
    })

其他模块监听消息

    var header=(function(){
            login.lister("loginSucc",function(data){
                header.setAttribute(data)
                })
                return {
                    setAvatar:function(data){
                        console.log("...")
                    }
                }
        })()

结束！一些存在的问题，外部可以直接访问event对象里面的属性，这样不是很好。还有一些问题，需要为每个发布者都添加listener方法，缓存一个clientList列表，这样开销比较大，必须知道售楼部名字才能订阅事件，小明要想订阅a,b售楼部消息就得订阅两个对象。如何解决?现实生活中，我们可以从中介公司那里得到很多楼盘的信息，so我们可以这样做：

    var Event=(function(){
        var clientList=[],listener,trigger,remove;
        listener=function(){
            //...
        }
        trigger=function(){
            //...
        }
        remove=function(){
            //...
        }
        return:{
            listener:listener,
            trigger:trigger,
            remove:remove
        }
    })()


