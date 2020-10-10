---
title: vue 新技巧
date: 2020-06-23 14:13:23
tags: vue
category: "vue"
---
### 使用Vue2.6提供的新API Vue.observable手动打造一个Vuex(简单的vuex替代方案)
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

2. 子组件可以设置`inheritAttrs: false` 父组件中的样式 不在跟元素中继承  在你定义的 v-bind="$attrs"  的元素中继承

