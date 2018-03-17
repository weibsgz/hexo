
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
比如vue 的data就是一个对象，所以可以对里边定义的属性进行双向绑定



### **双向绑定设计模式**

data => observer(Object.defineProperty) => watcher => view




### **实现一个简单的双向绑定**
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



### **源码解析**


```
new Vue({
          data:{
            a:1
          },
          methods:{

          }
        })

       function Vue(options) {
          this.data = options.data;
          this.methods = options.methods;
          var self = this;
          //循环data中的数据，每个key做代理
          Object.keys(this.data).forEach(function(key){
              self.proxyKeys(key)
          })  

          observe(this.data)
       }
       
       Vue.prototype = {
          proxyKeys:function(key){
            var self = this;
            //注意：这里this代表vm实例，绑定data中的key直接到vm上，就实现了 vm.key = vm.data.key的效果
            //所以你代码里写this.a = this.data.a
            Object.defineProperty(this,key,{
              enumerable:false,
              configurable:true,
              get:function() {
                return self.data[key]
              },
              set:function(newVal){
                self[data].key = newVal;
              }
            })
          }
       }

       //Observer 作用就是遍历所有的data 为其中所有的属性（可能有递归）添加观察者(getter) ,数据有变化应该通知观察者(setter) dep就是一个观察者列表，有添加(需要观察的属性)，有通知功能(通知wather)调用自身的update方法
       function Observe(data) {
          this.data = data;
          this.walk(data)
       }

       Observe.prototype = {
          walk:function(data) {
            var self = this;
            Object.keys(data).forEach(function(key) {
                self.defineReactive(data,key,data[key])
            })
          },
          defineReactive:function(data,key,val){
             var dep = new Dep()
             //如果data中的值还是对象，递归
             var childObj = observe(val);
             Object.defineProperty(data,key,{
                   enumerable:true,
                   configurable:true,
                   get:function getter() {
                       if(Dep.target) {  //Dep.target就是watcher
                          dep.addSub(Dep.target)
                       }
                       return val
                   },
                   set:function setter(newVal) {
                      val === newVal ? return : val = newVal;
                      dep.notify()
                   }
             })
          }
       }

       function Dep() {
         this.subs = []
       }

       Dep.prototype = {
          addSub:function(sub) {
              this.subs.push(sub)
          },
          notify:function() {
              this.subs.forEach(function(sub) {
                  sub.update();
              })
          }
       }

       Dep.target = null;


       function observe(val) {
          if(!val || typeof val !== Object) {
            return 
          }
          return new Observe(value)
       }

```