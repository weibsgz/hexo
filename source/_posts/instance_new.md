---
layout: post
title: 重写instance&&new
date: 2020-04-15 01:08:00 +0800
categories: js
tag: js
---

### **instance**

- instance 方法本质是 instanceof 第一个变量是一个对象 A，第二个变量是一个函数 B，
- 沿着 A 的原型链**proto**一直向上找，如果能找到一个**proto**等于 B 的 prototype，则返回 true；
- 如果找到终点还没找到则返回 false。

```
      function _instance(a, b) {
        if (!a || !b) {
          return false;
        }
        var A = a.__proto__;
        var B = b.prototype;
        while (true) {
          if (A === null) {
            return false;
          } else if (A === B) {
            return true;
          } else {
            //每次循环继续找__proto__
            A = A.__proto__;
          }
        }
      }

```

- new 本质上就是调用构造函数生成一个对象，
- 这个对象能够访问构造函数的的原型对象，因为我们来尝试模拟一下 new 的实现。

```
 function _new(Fn, ...param) {
        var obj = {};
        //apply第二个参数接收一个数组，call则不是，call可以有n个参数有多少放多少就行
        var result = Fn.call(obj, ...param);
        obj.__proto__ = Fn.prototype;
        // 如果构造函数返回一个对象，则将该对象返回，否则返回步骤1创建的对象
        return typeof result === "object" ? result : obj;
      }

      function Person(name) {
        this.name = name;
        this.say = function() {
          console.log("say", this.name);
        };
      }

      var obj = _new(Person, "weibin");
      console.log(obj);

```
