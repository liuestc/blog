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

5. 巨坑
    [和这个一模一样](https://blog.csdn.net/weixin_42142057/article/details/97655591)
    场景：下载文件后重命名

    多简单，`responseType:blob` ,然后调用download方法不就行了啊

    ```js
    axios({
        method: 'get',
        url: this.url,
        responseType: 'blob',
    }).then(res => {
        downloadFile(res, this.download)
    })
    // 下载方法
    export const downloadFile = (data, fileName = '') => {
    if (data.data.type === 'application/json' || data.data.type === 'text/html;charset=utf-8') {
        let reader = new FileReader()
        reader.readAsText(data.data)
        reader.onload = function (res) {
        iView.Notice.error({
            title: '操作失败',
            desc: JSON.parse(res.target.result).message,
            duration: 0
        })
        }
    } else {
        let contentDisposition = data.headers['content-disposition'].split(';')
        let fileSuffix = ''

        for (let i = 0; i < contentDisposition.length; i++) {
        let item = contentDisposition[i]
        item = item.replace(/\"/g, '')

        if (item.includes('filename=')) {
            let temp = item.split('.')
            fileSuffix = temp[temp.length - 1]
            break
        }
        }

        if (!fileSuffix) {
        iView.Notice.error({
            title: '操作失败',
            desc: '下载失败',
            duration: 0
        })
        }

        let blob = new Blob([data.data], { type: 'application/pdf' })
        // console.log(blob, blob)
        let link = document.createElement('a')
        link.href = window.URL.createObjectURL(blob)
        link.download = fileName + '.' + fileSuffix
        document.body.appendChild(link)
        link.click()
        document.body.removeChild(link)
    }
    }
    ```

    后端不用设置什么，前端的设置 `responseType:blob` 就行，打印下这个

    ![blob](https://i.loli.net/2019/11/29/vYUeB21VpJXtKSC.png)

    返回的不是blob就排查原因

    [修改文件名](https://www.jianshu.com/p/6545015017c4)

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

3. 权限管理问题

    经常遇到的场景是，按钮根据角色或者权限显示与否

    我们可以用指令来控制

    ```js
    Vue.directive('permission', {
    bind: function (el, binding, vnode) {
        let value=binding.value
        const permissionList=[1,2,3,4]
        let hasPermission=permissionList.includes(value)
        if(!hasPermission){
        el && el.parentNode ;
        el.parentNode.removeChild(el)
        //el.disabled=true
        }
    },
    update:function (el, binding, vnode) {
    },
    })
    ```

4. 文件引入问题
    vue 项目中如需要全局注册一些组件，通常就是 先 import 然后再在 components 里再注册，组件多了这样比较麻烦，可以利用webpack的 require.context引入并注册文件。

    > 可以使用 require.context() 方法来创建自己的（模块）上下文，这个方法有 3 个参数：要搜索的文件夹目录，是否还应该搜索它的子目录，以及一个匹配文件的正则表达式。

    ```js
    const path = require('path')
    const files = require.context('@/components/home', false, /\.vue$/)
    const modules = {}
    files.keys().forEach(key => {
    const name = path.basename(key, '.vue')
    modules[name] = files(key).default || files(key)
    })
    components:modules
    ```

5. 继发和并发

    常见需求是先初始化optionsList，再请求详情，如何设计？

    1. promise.all

    ```js
    await Promise.all([
      this.$store.dispatch('getUserList'),
      this.$store.dispatch('apiListByType', 'db_account_apply_status'),
      this.$store.dispatch('apiListByType', 'db_account_apply_type')
    ])
    ```

    当然由于是dispatch更新store，不返回什么，所以要加 await 才会和 `getList()` 异步

    `promise.all` 存在的问题, 我得手动在里面加，如果我想用循环呢？

    ```js
    async initOptionList () {
      for (let key in this.optList) {
        await this.$store.dispatch('apiListByType', key)
        this.optList[key] = this.$store.state.options[key]
      }
    }
    ```

    上面这个函数是继发执行的？如何修改？

    ```js
    async initOptionList () {
      let awaitList = []
      for (let key in this.optList) {
        awaitList.push(this.$store.dispatch('apiListByType', key))
      }
      for (let item of awaitList) {
        await item
      }
      for (let key in this.optList) {
        this.optList[key] = this.$store.state.options[key]
      }
      this.getList()
    }
    ```

    写了三个循环，好像也不怎么样，就这样吧，好像也没别的好办法了

    [await继发和并发](https://www.cnblogs.com/xbblogs/p/8946912.html)