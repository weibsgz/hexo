---
title: vue3 右键菜单的封装
date: 2024-02-6 14:13:23
tags: vue
category: 'vue'
---


##  使用页面。
```
<template>
    
    <div style="display: flex; height: 100%;">
        <!-- 区域1  每个区域都有一个右键选择组件 包裹着区域内容（插槽） -->
        <menu-com-vue :menu="menu1" @select="selectMenu">        
            <div class="area1">{{label1}}</div>
        </menu-com-vue>
        
        <!-- 区域2 -->
        <menu-com-vue :menu="menu2" @select="selectMenu2">        
        <div class="area1">{{label2}}</div>
        </menu-com-vue>

    </div>

</template>
<script setup>
 import { ref} from 'vue'
 import menuComVue from './components/menu-com.vue'
 

 const label1 = ref('')
 const label2 = ref('')
 const menu1 = ref([
    {lable:'部门',id:1},
    {lable:'部门1',id:2},
    {lable:'部门2',id:3},
    {lable:'部门3',id:4},
    {lable:'部门4',id:5},
 ])


 const menu2 = ref([
    {lable:'员工1',id:1},
    {lable:'员工3',id:2},
    {lable:'员工3',id:3},
    {lable:'员工4',id:4},
    {lable:'员工5',id:5},
 ])

 const selectMenu = (item) => {
    label1.value= item;
 }

 const selectMenu2 = (item) =>{
    label2.value= item;
 }

</script>

<style>
    .area1 {
        background: #ccc;
        flex: 50% 0 0;
    }

    .area2 {
        background-color:bisque;
        flex: 50% 0 0;
    }
</style>



```


## 右键菜单组件

```
<template>
    <div ref="containerRef"  class="container"> 
        {{ menuVis }} {{ x }}

        <!--包裹内容区域的插槽-->
        <slot></slot>

        <!-- 使用 Teleport 将右键菜单放到BODY 因为他是设置的fiexd 如果他的父元素有transform 就不生效了-->

        <Teleport to="body">           
            <div class="context-menu" v-if="menuVis" :style="{'top':y  + 'px','left':x  + 'px'}">
            <ul>
                    <li v-for="item in menu" @click="clickItem(item)">
                        {{ item.lable }}
                    </li>
            </ul>
            </div>           
        </Teleport>
        
    </div>
</template>

<script setup>
 import {ref} from 'vue'

 import useContextMenu from './useContextMenu.js'
 const emits = defineEmits(['select'])

const containerRef = ref(null)

console.log('主页中onMounted',containerRef,containerRef.value)

//使用这个HOOKS 去取得当前鼠标的X,Y 以及是否显示菜单。
const {x,y,menuVis} = useContextMenu(containerRef)

// watchEffect(()=>{
//     const {x,y,menuVis} = useContextMenu(containerRef.value)
// },{
//     flush:'post'
// })
    

const props = defineProps({
    // 数据源
    menu: {
      type: Array,
      required: true
    }
  
  })

 

  const clickItem = (item)=>{
    emits('select',item.lable)
  }
        
</script>

<style>
    .context-menu {

        position: fixed;
        left:v-bind(x + 'px');
        top:v-bind(y  + 'px');
        width: 200px;
        height: auto;
        border: 1px solid #000;
        font-size: 14px;
        text-align: center;
    }
</style>

```

## 右键菜单HOOKS（提供鼠标位置 X,Y ，是否显示菜单）
```

import {ref , onMounted  ,onUnmounted} from 'vue'

export default function useContextMenu(container) {
     

    console.log('use-container',container)
    //菜单的位置信息 是否可见
    const x = ref(0);
    const y = ref(0);
    const isVisible = ref(false)

    //打开菜单
    function showMenu(e) {
     
        e.preventDefault();

        //写这个阻止冒泡是为了那种嵌套区域的菜单 防止打开俩菜单。
        e.stopPropagation()
        x.value = e.pageX;
        y.value = e.pageY;
        isVisible.value = true;
        console.log('触发右键', x.value)
    }

    //关闭菜单
    function closeMenu() {
        isVisible.value = false
    }


    onMounted(() => {
        //关闭菜单的情况 1，点击菜单，2 打开一个新的菜单
        // 先关闭所有的，再打开,需要在捕获阶段执行。否则先打开菜单 又冒泡到window上被关闭了。等于没打开菜单

        window.addEventListener('contextmenu',closeMenu,true)
        window.addEventListener('click',closeMenu,true)
        container.value.addEventListener('contextmenu',showMenu)

        
      })

    onUnmounted(()=>{
        container.value.removeEventListener('contextmenu',showMenu)
    })

    return {
        x:x,
        y:y,
        menuVis:isVisible
    }
}

```