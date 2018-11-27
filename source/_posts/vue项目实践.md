---
title: vue项目实践
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