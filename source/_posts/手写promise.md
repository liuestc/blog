---
title: 手写promise
date: 2019-03-18 15:53:19
tags:
---

作为一个经典题目，有必要手动实现一下，先看一般的结构

```
    let p1=new Promise((resolve,reject)=>{
        setTimeout(()=>{
            let num=Math.random()
            console.log('num',num)
            if(num>.5){
                resolve('成功')
            }
            else{
                reject('失败')
            }
        },2000)
    })
    p1.then(function(val){
        console.log(val)
    },function(val){
        console.log("失败："+val)
    }) 
```

大致结构如下
思考：Promise是一个构造函数，传入一个函数，并执行该函数，其中有resolve/reject两个方法进行状态转换。

大致结构

```
new Promise(fn){

    //resolve/reject 状态转换

    function resolve(value){
        if(state=='pending'){
            state='resolve'
            cbResolve.forEach((cb)=>cb(value))
        }
    }

    function reject(resolve){
        
    }

    //最后需执行这个函数
    fn(resolve,reject)


}

Promise.prototype.then=function(resolveFn,rejectedFn){
    if(this.state==resolve){
        resolveFn(this.value)
    }
}


```

具体代码如下：

```
    const PENDING='pending'
    const RESOLVED='resolved'
    const REJECTED='rejected'
    function MyPromise(fn){
        const that=this
        that.status=PENDING
        that.onFulfilled=[]
        that.onRejected=[]
        function resolve(val){
            if(that.status==PENDING){
                that.status=RESOLVED
                that.val=val
                that.onFulfilled.map((cb)=>cb(that.val))
            }

        }
        function resolve(val){
            if(that.status==PENDING){
                that.status=REJECTED
                that.val=val
                that.onRejected.map((cb)=>cb(that.val))
            }
        }

        try{
            fn(resolve,resolve)
        }catch(e){

        }
    }

```


注意看这里是这么个结构，传入fn，并立即调用他

```
    function Test(fn){
        function a(){
            console.log('a')
        }
        function b(){
            console.log('b')
        }

        fn(a,b)  
        //  这里为什么要执行一次？传入function fn(a,b){a()} 必须执行一次才能调用到a方法
    }
    p2=new Test(function fn(a,b){
        a()
    })
```

构造函数的参数是一个函数，该函数又有两个参数a,b，然后执行该函数，思考，实例化的时候我们该传入什么？

传入一个有执行逻辑的fn，该fn是什么就会执行什么
有一个疑问？构造方法中a,b在是实例化的时候也会出现在p2中么？

***


然后实现`then`方法

```
MyPromise.prototype.then = function(onFulfilled, onRejected) {
  const that = this
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : r => {
          throw r
        }
  if (that.state === PENDING) {
    that.resolvedCallbacks.push(onFulfilled)
    that.rejectedCallbacks.push(onRejected)
  }
  if (that.state === RESOLVED) {
    onFulfilled(that.value)
  }
  if (that.state === REJECTED) {
    onRejected(that.value)
  }
}
```

+ 首先判断两个参数是否为函数类型，因为这两个参数是可选参数
+ 当参数不是函数类型时，需要创建一个函数赋值给对应的参数，同时也实现了透传
+ 接下来就是一系列判断状态的逻辑，当状态不是等待态时，就去执行相对应的函数。如果状态是等待态的话，就往回调函数中 push 函数

***

到这里就结束了么，并没有，未实现链式调用，链式调用中每一次then都返回promise对象



