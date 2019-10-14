---
title: flutter入门
date: 2019-05-27 10:35:18
tags:
---

本文记录一些学习过程中的疑问

### 语法

1. override 关键字

    @override just points out that the function is also defined in an ancestor class, but is being redefined to do something else in the current class. It's also used to annotate the implementation of an abstract method. It is optional to use but recommended as it improves readability.
  其实没什么作用，子类覆盖父类同名方法，提高程序可读性。
  
2. ss
3. ss
4. www
5. www



### Widget 组件

1. scaffold
2. 记住状态

###  其他

1. model
类似于js中的构造方法，
````
class CategoryBigModel{
  String mallCategoryId;
  String mallCategoryName;
  List<dynamic> bxMallSubDto;// dynamic 表示任何可能的复杂类型对象
  String image;
  Null comments;
  CategoryBigModel({
    this.mallCategoryId,
    this.mallCategoryName,
    this.comments,
    this.image,
    this.bxMallSubDto
  });

  factory CategoryBigModel.fromJson(dynamic json){

    return CategoryBigModel(
        mallCategoryId:json['mallCategoryId'],
        mallCategoryName:json['mallCategoryName'],
        comments:json['comments'],
        image:json['image'],
        bxMallSubDto:json['bxMallSubDto']
    );

  }
}
````
先定义属性，再初始化,factory开头的相当于方法

2. provide 注入顶层写法

```
    void main(){
    var providers  =Providers();
    var childCategory=ChildCategory();
    providers
    ..provide(Provider<ChildCategory>.value(childCategory));
    runApp(ProviderNode(child:MyApp(),providers:providers));
    }
```

改变数据：

```
    var childList = list[index].bxMallSubDto;
  
    Provide.value<ChildCategory>(context).getChildCategory(childList);
```

渲染数据要用provider包裹


3. 路由相关



4. FutureBuilder，

监听请求，等请求状态为true时，渲染页面。

遇到的问题：直接返回request 对象时是对的，但是丢到then里就错了，还是对async/await 不熟悉。

```
return await request(url); // right

await request(url).then((val){
    return val
    })  // wrong
```


### 请求

[Dio](https://github.com/flutterchina/dio/blob/master/README-ZH.md)库


完整发起请求，渲染页面流程
```
request(sendData).then((data){
    setState((){
      pageData=data.xx.toString()
    })
})
```

// 列表序列化 (json.decode(cartString.toString()) as List).cast()

### JSON 序列化

[json序列化](https://flutterchina.club/json/#manual-serialization)

反序列化-fromJson 即字符串转为dart对象，类比JS中JSON.parse方法

序列化-toJson 与上面相反

步骤

1. json 转 Model [有相关工具](https://javiercbk.github.io/json_to_dart/)
2. 反序列化 newModel=testModel.formJson(json.decode(res.data))
3. newModel.property


### 模型层
