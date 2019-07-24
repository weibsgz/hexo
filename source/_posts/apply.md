
---
title: js的apply问题
date: 2019-07-14 14:13:23
tags: js
category: "js" 
---

### **apply**

MSDN定义：


func.apply(thisArg, [argsArray])
参数节
thisArg
可选的。在 func 函数运行时使用的 this 值。请注意，this可能不是该方法看到的实际值：如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。
argsArray
可选的。一个数组或者类数组对象，`其中的数组元素将作为单独的参数传给 func 函数`。如果该参数的值为 null 或  undefined，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。 浏览器兼容性 请参阅本文底部内容。

```
class Test {
        do1() {
            var arg = Array.prototype.slice.call(arguments)
            console.log(arg)
        }
        do2() {
            this.do1.apply(this,arguments)
        }
        do3() {
            this.do1(arguments)
        }
    }
    
    var a = new Test()
    a.do2(test) //[f]
    a.do3(test) //[Arguments(f)]

     a.do2(1) // [1]
     a.do3(1) //[Arguments(1)]

    function test() {

    }
```
以上代码如果执行DO3方法 不用apply 那么传入的无论是函数还是数字就不是一个单一的函数或者数字
会被arguments包一层