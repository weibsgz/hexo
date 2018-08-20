
---
title: js原型及面向对象
date: 2018-02-28 14:13:23
tags: js
category: "js" 
---

### **new 一个对象的步骤**

1.创建一个空对象instance
2.设置空对象的原型链继承自创建这个空对象的构造函数的原型  instance.__proto__=F.prototype;
3.构造函数执行  this 指向新事例
4.判断构造函数的返回结果是对象类型就返回这个对象，前边就废了，如果不是就返回原来的instance


```
{
        function M() {
            this.a = 1;
        }
        
        let o = new M()
        //函数的prototype有个constructor属性 指向本身的构造函数 如果重新定义了prototype则为false;  
        M.prototype.constructor === M //true
        // js里所有的对象都有proto属性，指向构造该对象的构造函数的原型（用于继承）
        //原型prototype是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法    
        o.__proto__ === M.prototype //true
        M.__proto__ === Function.prototype //true 一层一层向上找
            
        //instanceof 的原理就是判断实例对象的__proto__和 构造函数的原型是不是引用同一个地址
        M.prototype.__proto__ === Object.prototype //true
        
        console.log(o instanceof M) //true
        console.log(o instanceof Object) //true

        // 那么如何判断 o 到底是谁创建的呢                                
        console.log(o.__proto__.constructor === M) //true

    }

```

### **继承方式**

1.借助构造函数 实现继承（call),但是父类的原型链的方法 子类无法继承，只能继承父类构造函数里边的属性和方法。


```
function Parent(){
    this.name = 'parent'
}
Parent.prototype.say = function () {...}

function Child(){
    Parent.call(this)
    this.type = 'child'
}

console.log(new Child()) //{name:parent,type:child} 注意，没有父类原型的方法
```

2.原型链实现继承 由于子类的的对象原型链都是父类是一样的，所以改变一个子类的继承父类的属性，另一个子类也变了

```
    function Parent(){
        this.name = 'parent';
        this.arr = [1,2,3]
    }
    
    Parent.prototype.say = function () {console.log("say parent")}
    
    function Child(){
        this.type = 'child'
    }

    Child.prototype = new Parent()
    
    console.log(new Child().name ) //parent
    console.log(new Child().say() ) //say parent


    var c1 = new Child()
    var c2 = new Child()
    c1.arr.push(4)
    console.log(c1.arr ,c2.arr)  //[1,2,3,4]  [1,2,3,4]



```

3.组合继承方式  防止上边两种的缺点
```
      function Parent() {
         this.name = 'Parent'
         this.arr = [1,2,3]
      }

      function Child(){
        Parent.call(this)
        this.type = 'child'
      }
      Child.prototype = new Parent()

      var c1 = new Child()
      var c2 = new Child()

      c1.arr.push(4)

      console.log(c1.arr,c2.arr)
```