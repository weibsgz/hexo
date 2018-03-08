
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


### **JS垃圾回收机制**

基本类型是：Undefined/Null/Boolean/Number/String

基本类型的值存在内存中，被保存在栈内存中。从一个变量向另一个变量复制基本类型的值，会创建这个值的一个副本。

引用类型：object

引用类型的值是对象，保存在堆内存中。

JS中最常见的垃圾回收方式是1.标记清除。 2.引用计数

标记清楚：是当变量进入环境时，将这个变量标记为“进入环境”。当变量离开环境时，则将其标记为“离开环境”。标记“离开环境”的就回收内存。

引用计数（低版本IE内存泄漏）1.    声明了一个变量并将一个引用类型的值赋值给这个变量，这个引用类型值的引用次数就是1。
3.    当包含这个引用类型值的变量又被赋值成另一个值了，那么这个引用类型值的引用次数减1.当垃圾收集器下一次运行时，它就会释放引用次数是0的值所占的内存

引起内存泄漏的原因：

1.变量相互引用
```
function problem() {
    var objA = new Object();
    var objB = new Object();

    objA.someOtherObject = objB;
    objB.anotherObject = objA;
}
```
这里存在循环引用会引起内存泄漏，引起内存泄漏解决方法

```
objA.someOtherObject = null
objB.anotherObject = null
```
2.意外的全局变量引起的内存泄漏。
全局变量，不会被回收，使用严格模式避免

3.闭包引起的内存泄漏

原因：闭包可以维持函数内局部变量，使其得不到释放。
解决：将事件处理函数定义在外部，解除闭包,或者在定义事件处理函数的外部函数中，删除对dom的引用。

### **闭包**
1，可以访问外部函数作用域中变量的函数
2，被内部函数访问的外部函数的变量可以保存在外部函数作用域内而不被回收

实现累加
```
var a = (function(){
          var s = 0; 
          return function (){
            s++;
            console.log(s)
          }
        })()

        a()//1
        a()//2
        a()//3
```
```
var oli = document.getElementsByTagName('li');
     var i;
     for(i = 0;i < 5;i++){
         oli[i].onclick = (function(num){
             var a = num; // 为了说明问题
             return function(){
                 alert(a);
             }
         })(i)
     }
     console.log(i); // 5
```


### **经典问题**
```
for(var i=0; i<5;i++){
          listArr[i].onclick = (function(n){
              return function() {
                console.log(n)
              }
          })(i)
       }

```

1.为什么不写闭包就输出5  
因为js 是单线程的 循环是同步，先执行， onclick指向的匿名函数需要onclick才执行所以是异步，后执行，var 没有块级作用域，所以循环到5后 window.i ==5  因此，这时你点击任何一个li，都要alert(i); 局部作用域内没有声明i变量，因此会追溯到全局作用域的i，所以弹出的都是5。可以用let解决

2.为什么写闭包就正确了
循环的时候 已经将listArr[i] 这里的i 变成 0,1,2,3,4 ，click 时候 function 内找 n 没有，找向上层定义时的作用域，里面形参 n 是 for 时候赋值的当时的 i 值。


### **提升页面性能的方法**

1.资源的压缩合并，减少HTTP请求

2.异步加载：动态脚本加载，defer(html加载完后，逐一按顺序加载) async(无序的)

3.浏览器缓存

4.使用cdn

5.DNS预解析

### **从输入URL到页面加载发生了什么**

DNS解析
TCP连接
发送HTTP请求
服务器处理请求并返回HTTP报文
浏览器解析渲染页面
连接结束
