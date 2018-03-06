
---
title: mvvm框架相关问题
date: 2018-03-05 14:13:23
tags: js
category: "js" 
---

### **MVVM具体含义**
M:model(服务端逻辑) v:view  vm(viewModel 特指vue.js,react.js处理服务和视图层的逻辑)
和MVC的区别：页面与逻辑完全分离，可双向绑定


### **双向绑定原理**

data - > watcher ->更新 view

Object.defineProperty(obj,prop,descripor)
obj:对象
prop:要修改的属性
descripor:定义或者修改的属性的描述
会直接在一个对象上定义一个新属性或对已有的属性修改，然后返回这个对象
```
<input id="objInput" type="" name="">
<div id="area"></div>

var obj = {}
    Object.defineProperty(obj,'a',{
      get:function(){
            return val
      },
      set:function(val){
            document.getElementById('objInput').value = val
            document.getElementById('area').innerHTML = val
      }
    })

    document.addEventListener('keyup',function(e){
        obj.a  =  e.target.value
    })

```






