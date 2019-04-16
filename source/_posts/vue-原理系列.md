---
title: vue 原理系列
date: 2018-04-18 15:57:55
tags:
categories: "源码相关"
---


###  数据劫持

递归给每层对象的属性加`Object.definProperty`

```
    function Vue(options={}){
        this.$options=options //属性挂载
        var data=this._data=this.$options.data
        observe(data)
    }
    function observe(data){
        return new Observe(data)
    }
    function Observe(data){
        for(let i in data){
            let val=data[i]
            if(typeof data[i]=='object'&&data[i]){
                observe(data[i])
            }
            else{
                Object.defineProperty(data,i,{
                    configurable:true,
                    enumerable:true,
                    get(){
                        console.log('get')
                        return val
                    },
                    set(newVal,oldVal){
                        if(val!==newVal){
                            val=newVal
                            observe(newVal)
                        }
                        return;
                        
                    } 
                })
            }
        }

    }

    //
    let data={
        a:'heihei',
        b:{
            c:'test'
        }
    }

    let ljd=new Vue({
        data,
        el:'#app'
    })
```

到这里完成了数据劫持，但是还是有问题的
每次的通过`ljd._data.a`访问属性，多一层，我们期望直接通过`ljd.a`访问
如何做？

###  代理
简单方法就是把数据直接挂载到最外层，
引申 vue中为何不能新增不存在的属性？因为无set和get。必须通过this.$set方法
```
    function Vue(options={}){
        this.$options=options //属性挂载
        var data=this._data=this.$options.data
        observe(data)
        // 代理到最外层
        for(let key in data){
            Object.defineProperty(this,key,{
                enumerable:true,
                get(){
                    return this._data[key]
                },
                set(newVal){
                    this._data[key]=newVal
                }
            })
        }
    }
```


### 模板编译
主要是如何将数据绑定到页面，思路是拿到DOM,然后正则替换里面的内容,这里用到了DocumentFragments,用该方法有以下好处

>DocumentFragments 是DOM节点。它们不是主DOM树的一部分。通常的用例是创建文档片段，将元素附加到文档片段，然后将文档片段附加到DOM树。在DOM树中，文档片段被其所有的子元素所代替。
因为文档片段存在于内存中，并不在DOM树中，所以将子元素插入到文档片段时不会引起页面回流（对元素位置和几何上的计算）。因此，使用文档片段通常会带来更好的性能。


```
    function Vue(options={}){
        this.$options=options //属性挂载
        this.el=this.$options.el
        var data=this._data=this.$options.data
        observe(data)
        // 代理到最外层
        for(let key in data){
            Object.defineProperty(this,key,{
                enumerable:true,
                get(){
                    return this._data[key]
                },
                set(newVal){
                    this._data[key]=newVal
                }
            })
        }
        // console.log(this.el)
        new Compile(this.el,this)
    }
    function Compile(el,vm){
        vm.$el=document.querySelector(el)
        //创建文档碎片
        //createDocumentFragment
        //appendChild 如果被插入的节点已经存在于当前文档的文档树中,则那个节点会首先从原先的位置移除,然后再插入到新的位置.
        let fragment=document.createDocumentFragment();
        while(child=vm.$el.firstChild){
            fragment.appendChild(child)
        }
        console.log('fragment',fragment)
        replace(fragment)
        function replace(fragment){
            // 类数组转化为数组
            Array.from(fragment.childNodes).forEach((item)=>{
                let text=item.textContent;
                let reg=/\{\{(.*)\}\}/
                if(item.nodeType==3&&reg.test(text)){
                    console.log(RegExp.$1)
                    //解析a.b
                    let arr=RegExp.$1.split('.')
                    console.log(arr,vm)
                    let val=vm
                    arr.forEach((k)=>{
                        val=val[k]
                    })
                    item.textContent=text.replace(reg,val)
                }
                if(item.childNodes){
                    replace(item)
                }
            })
        }
        vm.$el.appendChild(fragment)
    }
```

### 发布订阅模式
现在数据已经映射到页面上了，但是更改数据并没有相应，因此需要给数据订阅事件，每当数据改变时，就更新页面

```
    function Dep(){
        this.subs=[]
    }
    Dep.prototype.addSub=function(fn){
        this.subs.push(fn)
    }
    Dep.prototype.notify=function(){
        this.subs.forEach(item=>item.update())
    }
    function Watcher(fn){
        this.fn=fn
    }
    Watcher.prototype.update=function(){
        this.fn()
    }
    let watcher=new Watcher(function(){
        console.log(1)
    })

    let dep=new Dep()
    console.log(dep)
    dep.addSub(watcher)
    dep.notify()
```

以上是发布订阅主要逻辑
在Vue 中，当进行模板替换时我们给数据订阅事件，当属性被改变时，我们执行方法。

```
    function Watcher(vm,exp,fn){
        this.fn=fn
        this.vm=vm
        this.exp=exp
        Dep.target=this
        let arr=exp.split('.')
        //console.log(arr,vm)
        let val=vm
        arr.forEach((k)=>{
            val=val[k]
        })
        Dep.target=null

    }
    Watcher.prototype.update=function(){

        let val=this.vm
        let arr=this.exp.split('.')
        arr.forEach((k)=>{
            val=val[k]
        })
        this.fn(val)
    }


    function Observe(data){
        let dep=new Dep()
        for(let i in data){
            let val=data[i]
            if(typeof data[i]=='object'&&data[i]){
                observe(data[i])
            }
            else{
                Object.defineProperty(data,i,{
                    configurable:true,
                    enumerable:true,
                    get(){
                        // console.log('get')
                        Dep.target&&dep.addSub(Dep.target)
                        return val
                    },
                    set(newVal,oldVal){
                        if(val!==newVal){
                            val=newVal
                            observe(newVal)
                            dep.notify()  // 发布
                        }
                        return;
                        
                    } 
                })
            }
        }
    }
```

至此完成了数据绑定，即改变数据，视图响应
下一步就是双向数据绑定

响应式原理
如何让输入框和数据联动？
1. 拿到输入框val属性，让其值等于绑定的属性
2. 给输入框添加监听事件，每当值改变时，将值赋给其绑定的属性。


VNode 节点
可以看到，真正的 DOM 元素是非常庞大的，因为浏览器的标准就把 DOM 设计的非常复杂。当我们频繁的去做 DOM 更新，会产生一定的性能问题。

而 Virtual DOM 就是用一个原生的 JS 对象去描述一个 DOM 节点，所以它比创建一个 DOM 的代价要小很多。在 Vue.js 中，Virtual DOM 是用 VNode 这么一个 Class 去描述



Diff 原理及算法 [浅入浅出图解domDIff](https://juejin.im/post/5ad550f06fb9a028b4118d99)



相关链接

[learnVue](https://github.com/answershuto/learnVue)

```
