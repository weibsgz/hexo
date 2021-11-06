---
layout: post
title:  vue3 权限菜单
date:   2021-11-06 01:08:00 +0800
categories: vue
tag: vue
---

### 项目地址 
https://github.com/weibsgz/vue3.2.8-



### 菜单权限两种思路

1. 后端返回路由表  前端做个映射 省事了

- 简易思路
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  

  <script>
    var data =  [
      {
        'userManage':['user1','user2']
      },
      "roleList",
      {
        "pofile":["pro1"]
      }
    ]  


    var routeObj = {
      'userManage':{
        name:'userManage',
        path:'/userManage/indexPath',
        component:"/userManage/Comindex"
      },
      "user1":{
        name:"user1",
        path:'/userManage/user1/indexPath',
        component:"/userManage/user1/Comindex"
      },
      "user2":{
        name:"user2",
        path:'/userManage/user1/indexPath',
        component:"/userManage/user1/Comindex"
      },
      "roleList":{
        name:"roleList",
        path:'/roleList/indexPath',
        component:"/roleList/Comindex"
      },
      "pofile":{
        name:"pofile",
        path:'/pofile/indexPath',
        component:"/pofile/Comindex"
      },
      "pro1":{
        name:"pro1",
        path:'/pofile/pro1/indexPath',
        component:'/pofile/pro1/Comindex'
      },
    }


    var generateRoute = function(data,routeObj) {
      var route = []

      data.map(function(v,i){
         if(Object.prototype.toString.call(v) === '[object Object]') {
             var key = Object.keys(v)[0];
             var arr = v[key];
             var obj = routeObj[key]
             obj.children = []
             for(var i=0; i<arr.length; i++) {
              obj.children.push(routeObj[arr[i]])
             }
             route.push(obj)

         }
         else {
           route.push(routeObj[v])
         }
      })


      return route
    }

   console.log( generateRoute(data,routeObj))
  </script>
</body>
</html>

```


2. 自己根据后端返回的权限字段，动态生成路由  需要用到addRoutes
   慕课网项目 权限相关：https://www.imooc.com/wiki/vue3/elementAdmin8.html

   路由需要公有路由 和 私有路由  我们处理的是根据返回的权限 来动态插入匹配到的私有路由（addRoutes方法）

   本项目中  
   - 把私有路由分成几块（根据不同权限） `./store/modules`

   - 根据权限筛选路由 menus 是后端返回的权限字段 `["userManage", "roleList", "permissionList", "articleRanking", "articleCreate"]`
     privateRoutes是拆分好的私有路由集合 每个私有路由上的name和 menus中的字段对应
   ```
    filterRoutes(context, menus) {
      const routes = []
      // 路由权限匹配
      menus.forEach(key => {
        // 权限名 与 路由的 name 匹配
        routes.push(...privateRoutes.filter(item => item.name === key))
      })
      // 最后添加 不匹配路由进入 404
      routes.push({
        path: '/:catchAll(.*)',
        redirect: '/404'
      })
      context.commit('setRoutes', routes) //  vuex中state.routes = [...publicRoutes, ...newRoutes]
      return routes
    }


   ```
 - `src/permission.js` （路由守卫） 中 使用addRoutes方法动态添加路由，最后添加404页面
 ```

// 判断用户资料是否获取
      // 若不存在用户信息，则需要获取用户信息
      if (!store.getters.hasUserInfo) {
        // 触发获取用户信息的 action，并获取用户当前权限 （通过接口返回权限菜单）
        const { permission } = await store.dispatch('user/getUserInfo')
        // 处理用户权限，筛选出需要添加的权限
        const filterRoutes = await store.dispatch(
          'permission/filterRoutes',
          permission.menus
        )
        // 利用 addRoute 循环添加
        filterRoutes.forEach(item => {
          router.addRoute(item)
        })
        // 添加完动态路由之后，需要在进行一次主动跳转
        return next(to.path)
      }
      next()


 ```


- 退出登录时，添加的路由表并未被删除 需要退出时候重置路由表

```
/**
 * 初始化路由表
 */
export function resetRouter() {
  if (
    store.getters.userInfo &&
    store.getters.userInfo.permission &&
    store.getters.userInfo.permission.menus
  ) {
    const menus = store.getters.userInfo.permission.menus
    menus.forEach((menu) => {
      router.removeRoute(menu)
    })
  }


```