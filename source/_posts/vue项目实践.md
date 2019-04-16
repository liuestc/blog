---
title: 项目实战问题
date: 2018-11-27 16:03:47
tags:
categories: vue
---
总结一些vue项目中遇到的问题

### 菜单点击刷新
正常情况下,spa框架点击两次菜单是不会刷新的，如何实现此需求?通过query改变去刷新

```
clickLink(path) {
  this.$router.push({
    path,
    query: {
      t: +new Date() //保证每次点击路由的query项都是不一样的，确保会重新刷新view
    }
  })
}
```

>ps:不要忘了在 router-view 加上一个特定唯一的 key，如 <router-view :key="$route.path"></router-view>， 但这也有一个弊端就是 url 后面有一个很难看的 query 后缀如 xxx.com/article/list?t=1496832345025

问题，这样URL会比较难看

如何解决

>在公司项目中，现在采取的方案是判断当前点击的菜单路由和当前的路由是否一致，但一致的时候，会先跳转到一个专门 Redirect 的页面，它会将路由重定向到我想去的页面，这样就起到了刷新的效果了。


***

### 树形菜单

也不是树形菜单吧，应该描述为多层级菜单，支持无限嵌套

思路是用递归组件


### 异步请求问题

一般详情页面先请求列表，再请求详情，可以把列表放到 process.all中，等所有请求执行完再请求详情

```
Promise.all([list1,list2]).thne((val1,val2)=>{
    getDetail()
    })
```

### 循环退出问题

### nextTick()

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

例子1：
点击按钮显示原本以 v-show = false 隐藏起来的输入框，并获取焦点。
```
showsou(){
  this.showit = true //修改 v-show
  document.getElementById("keywords").focus()  //在第一个 tick 里，获取不到输入框，自然也获取不到焦点
}
//修改为：
showsou(){
  this.showit = true
  this.$nextTick(function () {
    // DOM 更新了
    document.getElementById("keywords").focus()
  })
}
```

例子2：

点击获取元素宽度。
```
<div id="app">
    <p ref="myWidth" v-if="showMe">{{ message }}</p>
    <button @click="getMyWidth">获取p元素宽度</button>
</div>
getMyWidth() {
    this.showMe = true;
    //this.message = this.$refs.myWidth.offsetWidth;
    //报错 TypeError: this.$refs.myWidth is undefined
    this.$nextTick(()=>{
        //dom元素更新后执行，此时能拿到p元素的属性
        this.message = this.$refs.myWidth.offsetWidth;
  })
}
```

***
决策服务平台：

1. 登录&&权限验证
  菜单：在登陆接口中拿到菜单，然后直接渲染
  按钮权限：也是从登录信息中拿
  该系统中所有权限都由后端控制，所以前端逻辑比较简单，不用做过多权限限制。这样做的原因是菜单可以根绝用户灵活配置。
  
2. 页面状态管理
  主要是按钮状态，通过上页面传参得到，然后在computed中得到最终结果。

3. 树形表
