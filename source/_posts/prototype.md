---
title: prototype
date: 2018-03-12 12:16:42
tags:
---

整理关于原型的一些知识

一些方法

`obj.hasOwnProperty(key)` :对象中是否有某个属性，不对遍历该对象的原型
`hasPrototypeProperty(obj,property)`  只看原型中是否存在某个属性
`in` 操作会遍历实例和原型  `for in`循环遍历所有属性，包括实例和原型中的熟悉感
`instanceof`