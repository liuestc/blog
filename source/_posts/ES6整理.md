---
title: ES6整理
date: 2019-03-06 10:58:01
tags:
---

### let,const

`let`:
1. 仅在块级作用域内生效（以前作用域通过闭包实现）
2. 不存在声明提升，不能重复声明

`const`
不能修改的常量

****
经典例子

```
    for(var i=0;i<3;i++){
        setTimeout(()=>{
            console.log(i)
        })
    }
```

以上例子涉及事件循环机制，具体参见另一篇

将`var`改成`let`可以完美输出012

经过编译成ES5之后的代码如下

```
"use strict";

var _loop = function _loop(i) {
    setTimeout(function () {
        console.log(i);
    });
};

for (var i = 0; i < 3; i++) {
    _loop(i);
}
```

其实就是闭包的写法

```
    for(var i=0;i<3;i++){
        ((i)=>{
            setTimeout(()=>{
                console.log(i)
            })
        })(i)
    }
```

### 解构，析构

1、解构

```
let arr=[1,2,3]
let [a,b,c]=arr
//

let obj={
    name:'ljd',
    //age:24
}

```

### 函数

1. 默认参数

```
function ajax(url=new,method='GET',dataType='json'){

}
```

2. 展开操作符
3. 剩余参数

### 类

本质是构造函数的语法糖。
类的所有方法都定义在类的prototype属性上面。

类的方法内部如果含有this，它默认指向类的实例。

```
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

****

+ `constructor`
constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
`constructor`初始化属性，相当于一起的this.a=a这种的

+ `static`
类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。
```
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

如果静态方法包含this关键字，这个this指的是类，而不是实例。

```
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}

Foo.bar() // hello
```

#### 继承

`super`:

>子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。

```
class Point { /* ... */ }

class ColorPoint extends Point {
  constructor() {
  }
}

let cp = new ColorPoint(); // ReferenceError
```

类中的方法就是原型上的方法，前面如果用`static`修饰，则是静态方法

***


###  生成器、迭代器
