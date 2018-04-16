---
title: learn-react
date: 2018-04-02 18:18:44
tags:
---

学习下react相关，主要源于这里
[react小书](http://huziketang.com/books/react/lesson2)
[从0开始搭建react全家桶](https://github.com/brickspert/blog/issues/1)

因为是别人的教程，最好代码自己过一遍，记录所遇到的问题。

1. JSX语法中用`className`
2. 事件监听 `onClick={this.handleEvent}`
3. 事件监听的`this`.直接在事件中打印`this`是木有结果的。如果想绑定`this`，`onClick={this.handleEvent.bind(this)}`
4. `setState`是异步的，如何变同步。`setState`可以接受一个函数

```
  handleClickOnLikeButton () {
    this.setState((prevState) => {
      return { count: 0 }
    })
    this.setState((prevState) => {
      return { count: prevState.count + 1 } // 上一个 setState 的返回是 count 为 0，当前返回 1
    })
    this.setState((prevState) => {
      return { count: prevState.count + 2 } // 上一个 setState 的返回是 count 为 1，当前返回 3
    })
    // 最后的结果是 this.state.count 为 3
  }
```

5. 事件要传入值的话，这样写`this.handleEvent.bind(this,params1,params2)`

```
class Lesson extends Component {
  /* TODO */
  constructor(){
    super()
  }
  alertInfo(lesson,index){
    console.log(index+" - "+lesson.title)
  }
  render(){
    const lesson=this.props.lesson
    const index=this.props.index
    console.log(lesson)
    
    return(
      <div onClick={this.alertInfo.bind(this,lesson,index)}>
        <h1 >{ lesson.title }</h1>
        <p>{ lesson.description }</p>
      </div>      
      )
  }
}
```

6.  `<Lesson lesson={item} key={index} index={index} onClick={this.info.bind(this)}/>`  不能这么写，拿不到事件，事件定义到最底层组件中。
7.  `this.state`放到`constructor()`里面。
8.  不依赖 DOM 操作的组件启动的操作都可以放在 componentWillMount 中进行




***


`redux`相关

`state` 数据

`action` 更新`state`必须通过`action`. `action`为一个普通对象，里面type属性对应相应操作。

