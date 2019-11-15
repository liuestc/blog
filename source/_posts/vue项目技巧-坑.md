---
title: vue项目技巧&坑
date: 2019-10-17 15:38:54
tags:
---

坑：

1. iview 中 select 清除赋值赋值不能直接 `this.bindValue=''` 这样只是清空了值而未清空搜索条件，导致option只有一项
    解决方法：`this.$refs.el.setQuery(null)`

2. v-for 循环出来的元素，当涉及到上移下移删除时，不能用index当key，会出现混乱，原因是vue内部patch时是根据index对比的，上下移动index变没有变化，可能会导致渲染不更新。
    解决方法：:key=item.uuid

3. 文件上传组件Upload的坑 可以手动上传，注意设置

    常见的 `content-type:`
        1. `application/json` json格式
        2. `application/x-www-form-urlencoded` <form encType=””>中默认的encType，form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）
        3. `multipart/form-data` 需要在表单中进行文件上传时，就需要使用该格式

    [文件上传](https://juejin.im/post/5da14778f265da5bb628e590)

4. Select组件中绑定的数据有 `\"`反斜杠转义，然后搜索，会出现选不中的情况,[问题](https://run.iviewui.com/6qq7Qxox)
    原因：先搜索再选中，在on-change事件里发现打印了两次val,第一次打印正确的值，第二次打印了`undefined`,思考只用把第一次的值存起来，如果是undefined的话就取缓存下来的值，但是clear的时候也是undefined，需要排除掉clear的时候

    具体解决方法:

    ```js
        getValue(value){
            if(value===undefined&&!this.isClear){
            //bug分支
            this.test=this.lastValue
            }
            else{
            this.lastValue=value
            }
        },
    ```

---

技巧：

1. "listeners" 传入内部组件——在创建更高层次的组件时非常有用

    ```js
    // 父组件
    <home @change="change"/>
    // 子组件
    mounted() {
    console.log(this.$listeners) //即可拿到 change 事件
    }
    ```

2. provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中; 并且这对选项需要一起使用; 以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

    ```js
    provide: { //provide 是一个对象,提供一个属性或方法
    foo: '这是 foo',
    fooMethod:()=>{
        console.log('父组件 fooMethod 被调用')
    }
    },

    // 子或者孙子组件
    inject: ['foo','fooMethod'], //数组或者对象,注入到子组件
    mounted() {
    this.fooMethod()
    console.log(this.foo)
    }
    //在父组件下面所有的子组件都可以利用inject
    ```
