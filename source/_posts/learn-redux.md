---
title: learn-redux
date: 2018-04-16 17:25:10
tags:
---
 >一个状态可能被多个组件依赖或者影响，而 React.js 并没有提供好的解决方案，我们只能把状态提升到依赖或者影响这个状态的所有组件的公共父组件上，我们把这种行为叫做状态提升。但是需求不停变化，共享状态没完没了地提升也不是办法。
 后来我们在 React.js 的 context 中提出，我们可用把共享状态放到父组件的 context 上，这个父组件下所有的组件都可以从 context 中直接获取到状态而不需要一层层地进行传递了。但是直接从 context 里面存放、获取数据增强了组件的耦合性；并且所有组件都可以修改 context 里面的状态就像谁都可以修改共享状态一样，导致程序运行的不可预料。
 既然这样，为什么不把 context 和 store 结合起来？毕竟 store 的数据不是谁都能修改，而是约定只能通过 dispatch 来进行修改，这样的话每个组件既可以去 context 里面获取 store 从而获取状态，又不用担心它们乱改数据了

 根据需求实现一个redux。输入需要改变的内容（根据`action`的`type`），通过渲染（比对`newState`和`oldState`），输出结果。

 为什么要通过如此麻烦的`action`修改`state`,因为如果随意修改全局`state`的话会带来很多问题，必须通过`dispatch`来修改。

 具体函数解释

 `createStore(reducer)`: 

   + input:`reducer`函数，`reducer`函数返回根据`action`计算出来的`state`
   + output: 

    1. `getState` 拿到state
    2. `dispatch` 产生新的`state`
    3. `subscribe` 监听`dispatch`函数，`state`改变，执行传入的函数。


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