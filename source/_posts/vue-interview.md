---
layout: post
title: vue面试考点
date: 2019-08-17 01:08:00 +0800
categories: vue
tag: vue
---

### MVVM

1. M - model (数据模型，为 data 部分)
2. v - view (视图层)
3. vm - viewModel(ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来，ViewModel 通常要实现一个 observer 观察者，当数据发生变化，ViewModel 能够监听到数据的这种变化，然后通知到对应的视图做自动更新，而当用户操作视图，ViewModel 也能监听到视图的变化，然后通知数据做改动，这实际上就实现了数据的双向绑定。。)

### 响应式原理

1. Object.defineProperty 缺点：

- 深度监听（复杂对象）需要递归，计算量大
- 无法检测到新增，删除的属性 需要用 Vue.set/vue.delete
- 无法监听数组方法，需要特殊处理 (尤雨溪 github 回答是因为 JS 性能原因)
- 那么 vue 如何监听数组

```
// 获取数组的原型Array.prototype，上面有我们常用的数组方法
 const oldArrayProperty = Array.prototype

// 创建一个空对象arrayMethods，并将arrayMethods的原型指向Array.prototype
const arrProto = Object.create(oldArrayProperty);

const methodsToPatch = ['push','pop','shift','unshift','splice','sort','reverse'];

 // 将上面的方法重写
methodsToPatch.forEach(function (method) {
     arrProto[method] = function() {
       updateView()   //触发视图更新
       oldArrayProperty[method].call(this, ...argruments)   //调用原数组的方法
     }
})

//这样 arrProto 上就有methodsToPatch这些方法  同时 arrProto.__proto__ 有原来Array.prototype的方法


function observer(target) {
  if(typeof target !== 'object' || target ===null) {
    return target
  }

  //如果是数组把数组的原型链指向重写过的数组原型
  if(Array.isArray(target)) {
    target.__proto__ = arrProto
  }


  for(let key in target) {
    defineReactive(target,key,target[key]) //defineProperty
  }

}



```

2. vue3 用 proxy 有兼容性问题，无法用 polyfill

### 虚拟 DOM

概念： 用普通 js 对象来描述 DOM 结构

1. 虚拟 DOM 结构

```
<div id="div1" class="container">
 <ul style="font-size:20px">
  <li>a</li>
 </ul>
</div>



{
  tag:'div',
  props:{
    id:"div1",
    className:'container'
  }
  children:[
    {
      tag:'ul',
      props:{
        style:"font-size:20px"
      },
      children:[
        {
          tag:"li",
          children:'a'
        }
      ]
    }
  ]
}

```

### DIFF 算法

- 只比较同一层级，不跨级比较
- tag 不相同，则直接删掉重建，不在深度比较
- tag 和 key，两者都相同，则认为是相同节点，不再深度比较 （为什么 v-for 中要使用 key）

- h 函数就是 vue 中的 createElement 方法，这个函数作用就是创建虚拟 dom，追踪 dom 变化的。。。

```
const app = new Vue({
    ··· ···
    render: h => h(App)
})

```

这个 render 方法也可以写成这样：

```
const app = new Vue({
    ··· ···
    render:function(createElement){
        return createElment(App)
    }
})

```

### vue 模板编译成什么

`const template = '<p>{{message}}</p>'`
`with(this){return _c('p',[_v(_s(message))])}`

- with 语法括号内的 this 代表 vm 实例 \_c(createElement),\_v(createVnode) 都是绑定在 vm 实例上的方法
- 模板编译为 render 函数，执行 render 函数返回 vnode
- 使用 webpack vue-loader 会编译模板

- vue 组件中使用 render 代替 template

```
Vue.component('heading',{
    render:function(createElment){
        return createElement(
            'h1',
            [
                createElement('a',{
                    attrs:{
                        name:'headerId',
                        href:'#'
                    }
                },'this is a tag')
            ]
        )
    }
})



```

### 前端路由

1.  hash

- hash 变化会触发网页跳转，即浏览器的前进、后退
- hash 变化不会刷新页面，SPA 必须的特点
- hash 永远不会提交到 server 端（前端自生自灭）

```
<p>hash test</p>
<button id="btn1">修改 hash</button>

<script>
    // hash 变化，包括：
    // a. JS 修改 url
    // b. 手动修改 url 的 hash
    // c. 浏览器前进、后退
    window.onhashchange = (event) => {
        console.log('old url', event.oldURL)
        console.log('new url', event.newURL)

        console.log('hash:', location.hash)
    }

    // 页面初次加载，获取 hash
    document.addEventListener('DOMContentLoaded', () => {
        console.log('hash:', location.hash)
    })

    // JS 修改 url
    document.getElementById('btn1').addEventListener('click', () => {
        location.href = '#/user'
    })
</script>

```

2. H5 history

- 用 url 规范的路由，但跳转时不刷新页面
- history.pushState
- window.onpopstate

```
<p>history API test</p>
<button id="btn1">修改 url</button>

<script>
    // 页面初次加载，获取 path
    document.addEventListener('DOMContentLoaded', () => {
        console.log('load', location.pathname)
    })

    // 打开一个新的路由
    // 【注意】用 pushState 方式，浏览器不会刷新页面
    document.getElementById('btn1').addEventListener('click', () => {
        const state = { name: 'page1' }
        console.log('切换路由到', 'page1')
        history.pushState(state, '', 'page1') // 重要！！
    })

    // 监听浏览器前进、后退
    window.onpopstate = (event) => { // 重要！！
        console.log('onpopstate', event.state, location.pathname)
    }

```
