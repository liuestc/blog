---
title: elementUI 问题
date: 2017-12-13 11:19:02
tags:
---

记下一些值得注意的问题，基本文档都有.

### Table 问题
  #### 某一个单元格设置class

    <el-table :data="inOrder"  @selection-change="selectRow" ref="multipleTable" stripe   :header-row-style='{background:"rgb(229,233,242)",color:"#475669"}' :cell-class-name='hightLight' >

    hightLight({row, column, rowIndex, columnIndex}){
      if(columnIndex==7&&row.ISCHECK==1){
        return 'hightLightBlue'
      }
      if(columnIndex==7&&row.ISCHECK==2){
        return 'hightLightRed'
      }
      if(columnIndex==7&&row.ISCHECK==2){
        return 'hightLightGreen'
      }
    }

        值得注意的地方是`hightLight({row, column, rowIndex, columnIndex})`  ,注意括号里参数

  #### 表格字段 computed

    <el-table-column prop="ISCHECK" label="单据状态" :formatter="formatter">
    </el-table-column>

    formatter(row, column, cellValue){
      console.log("row",row.ISCHECK)
      if(row.ISCHECK=='0'){
        // row.ISCHECK='已录入'
        return "已录入"
      } 
      if(row.ISCHECK=='1'){
        // row.ISCHECK='已录入'
        return "审核中"
      }
      if(row.ISCHECK=='2'){
        return "已驳回"
      }
      if(row.ISCHECK=='3'){
        return "已审核"
      }
    },
        


### 代理配置

    module.exports = {
      dev: {
        // Paths
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
        proxyTable: {
            '/WoodDepot2':{
                target:'http://139.129.118.14:8083',
                changeOrigin: true,
                pathRewrite:{
                    '^/WoodDepot2':'/WoodDepot2'
                }
            }
        },
      }
    }

#### 日期禁用配置

    <el-date-picker  v-model="formInline.date"   type="date"  value-format="yyyy-MM-dd"   placeholder="选择日期" :picker-options='disabledTime'></el-date-picker>

    //  注意这里是data字段，不是方法字段

    disabledTime:{
        disabledDate:(time)=>{
          return time.getTime() > Date.now();
        }
    }
        

#### 关于路由

这是vue中的一些内容，记住编程式是 `this.$router.push({name:"test",params:{id:1}})`，而参数那里是`this.$route.params.id`.watch的时候也是`'$route'`不要把这两搞反了。

>提醒一下，当使用路由参数时，例如从 /user/foo 导航到 user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。

>复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch（监测变化） $route 对象：

[关于为什么不生效](https://router.vuejs.org/zh-cn/essentials/dynamic-matching.html)

通俗来说  `/a/:id` 当这个id变换时，组件是不会调用生命周期方法的，要想操作的话需要`watch`
```
watch:{
    '$route'(to,from){
        //初始化
    }
}
```














