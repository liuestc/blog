---
title: VUE大数据渲染卡顿问题&&权限判定&&文件引入
date: 2019-10-14 21:06:17
tags:
---

最近遇到一个项目优化问题，关于big Data在项目中渲染问题的，场景是后端返回了一个JSON对象，需要将这个JSON转成一棵树，并选择某个节点。用了iview的Tree组件，但是发现当页面这个树的结构达到1000左右的时候，页面巨卡无比。perfermance里看了一下，Scripting高达十几秒，然后一层一层的看callTree,发现是更新树结构时，层次巨深无比，每一层都要patchNode,不卡才怪。

so  解决问题分两步：

1. 拍平obj的树形结构，将以前的嵌套结构改为扁平list,树的样子用style画出来
2. virtual-list 只展示视图内一部分list

over

代码整理下贴

----

另一个权限管理问题

经常遇到的场景是，按钮根据角色或者权限显示与否

我们可以用指令来控制

```js
Vue.directive('permission', {
  bind: function (el, binding, vnode) {
    let value=binding.value
    const permissionList=[1,2,3,4]
    let hasPermission=permissionList.includes(value)
    if(!hasPermission){
      el && el.parentNode 
      el.parentNode.removeChild(el)
      //el.disabled=true
    }
  },
  update:function (el, binding, vnode) {
  },
})
```

文件引入问题

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
