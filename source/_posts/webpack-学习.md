---
title: webpack 学习
date: 2017-12-12 15:32:30
tags:
---

webpack 例子
[官网首页](https://doc.webpack-china.org/)

1. `webpack.config.js`  配置文件写在这里，这个文件这么写

    ```
    module.export={
        entry:'./app.js',
        output:{
            filename:'bundle.js'
        }
    }
    ```

2. 注意`webpack.config.js`里面四部分值得注意
    + entry  入口文件
    + output 出口文件
    + loader
    + plugins
