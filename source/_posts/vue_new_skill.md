---
title: vue 新技巧
date: 2020-06-23 14:13:23
tags: vue
category: 'vue'
---

### 使用 Vue2.6 提供的新 API Vue.observable 手动打造一个 Vuex(简单的 vuex 替代方案)

1. 创建 store

```
import Vue from 'vue'

// 通过Vue.observable创建一个可响应的对象
export const store = Vue.observable({
  userInfo: {},
  roleIds: []
})

// 定义 mutations, 修改属性
export const mutations = {
  setUserInfo(userInfo) {
    store.userInfo = userInfo
  },
  setRoleIds(roleIds) {
    store.roleIds = roleIds
  }
}

```

2. 在组件中引用

```
<template>
  <div>
    {{ userInfo.name }}
  </div>
</template>
<script>
import { store, mutations } from '../store'
export default {
  computed: {
    userInfo() {
      return store.userInfo
    }
  },
  created() {
    mutations.setUserInfo({
      name: '子君'
    })
  }
}
</script>
```

### attrs

1. 父组件上定义的属性，样式 可以直接完全让子组件继承

2. 子组件可以设置`inheritAttrs: false` 父组件中的样式 不在跟元素中继承 在你定义的 v-bind="$attrs" 的元素中继承

### 按钮权限

```
app.directive('has', {
  // 比如 v-has:add="'user-create'"(注意，一定要用字符串引起来，否则是个函数了，)
  // el是绑定过的元素   ， binding.args = 'add'  binding.value = 'user-crate'
  //使用 USER.VUE <el-button type="danger" v-has="'user-patch-delete'"@click="handlePatchDel">批量删除</el-button>
  beforeMount: function (el, binding) {
    let actionList = storage.getItem('actionList')
    let value = binding.value
    let hasPermission = actionList.includes(value)
    if (!hasPermission) {
      el.style = 'display:none'
      //光隐藏不够，删了他，做个异步因为在beforeMount钩子了  他还没有渲染到DOM上
      setTimeout(() => {
        el.parentNode.removeChild(el)
      }, 0)
    }
  }
})

```

### 404 页面

```
//路由配置
{
    name: '404',
    path: '/404',
    meta: {
      title: '页面不存在'
    },
    component: () => import('@/views/404.vue')
  }


// 判断当前地址是否可以访问
function checkPermission(path) {
  console.log(router.getRoutes()) //所有路由的数组
  let hasPermission = router
    .getRoutes()
    .filter((route) => route.path == path).length
  if (hasPermission) {
    return true
  } else {
    return false
  }
}
// 导航守卫
router.beforeEach((to, from, next) => {
  if (checkPermission(to.path)) {
    document.title = to.meta.title
    next()
  } else {
    next('/404')
  }
})


```

### storeage 封装

```
import config from '../config/index.js'

//这个项目所有的storage存储在一个命名空间对象上

export default {
  getStorage() {
    return JSON.parse(window.localStorage.getItem(config.namespace) || '{}')
  },

  setItem(key, val) {
    let storage = this.getStorage()
    storage[key] = val
    window.localStorage.setItem(config.namespace, JSON.stringify(storage))
  },

  getItem(key) {
    return this.getStorage()[key]
  },
  clearItem(key) {
    let storage = this.getStorage()
    delete storage[key]
    window.localStorage.setItem(config.namespace, JSON.stringify(storage))
  },
  clearAlliItem() {
    window.localStorage.clear()
  }
}


```

config.js

```
const env = import.meta.env.MODE || 'production'

const envConfig = {
  development: {
    baseApi: '/api',
    mockApi:
      'https://www.fastmock.site/mock/3aa8fc6d77553c0405584d0dc88c5457/api'
  },
  test: {
    baseApi: 'test.xxx.com',
    mockApi:
      'https://www.fastmock.site/mock/3aa8fc6d77553c0405584d0dc88c5457/api'
  },
  production: {
    baseApi: 'prod.xxx.com',
    mockApi: ''
  }
}

export default {
  env,
  mock: false,
  namespace: 'manager',
  ...envConfig[env]
}


```

### request.js

```
import axios from 'axios'
import config from '../config'
import { ElMessage } from 'element-plus'
import router from '../router'
import storage from './storage'

const NETWORKERROR = '网络错误'
const TOKEN_INVALID = 'TOKEN认证失败，请重新登录'
const ERROR_OK = 200

const service = axios.create({
  baseURL: config.baseApi,
  timeout: 8000
})

service.interceptors.request.use((req) => {
  //TOKEN判断
  // const headers = req.headers
  // const userInfo = storage.getItem('userInfo')
  // let token = ''
  // if (userInfo) {
  //   token = userInfo.token
  // }
  // if (!headers.Authorization) headers.Authorization = 'Bearer ' + token

  const headers = req.headers
  const { token } = storage.getItem('userInfo') || {}

  if (!headers.Authorization)
    headers.Authorization = 'Bearer ' + token ? token : ''

  return req
})

service.interceptors.response.use((res) => {
  const { code, data, msg } = res.data
  if (code === ERROR_OK) {
    return data
  } else if (code === 500001) {
    ElMessage.error(TOKEN_INVALID)
    setTimeout(() => {
      router.push('/login')
    }, 1500)
    return Promise.reject(TOKEN_INVALID)
  } else {
    ElMessage.error(msg || NETWORKERROR)
    return Promise.reject(msg || NETWORKERROR)
  }
})

export default function request(options) {
  options.method = options.method || 'get'
  if (options.method.toLowerCase() === 'get') {
    options.params = options.data
  }
  //取全局的MOCK 就是config/index.js配置的mock:false
  let isMock = config.mock
  if (typeof options.mock !== 'undefined') {
    isMock = options.mock
  }

  //以防万一 线上地址一定用线上接口
  if (config.env === 'production') {
    service.defaults.baseURL = config.baseApi
  } else {
    service.defaults.baseURL = isMock ? config.mockApi : config.baseApi
  }

  return service(options)
}

;['get', 'post', 'put', 'delete', 'patch'].forEach((item) => {
  request[item] = (url, data, options) => {
    return request({
      url,
      method: item,
      data,
      ...options
    })
  }
})


```
