---
title: JS核心概念相关
date: 2018-03-12 12:16:42
tags:
---

###原型：

三个概念

构造函数 实例 原型

构造函数下有 `prototype`属性，指向原型,对象是没有 `prototype`这个属性的

每个对象都有其原型，原型上有`constructor`属性，指向其自身函数

实例是构造函数生成出来的，所有实例的 `_proto_`属性都指向函数的原型

```js
function Person(){}
let person1=new Person()
let person1=new Person()
Person.prototype===person1.__proto__
person1.__proto__===person2.__proto__
```

一些方法

`obj.hasOwnProperty(key)` :对象中是否有某个属性，不对遍历该对象的原型
`hasPrototypeProperty(obj,property)`  只看原型中是否存在某个属性
`in` 操作会遍历实例和原型  `for in`循环遍历所有属性，包括实例和原型中的熟悉感
`instanceof` 是不是某构造函数下的实例

### 作用域相关

js 是词法作用域，函数在声明时就确定了其作用域
以下例子

```js
var value = 1;
function foo() {
    console.log(value);
}
function bar() {
    var value = 2;
    foo();
}
bar();
```

这个例子会打印1，因为调用bar里的foo,foo的作用域是在声明时拿到的

再来一个例子

```js
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();

```

另一个例子

```js
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```

这两分别打印什么？都是`local scope`了,再次验证函数作用域是在声明时确定的

这两个函数的区别是什么？

引入下一个问题，执行上下文，

函数执行时会创建一个执行上下文

为了模拟执行上下文栈的行为，让我们定义执行上下文栈是一个数组：

ECStack = [];
试想当 JavaScript 开始要解释执行代码的时候，最先遇到的就是全局代码，所以初始化的时候首先就会向执行上下文栈压入一个全局执行上下文，我们用 globalContext 表示它，并且只有当整个应用程序结束的时候，ECStack 才会被清空，所以程序结束之前， ECStack 最底部永远有个 globalContext：

```js
ECStack = [
    globalContext
];
//现在 JavaScript 遇到下面的这段代码了：
function fun3() {
    console.log('fun3')
}

function fun2() {
    fun3();
}

function fun1() {
    fun2();
}

fun1();
//当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。知道了这样的工作原理，让我们来看看如何处理上面这段代码：
// 伪代码

// fun1()
ECStack.push(<fun1> functionContext);

// fun1中竟然调用了fun2，还要创建fun2的执行上下文

ECStack.push(<fun2> functionContext);

// 擦，fun2还调用了fun3！
ECStack.push(<fun3> functionContext);

// fun3执行完毕
ECStack.pop();

// fun2执行完毕
ECStack.pop();

// fun1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContext

```

对于每个执行上下文，都有三个重要属性：

1. 变量对象(Variable object，VO)
2. 作用域链(Scope chain)
3. this

重要问题说三遍

1. 变量对象 VO

    先会扫描一遍函数，进行一些初始化，形参会被赋值，其他的函数内变量会被赋值为`undefined`
    比如上面那个函数的VO如下

    ```js
        AO: {
            arguments: {
                length: 0
            },
            scope: undefined,
            f: reference to function f(){}
        },
    ```

2. 作用域链 Scope

    >因为函数有一个内部属性 [[scope]]，当函数创建的时候，就会保存所有父变量对象到其中，你可以理解 [[scope]] 就是所有父变量对象的层级链，但是注意：[[scope]] 并不代表完整的作用域链！

    ```js
    function foo() {
        function bar() {
            ...
        }
    }
    ```

    函数创建时，各自的[[scope]]为：

    foo.[[scope]] = [
    globalContext.VO
    ];

    bar.[[scope]] = [
        fooContext.AO,
        globalContext.VO
    ];

    具体看这里：

    [scope](https://github.com/mqyqingfeng/Blog/issues/6)

3. this

### 闭包

老生常谈的话题，每次看都会有不同理解，还是很经典的那个例子

```js
    for(var i=0;i<list.length;i++){
        list[i].addEventListener('click',function(){
            console.log(i)
        })
    }
```

怎么打印都是最后一个

也可以用上面的知识理解

点击即执行`list[i]()` 但是list[i]的函数却是下面这个

```js
list[0]=function(){
    console.log(i)
}
```

执行 `list[i]()` 时他的作用域链是 `[AO,globalContext]` AO中没有i,去全局中找，全局中现在已经是3了，所以打印3

如何修改

1. let

    用let 相当于做了一次代码分块

    ```js
        printI=function(i){
            list[i].addEventListener('click',function(){
                console.log(i)
            })
        }
        for(var i=0;i<list.length;i++){
            printI(i)
        }
    ```

2. 匿名函数

    ```js
        for(var i=0;i<list.length;i++){
            list[i].addEventListener('click',(function(i){
                return function(){
                    console.log(i)
                }
            })(i))
        }
    ```

### call和apply的实现

>call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。

```js
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call(foo); // 1
```

call 改变了 this 的指向，指向到 foo
bar 函数执行了

```js

Function.prototype.myCall=function(){
    // console.log('arguments',typeof arguments)
    var context=arguments[0]||window
    context.fn=this
    var result=context.fn(...Array.from(arguments).slice(1))
    delete context.fn
    return result
}
```

要是不能用ES6可以用eval实现

```js
Function.prototype.call2 = function (context) {
    var context = context || window;
    context.fn = this;

    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    // 这里args会默认调用toString()
    var result = eval('context.fn(' + args +')');

    delete context.fn
    return result;
}
```

apply也类似，代码就不贴了

### bind 手动实现

### new 的实现