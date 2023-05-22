---
title: vue3 换肤思路
date: 2023-05-22 14:13:23
tags: vue
category: 'vue'
---

### 我们使用CSS变量来实现换肤是最方便的

**创建CSS**
```
//主题色设置 root代表默认主题色， html[data-theme]代表另一种主题色
:root{
  --main-color: hotpink;
}

html[data-theme='dark'] {
  --main-color: #000;
}



//主题色应用
body{
  background-color: var(--main-color);
}

```

> 设置好css 在main.js中引入



**我们使用hooks来创建一个theme代替使用vuex**
```
import { ref, watchEffect } from "vue";

const theme = ref(localStorage.getItem("_theme_")) || ref("light");

watchEffect(() => {
  document.documentElement.dataset.theme = theme.value;
  localStorage.setItem("_theme_", theme.value);
});

export default function useTheme() {
  return {
    theme
  };
}

```
> 这里的思想就是从本地缓存中读取 theme 在他发生更改的时候设置html的 data-theme属性，从而能使用到我们的第一步设置的CSS



**随便写一个更改主体的组件**

```
<template>
  <input
    :checked="theme == 'light'"
    @click="changeTheme('light')"
    type="radio"
    id="light"
    name="theme"
  /><label for="light">light</label>
  <input
    type="radio"
    :checked="theme == 'dark'"
    @click="changeTheme('dark')"
    id="dark"
    name="theme"
  /><label for="dark">dark</label>
</template>

<script setup>
import useTheme from "../useTheme.js";
const { theme } = useTheme();

const changeTheme = (params) => {
  theme.value = params;
};
</script>

<style>
</style>

```
