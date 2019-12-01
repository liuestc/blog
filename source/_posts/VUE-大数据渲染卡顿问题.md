---
title: VUE大数据渲染卡顿问题
date: 2019-10-14 21:06:17
tags:
---


最近遇到一个项目优化问题，两个问题，1.加载慢 2. 表格操作时卡顿

#### 加载慢

太多的请求 `initOptions`，都是`Select`里的选项，未缓存

1. 合并请求
2. options存入`store`

#### 表格操作卡顿

关于big Data在项目中渲染问题的，场景是后端返回了一个JSON对象，需要将这个JSON转成一棵树，并选择某个节点。用了iview的Tree组件，但是发现当页面这个树的结构达到1000左右的时候，页面巨卡无比。perfermance里看了一下，Scripting高达十几秒，然后一层一层的看callTree,发现是更新树结构时，层次巨深无比，每一层都要patchNode,不卡才怪。

总而言之

+ `DOM` 层级过深
+ 频繁触发`Vue.prototype._update`更新
+ table的标签太重，input->span

so  解决问题分两步：

1. 拍平obj的树形结构，将以前的嵌套结构改为扁平list,树的样子用style画出来
2. virtual-list 只展示视图内一部分list

over

代码整理下贴

----
