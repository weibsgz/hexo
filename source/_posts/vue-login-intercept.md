---
title: VUE登录拦截
date: 2018-06-15 14:13:23
tags: vue
category: "vue"  # 分类为 js
---




### **1.首先，应该在登录页面点击登录按钮时候 存token**
```
 cacheUtils.cookie.setObject(consts.ACCESSTOKEN,response.data.accessToken);
```

<!-- more -->

### **2.定义路由的时候就需要多添加一个自定义字段requireAuth，用于判断该路由的访问是否需要登录**
```
const routes = [
    {
        path: '/',
        name: '/',
        component: Index
    },
    {
        path: '/repository',
        name: 'repository',
        meta: {
            requireAuth: true,  // 添加该字段，表示进入这个路由是需要登录的
        },
        component: Repository
    },
    {
        path: '/login',
        name: 'login',
        component: Login
    }
];
```
### **3.main.js中 需要做路由钩子，判断每个路由是否需要验证登录，结合本地是否存在token**
```
//路由重定向方法,如果缓存没有ACCESSTOKEN就会自动跳转到登录页
router.beforeEach((to, from, next) => {
  if (to.meta.requireAuth) {  // 判断该路由是否需要登录权限
    if (!cacheUtils.cookie.getObject('access_token')) {  // 通过cookie获取当前的token是否存在
      console.log('需要验证登录，不存在token')
      next({
        path: '/login',
        //query: {redirect: to.fullPath}  // 将跳转的路由path作为参数，登录成功后跳转到该路由
      })
    }
    else {
      console.log('需要验证登录，存在token')
      next()
    }
  } else {
    console.log('不需要验证登录')
    next()
  }

});
```


### **4.当前token失效了，但是token依然保存在本地 需要结合 http 拦截器 + 后端接口返回的http 状态码来判断。**

```
xhr.interceptors.response.use(
  function(response) {
    //后台无 状态码  无法判断接口异常情况
    return response.data
  },
  function(error) {
    console.error('Request failed')
    if (error.response) {
      switch (error.response.status) {
          case 401:
              // 返回 401 清除token信息并跳转到登录页面
              cacheUtils.cookie.clear();
              router.replace({
                  path: 'login',
                  query: {redirect: router.currentRoute.fullPath}
              })
      }
  }
    return Promise.reject(error)
  }
)
```
 
 