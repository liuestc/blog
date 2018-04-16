---
title: learn-redux
date: 2018-04-16 17:25:10
tags:
---
 
 根据需求实现一个redux。输入需要改变的内容（根据`action`的`type`），通过渲染（比对`newState`和`oldState`），输出结果。

 为什么要通过如此麻烦的`action`修改`state`,因为如果随意修改全局`state`的话会带来很多问题，必须通过`dispatch`来修改。

 具体函数解释

 `createStore(reducer)`: 

   + input:`reducer`函数，`reducer`函数返回根据`action`计算出来的`state`
   + output: 

    1. `getState` 拿到state
    2. `dispatch` 产生新的`state`
    3. `subscribe` 监听`dispatch`函数，`state`改变，执行传入的函数。

 ``
// 定一个 reducer
function reducer (state, action) {
  /* 初始化 state 和 switch case */
}

// 生成 store
const store = createStore(reducer)

// 监听数据变化重新渲染页面
store.subscribe(() => renderApp(store.getState()))

// 首次渲染页面
renderApp(store.getState()) 

// 后面可以随意 dispatch 了，页面自动更新
store.dispatch(...)

```
 function createStore(reducer){
    let state=null
    const listeners=[]
    const subscribe=(listener)=>listeners.push(listener)

    const getState=()=>state
    const dispatch=(action)=>{
        state=reducer(state,action)
        listeners.forEach((listener)=>listener())
    }
    dispatch({})
    return {getState,dispatch,subscribe}
}

function reducer (state,action) {
  if(!state){
    return {
      title: {
        text: 'React.js 小书',
        color: 'red',
      },
      content: {
        text: 'React.js 小书内容',
        color: 'blue'
      }
    }
  }

  switch (action.type) {
    case 'UPDATE_TITLE_TEXT':
      // appState.title.text = action.text
      return{
        ...state,
        title:{
            ...state.title,
            text:action.text
        }
      }

    case 'UPDATE_TITLE_COLOR':
      //appState.title.color = action.color
      return{
        ...state,
        title:{
            ...state.title,
            color:action.color
        }
      }
    default:
      return state
  }
}


function renderApp (newAppState,oldAppState={}) {
  console.log("render app...")
  if(newAppState===oldAppState) return;

  renderTitle(newAppState.title,oldAppState.title)
  renderContent(newAppState.content,oldAppState.content)
}

function renderTitle (newTitle, oldTitle = {}) {
  if (newTitle === oldTitle) return // 数据没有变化就不渲染了
  console.log('render title...')
  const titleDOM = document.getElementById('title')
  titleDOM.innerHTML = newTitle.text
  titleDOM.style.color = newTitle.color
}

function renderContent (newContent, oldContent = {}) {
  if (newContent === oldContent) return // 数据没有变化就不渲染了
  console.log('render content...')
  const contentDOM = document.getElementById('content')
  contentDOM.innerHTML = newContent.text
  contentDOM.style.color = newContent.color
}

const store=createStore(reducer)

let oldState=store.getState()

console.log('oldState',oldState)

store.subscribe(()=>{
    const newState=store.getState()
    console.log('newState',newState)
    renderApp(newState,oldState)
    oldState=newState
})


// 注意store.subscribe 接收的是一个函数 store.subscribe(renderApp(store.getState())  这样是错的，这是传入了一个结果
// 
renderApp(store.getState()) // 首次渲染页面
// render app render title render content

store.dispatch({ type: 'UPDATE_TITLE_TEXT', text: '《React.js 小书!!!》' }) // 修改标题文本

store.dispatch({ type: 'UPDATE_TITLE_COLOR', color: 'red' }) // 修改标题颜色
```