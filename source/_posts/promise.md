---
title: 对promise简单介绍和理解
date: 2017-08-30 14:13:23
tags: js
category: "js"  # 分类为 hexo
---




[原文链接](http://liubin.org/promises-book/#promises-overview)

### **基本介绍**

Promise类似于 XMLHttpRequest，从构造函数 Promise 来创建一个新建新promise对象作为接口。

要想创建一个promise对象、可以使用new来调用Promise的构造器来进行实例化。

```
var promise = new Promise(function(resolve, reject) {
    // 异步处理
    // 处理结束后、调用resolve 或 reject
});

```

对通过new生成的promise对象为了设置其值在 resolve(成功) / reject(失败)时调用的回调函数 可以使用promise.then() 实例方法。

```
promise.then(onFulfilled, onRejected)

```
resolve(成功)时
onFulfilled 会被调用

reject(失败)时
onRejected 会被调用

### **Promise的状态**

用new Promise 实例化的promise对象有以下三个状态。

"has-resolution" - Fulfilled
resolve(成功)时。此时会调用 onFulfilled

"has-rejection" - Rejected
reject(失败)时。此时会调用 onRejected

"unresolved" - Pending
既不是resolve也不是reject的状态。也就是promise对象刚被创建后的初始化状态等


### **创建promise对象**
1.new Promise(fn) 返回一个promise对象

2.在fn 中指定异步等处理

    处理结果正常的话，调用resolve(处理结果值)

    处理结果错误的话，调用reject(Error对象)

```
function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText);
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}
// 运行示例
var URL = "http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});

```
getURL 只有在通过XHR取得结果状态为200时才会调用 resolve - 也就是只有数据取得成功时，而其他情况（取得失败）时则会调用 reject 方法。

getURL函数 中的 resolve(req.responseText); 会将promise对象变为resolve（Fulfilled）状态， 同时使用其值调用 onFulfilled 函数。

在getURL 的处理中发生任何异常，或者被明确reject的情况下， 该异常原因（Error对象）会作为 .catch 方法的参数被调用。

其实 .catch只是 promise.then(undefined, onRejected) 的别名而已， 如下代码也可以完成同样的功能。

```
getURL(URL).then(onFulfilled, onRejected);
```

### **Promise.resolve**

静态方法Promise.resolve(value) 可以认为是 new Promise() 方法的快捷方式。

比如 Promise.resolve(42); 可以认为是以下代码的语法糖。

```
new Promise(function(resolve){
    resolve(42);
});
```
在这段代码中的 resolve(42); 会让这个promise对象立即进入确定（即resolved）状态，并将 42 传递给后面then里所指定的 onFulfilled 函数

方法 Promise.resolve(value); 的返回值也是一个promise对象，所以我们可以像下面那样接着对其返回值进行 .then 调用

```
Promise.resolve(42).then(function(value){
    console.log(value);
});

```
### **Promise.reject**

Promise.reject(error)是和 Promise.resolve(value) 类似的静态方法，是 new Promise() 方法的快捷方式。
比如 Promise.reject(new Error("出错了")) 就是下面代码的语法糖形式。

```
new Promise(function(resolve,reject){
    reject(new Error("出错了"));
});
```
这段代码的功能是调用该promise对象通过then指定的 onRejected 函数，并将错误（Error）对象传递给这个 onRejected 函数。

```
Promise.reject(new Error("BOOM!")).catch(function(error){
    console.error(error);
});
```

它和Promise.resolve(value) 的不同之处在于promise内调用的函数是reject而不是resolve，这在编写测试代码或者进行debug时，说不定会用得上。


### **promise chain 中如何传递参数**

```
function taskA() {
    console.log("Task A");
    throw new Error("throw Error @ Task A")
}
function taskB() {
    console.log("Task B");// 不会被调用
}
function onRejected(error) {
    console.log(error);// => "throw Error @ Task A"
}
function finalTask() {
    console.log("Final Task");
}

var promise = Promise.resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
    .then(finalTask);

```
执行这段代码我们会发现 Task B 是不会被调用的。
前面例子中的Task都是相互独立的，只是被简单调用而已。
这时候如果 Task A 想给 Task B 传递一个参数该怎么办呢？
答案非常简单，那就是在 Task A 中 return 的返回值，会在 Task B 执行时传给它。

```
function doubleUp(value) {
    return value * 2;
}
function increment(value) {
    return value + 1;
}
function output(value) {
    console.log(value);// => (1 + 1) * 2
}

var promise = Promise.resolve(1);
promise
    .then(increment) //2
    .then(doubleUp) //4
    .then(output) //4
    .catch(function(error){
        // promise chain中出现异常的时候会被调用
        console.error(error);
    });

```
这段代码的入口函数是 Promise.resolve(1); ，整体的promise chain执行流程如下所示。
Promise.resolve(1); 传递 1 给 increment 函数
函数 increment 对接收的参数进行 +1 操作并返回（通过return）
这时参数变为2，并再次传给 doubleUp 函数
最后在函数 output 中打印结果

### **Promise#catch**

实际上 Promise#catch 只是 promise.then(undefined, onRejected); 方法的一个别名而已。 也就是说，这个方法用来注册当promise对象状态变为Rejected时的回调函数。







