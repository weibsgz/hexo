---
title: react-redux开发登录校验
date: 2017-11-16 14:13:23
tags: react
category: "react"  # 分类为 react
---

1.基础原理详见上一篇 react-redux 理解
2.分为两个页面 auth.js是登录页 Dashbord是主页 ，默认不输入登录页地址的话直接到dashbord.js做验证，没有登录的话去auth.js


### index.js入口页面 引用 
```
import {createStore,applyMiddleware} from 'redux';
//npm install redux-thunk -save  
import thunk from 'redux-thunk' //applyMiddleware中间件，redux-thunk处理异步获取
import combineReducers from './reducer'  //涉及到合并reducer 不合并的话可以不用
import {Provider} from 'react-redux'
//路由
import {BrowserRouter,Route,Redirect,Switch} from 'react-router-dom'
//分为登录页和主页  做权限校验
import Auth from './Auth.js'
import Dashbord from './Dashbord'
```

### index.js 页面逻辑 默认去dashbord页面

```
//如果不处理异步 直接const store = createStore(counter)就可以了
//这里用了combineReducer  合并了auth.redux.js index.redux.js里两个reducer
const store = createStore(combineReducers,applyMiddleware(thunk))

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

### Auth.redux.js 
```
import axios from 'axios'

const LOGIN = 'LOGIN'
const LOGOUT = 'LOGOUT'
const USERDATA = 'USERDATA'
const init = {
   // isAuth:false,
    user:'李云龙',
    age:20
}

/**
 * 这是一个 reducer，形式为 (state, action) => state 的纯函数。
 * 描述了 action 如何把 state 转变成下一个 state。**/
export function auth(state = init ,action){ 
    console.log('auth.redux.js里的 state ： ' + JSON.stringify(state))
    console.log('auth.redux.js里的 action ： ' + JSON.stringify(action))
    switch(action.type){
        case LOGIN:
            return {...state,isAuth:true}
        case LOGOUT:
            return {...state,isAuth:false}
        case USERDATA:
           return {...state,user:action.payload.user,age:action.payload.age}
        default :
            return state
    }
}

//惟一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象
//// 改变内部 state 惟一方法是 dispatch 一个 action。
//action
export function login(){
    return {type:LOGIN}
}

export function logout(){
    return {type:LOGOUT}
}

//可传入一个载荷 将在reducer的action中体现
export function userData(data)
{
    return {type:USERDATA,payload:data}
}
//建立一个异步的action 因为数据传输是异步的 需要手动dispatch
export function getUserData(){
    return dispatch=>{
        axios.get('/data').then(res=>{
            if(res.status === 200){
                dispatch(userData(res.data))
            }   
        })
    }
}
```



### Dashbord.js 进入这个页面 要做用户是否登录的判断，如果没登录跳转到auth.js页面

1.通过connect state.auth和 logout方法挂载到当前组件的this.props下了
2.默认this.props.isAuth肯定是没有定义的 this.props.isAuth ? app : redirectToLogin
```
import React, { Component } from 'react';
//路由
import {Route,Link,Switch,Redirect} from 'react-router-dom'
import App from './App';
import {connect} from 'react-redux'
import {logout} from './Auth.redux'

@connect(
     state=>state.auth,
    {logout} 
)

class Dashbord extends Component{

    render(){
        console.log(this.props)

        const redirectToLogin = <Redirect to='/Auth'></Redirect>
        const match = this.props.match;
        const app = (
          <div>
          {this.props.isAuth?<button onClick={this.props.logout}>注销</button> : null}
              <ul>
                  <li>
                      <Link to={`${match.url}/`}>一营</Link>
                  </li>
                  <li>
                      <Link to={`${match.url}/erying`}>二营</Link>
                  </li>
                  <li>
                      <Link to={`${match.url}/qibinglian`}>骑兵连</Link>
                  </li>
              </ul>  
              <Switch>
                  <Route path='/Dashbord/' exact component={App}></Route>
                  {/*<Route path='/Dashbord/erying' component={Erying}></Route>
                  <Route path='/Dashbord/qibinglian' component={Qibinglian}></Route>*/}
              </Switch>
          </div>
        )


        return this.props.isAuth ? app : redirectToLogin
    }
}
export default Dashbord
```



### Auth.js

```
import React, { Component } from 'react';
import {connect} from 'react-redux'
// import {connect} from 'react-redux'
// import {addGun,addGunAnsyc,removeGun} from './index.redux'
import {login,getUserData} from './Auth.redux.js'
import {Redirect} from 'react-router-dom'


//通过connect state属性 和 方法 都挂载到props
//这里挂了state.auth  所以下面可以通过this.props.isAuth 取得用户是否登录
@connect(
    state => state.auth,
    {login,getUserData}
)

class Auth extends Component{
    constructor(props){
        super(props)
        this.state = {
            data:{}
        }
    }
    componentWillMount(){
        
        this.props.getUserData()
        console.log(this.props)
        // axios.get('/data').then(res=>{
        //         if(res.status == 200){
        //             this.setState({
        //                 data:res.data
        //             })
        //         }   
                
        //         console.log('state.data ',JSON.stringify(this.state.data.user,null,2))  
        // })
    }
    render(){
        console.log('auth页面的props' + JSON.stringify(this.props))

        return (
            <div>
                {/*<h2>我的名字是{this.state.data.user}</h2>*/}
                <h2>用户名：{this.props.user},年龄:{this.props.age}</h2>
                
                {this.props.isAuth ? <Redirect to='/Dashbord' /> : <h2>你没有权限，需要登录才能看</h2>}

                <button onClick={this.props.login}>登录</button>
            </div>
            
        )
    }
}
export default Auth

```
