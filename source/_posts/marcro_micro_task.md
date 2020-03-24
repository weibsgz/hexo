---
layout: post
title: 时间执行机制及对象赋值的一些问题
date: 2020-03-24 01:08:00 +0800
categories: js
tag: js
---

### **宏任务微任务**

1. 宏任务包括 setTimeout setInterval postMessage
2. 微任务包括 promise.then process.nextTick(node)
   执行一个宏任务（栈中没有就从事件队列中获取）
   执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
   宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
   当前宏任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染
   渲染完毕后，JS 线程继续接管，开始下一个宏任务（从事件队列中获取）

```
//打印  promise start  promise.then setTimeout
      {
        setTimeout(() => {
          console.log("setTimeout");
        }, 0);
        new Promise((resovle, reject) => {
          console.log("promise");
          resovle();
        }).then(res => {
          console.log("promise then");
        });

        console.log("start");
      }

```

```
{
        // pr1 2 then1 set1 then2 then4 set2
        setTimeout(function() {
          console.log("set1");
          new Promise(resovle => {
            resovle();
          }).then(function() {
            new Promise(function(resovle) {
              resovle();
            }).then(function() {
              console.log("then4");
            });
            console.log("then2");
          });
        });
        new Promise(function(resovle) {
          console.log("pr1");
          resovle();
        }).then(function() {
          console.log("then1");
        });
        setTimeout(function() {
          console.log("set2");
        });
        console.log(2);
      }


```

```
{
        //1.对象的赋值就是对象的引用地址赋值
        /*
        解题步骤：
        1. a的地址假设是1000 a赋值给b b指向同样的地址
           a  --  1000 {n:1}
           b  --  1000 {n:1}
        2. 运算符优先级 . 更高
           a.x 在1000这个地址上开辟一个x
           {n:1,x:} -- 1000

           a={n:2} a 指向了一个新的地址 1002
           a -- 1002 {n:2}
           再赋值给x
           {n:1,x:{n:2}} -- 1000
            此时 a已经指向1002 b 还是指向1000
           a -- 1002 {n:2}
           b --1000  {n:1,x:{n：2}}
        */

        var a = { n: 1 };
        var b = a;
        a.x = a = { n: 2 };
        console.log("a", a); //{n:2}
        console.log("b", b); //{n:1,x:{n:2}}
      }

```
