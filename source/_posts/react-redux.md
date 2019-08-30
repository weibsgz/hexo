---
title: react-redux使用
date: 2019-08-30 14:13:23
tags: react
category: "react"  # 分类为 js
---



### 使用react-redux需要安装的插件
`cnpm i -S redux react-redux react-thunk`

page -> Action -> Store -> Reducer ->Store -> page

+  根文件需要引用 Provider 来给全局组件挂载store
+ 业务组件使用需要connect导出组件和全局STORE做连接，业务组件通过mapStateToProps，mapDispatchToProps，可以直接取到STORE中的state和dispatch你的action
+ 各个业务线都可以建立自己的STORE文件夹，所有导出的reducer 通过 combinReducer在根路径下
的store/reducer中联合使用
+ 建议谷歌安装插件 redux devtools 方便调试，本例已经使用
+ 需要请求接口或异步加载的内容 需要安装react-thunk使action可以导出异步函数

项目链接：
[todoList](https://github.com/weibsgz/react-todolist-redux)
[简书项目](https://github.com/weibsgz/react-jianshu)

### 相关代码：

+ provider

```
<Provider store={store}>
      <div className="App">        
          <BrowserRouter>
            <Header></Header>
            <Route exact path='/' component={Home}></Route>
            <Route exact path='/detail' component={Detail}></Route>
          </BrowserRouter>
      </div>
    </Provider>

```

+ connect
```
//取得store中state的数据 挂到 this.props
const mapStateToProps = (state) =>{
    return {
        list:state.todoList.todoList
    }
}
//向 store 派发action  触发 reducer 挂在到 this.props 
const mapDispatchToProps = (dispatch)=>{
    return {
        initList() {
            dispatch(initTodoAction())
        },

        addTodo(data,input) {
            input.value = ''            
            dispatch(addTodoAction(data))
            
        },
        deleteTodo(index) {
            dispatch(deleteTodoAction(index))
        }   
    }
}


export default connect(mapStateToProps,mapDispatchToProps)(Home);

```

+ 根目录下store/reducer.js

```
import {combineReducers} from 'redux'

import todoListReducer from '../pages/home/store/reducer'
import addMinusReducer from '../pages/detail/store/reducer'

const Reducer = combineReducers({
    todoList:todoListReducer,
    addMinus:addMinusReducer
})

export default Reducer

```
