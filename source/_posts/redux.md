---
title: react-redux理解
date: 2017-11-14 14:13:23
tags: react
category: "react"  # 分类为 react
---

用react-redux的好处 想必较 直接使用redux
他不用处理dispatch  通过connect挂载到所需组件的props上 方法直接有了dispatch
通过provider给所有组件传入store 不用store.subscribe(render) 去做订阅了


### 总体说明
1.应用中所有的 state 都以一个对象树的形式储存在一个单一的 store 中。

2.惟一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象
改变内部 state 惟一方法是 dispatch 一个 action。

3.为了描述 action 如何改变 state 树，你需要编写 reducers。

4.reducer，形式为 (state, action) => state 的纯函数。描述了 action 如何把 state 转变成下一个 state。

5.Action 本质上是 JavaScript 普通对象。我们约定，action 内必须使用一个字符串类型的 type 字段来表示将要执行的动作。多数情况下，type 会被定义成字符串常量。

6.同步不用手动dispatch react-redux帮我们做了，
  我目前认为的是在组件内部挂载到connect下。同步方法就给你加了dispatch了见下边的this.props.addGun

  异步需要手动的dispatch  不管同步异步 都要通过dispatch修改action 只有action能告诉reducer 如何改变state








### index.js入口页面
```
import {createStore,applyMiddleware} from 'redux';
import {createStore,applyMiddleware} from 'redux';
//npm install redux-thunk -save  
import thunk from 'redux-thunk' //applyMiddleware中间件，redux-thunk处理异步获取
import combineReducers from './reducer' //负责合并reducer
import {Provider} from 'react-redux' //provider 负责把STORE传入到组件中
```
applyMiddleware，redux-thunk都是处理异步的


#combineReducers   reducer.js
```
//合并所有reducer 并返回

import {combineReducers} from 'redux'
import {counter} from './index.redux'
import {auth} from './Auth.redux'

export default combineReducers({counter,auth})

```


#两个reducer index.redux.js
1.加减机关枪
```
const ADD_GUN = '加机关枪'
const REMOVE_GUN = '减机关枪'

// 这个东西就是reducer
export function counter(state = 0 , action){
    switch(action.type){
        case ADD_GUN:
            return state + 1
        case REMOVE_GUN:
            return state - 1
        default:
            return 10
    }
}


//action 同步的action 这里不用dispatch了 页面调用的时候

export function addGun(){
    return {type:ADD_GUN}
}

export function removeGun(){
    return {type:REMOVE_GUN}
}

//这就是异步了  需要手动触发dispatch
export function addGunAnsyc(){
    return dispatch=>{
        setTimeout(() => {
            dispatch(addGun())
        }, 2000)
    }
}

```


2.Auth.redux.js
```
const LOGIN = 'LOGIN'
const LOGOUT = 'LOGOUT'

export function auth(state = {isAuth:false,user:'李云龙'},action){ 
    switch(action.type){
        case LOGIN:
            return {...state,isAuth:true}
        case LOGOUT:
            return {...state,isAuth:false}
        default :
            return state
    }
}


//action
export function login(){
    return {type:LOGIN}
}

export function logout(){
    return {type:LOGOUT}
}

```


# index.js 页面创建 store
```
//如果不处理异步 直接const store = createStore(counter)就可以了
//这里用了combineReducer  合并了auth.redux.js index.redux.js里两个reducer
const store = createStore(combineReducers,applyMiddleware(thunk))

```

#通过provider将STORE传入根组件  下面所有的组件就都有了store

```
ReactDOM.render(

//使用react-redux 方便了很多 Provider只在入口页面写一次 负责传入store 也不需要subscribe订阅了

        (<Provider store={store}>
            <BrowserRouter>
              <div>
                  <Switch>
                      {/*只命中匹配上的第一个route*/}
                      <Route exact path='/Auth' component={Auth}></Route>
                      <Route path='/Dashbord' component={Dashbord}></Route>                   
                      <Redirect to='/Dashbord'></Redirect>
                  </Switch>
              </div>
            </BrowserRouter>
         </Provider>),        
        document.getElementById('root')
)
```



#组件调取store   通过connect 将本组件使用到的store里的属性 和 方法挂载到本组件

```
import {connect} from 'react-redux'
@connect(
    state =>({num:state.counter}),
    {addGun,removeGun,addGunAnsyc}
)


render() {
   
    return (
        <div>
            <Button type="primary" size="small" onClick={this.props.addGun}>申请武器</Button>
            <Button type="primary" size="small" onClick={this.props.removeGun}>上交武器</Button>
            <Button type="primary" size="small" onClick={this.props.addGunAnsyc}>拖两天再给</Button>

            <h1>现在有机枪{this.props.num}把</h1>
        </div>
    )
  }


```

this.props.num 因为通过connect 把state.counter放到this.props.num上了

同理其他方法 也挂载到props里了
