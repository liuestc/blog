---
title: vue原理
date: 2017-10-10 10:40:52
tags:
categories: vue
---

理解vue的实现原理


### 现象

主要`Object.defineProperty`方法，这个方法可以操作对象的一些属性。

先看下vue中的数据长什么样？

```
var vm = new Vue({
 data: { 
    obj: { 
        a: 1 
        } 
        }, 
 created: function () { 
        console.log(this.obj);
        } 
     });
```

打印如下

![屏幕快照 2017-10-11 下午2.55.14.png](https://i.loli.net/2017/10/11/59ddc0778377a.png)

可以看到有个 `get a`和`set a`方法，下面细谈下这两东西

### 原理

通常定义一个对象的属性直接用的默认值，我们可以用`Object.defineProperty()`方法自定义一些事情。像下面这样：

```
var obj={}

Object.defineProperty("obj","a",{
    value:2,
    writable:true,
    configurable:true, // 可配置，是否可以用defineProrerty修改
    enumerable:true   //  可枚举
    })
```

其实这都是默认值，除了上述属性还有一些方法

```

var Book = {}
var name = ''; 
Object.defineProperty(Book, 'name', 
    { 
        set: function (value) { 
            name = value;
            console.log('你取了一个书名叫做' + value); 
        }, 
        get: function () { 
                return '《' + name + '》' 
            } 
    }) 
Book.name = 'vue权威指南'; // 你取了一个书名叫做vue权威指南 console.log(Book.name); // 
```

有两个方法 ，set  设置的时候触发，get访问的时候触发。如果我们`console.log(Book)`就会出现和vue打印差不多的情况。

继续讨论这两个方法

```
var myObj={
    get a(){
        return 4;
    }
}

Object.defineProperty(
    myObj,
    "b",
    {
        get: function(){
            return this.a*2
        },
        enumberable:true
    }
    )    
```


打印出 4和8.没毛病。

### vue中如何实现
思考 M和V之间的关系。M变化引起V的变化？这个如何实现。M变化的时候，会触发set，在set中让V改变即可。

思考下面代码：
```
function observe(data){
    if(!data|| typeof data !=='object'){
        return
    }
    Object.keys(data).forEach(function(key){
        defineReactive(data,key,data[key])
        })
}

function defineReactive(data,key,val){
    observe(val)
    Object.defineProperty(data,key,{
        enumerable:true,
        configurable:true,
        get:function(){
            return val
        },
        set:function(newVal){
            val=newVal;
            console.log("属性"+key+":被监听"+newVal.toString())
        }
        })
}


var library={
    book1:{
        name:""
    },
    book2:""
}

observe(library)
library.book1.name="test1"

library.book2="test2"
```



补充：
>Object.keys 返回一个所有元素为字符串的数组，其元素来自于从给定的对象上面可直接枚举的属性。这些属性的顺序与手动遍历该对象属性时的一致。










#### 相关链接

[观察者模式](https://github.com/Kelichao/javascript.basics/issues/22)




