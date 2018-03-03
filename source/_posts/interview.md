
---
title: 前端一些安全类，渲染机制，运行机制，性能的面试问题
date: 2017-03-3 14:13:23
tags: js
category: "js" 
---

### **安全类**
1.CSRF 攻击
引诱用户点击链接登录到目标网站
防范措施：加token验证 ，  第一次登录的时候存token，refer验证：看来源是不是本网站的，不是一律拦截

2.XSS攻击
注入脚本
防范措施：对一些表单输入的参数进行过滤限制


### **重排重绘**

重排：页面上的盒模型都有自己的样式，浏览器会把他放在他该出现的位置
导致重排：改动dom,display:none,加动画,resize窗口等
减少重排重会：比如写一个插入HTML的片段 写好后一次插入到页面，不要分步


### **JS运行机制**
js是单线程，异步任务先挂起，同步任务处理完毕再处理异步任务
```
 console.log(1)
 setTimeout(funciton(){
  console.log(2)
 },0)
 console.log(3)  //1 3 2

```

```
console.log('a')

while(true){
    
}

console.log('b')  //a 同步任务  while一直在循环 执行不到B
```


```
for(var i=0; i<4; i++){
    setTimeout(function(){
        console.log(i)   // 4 4 4 4  循环是同步的每次执行都将异步挂起，都循环完i =4 这个时候才把挂起的4次异步任务加入
    },0)
}

```

同步>promise>promise.then>setTimeout

```

    setTimeout(function () {
          console.log(1)
      }, 0);
      new Promise((resolve) =>{
          console.log(4)
              resolve();
      }).then(function () {
          console.log(2);
      });
      console.log(3)    //4,3,2,1

```