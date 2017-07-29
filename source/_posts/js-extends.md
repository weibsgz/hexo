---
title: js继承
date: 2016-07-15 14:13:23
tags: js
category: "js"  # 分类为 hexo
---




### **call/apply方法继承**
 第一种方法也是最简单的方法，使用call或apply方法，
 将父对象的构造函数绑定在子对象上，即在子对象构造函数中加一行

```
     function Animal(name){
　　　　this.name = name;
        this.spec = '动物';
        this.run = function(){
            console.log(this.name + '是' + this.spec +　' ,它可以跑步')
        }
　　 }
     

     function Cat(name,color){
        console.log(arguments) //["猫", "黄色"]
        Animal.call(this,arguments)  //这里其实也是不传arguments
　　　　this.name = name;
　　　　this.color = color;
　　 }
        
   var cat = new Cat('猫','黄色');
   cat.run()  //猫是动物 ,它可以跑步

```




### **prototype模式 **
如果"猫"的prototype对象，指向一个Animal的实例，那么所有"猫"的实例，就能继承Animal了。
```
Cat.prototype = new Animal();//相当于完全删除了prototype 对象原先的值，然后赋予一个新值
   Cat.prototype.constructor = Cat  //constructor指回Cat 防止继承链的紊乱
   var cat = new Cat('猫','黄色');
   cat.run() //猫是动物 ,它可以跑步
```


### **ES6继承**
```
class AnimalEs6 {
     constructor(props){
        this.name=props.name||'未知';
     }
     eat(){
        console.log(this.name+"在吃东西...");
     }
   }

   class Bird extends AnimalEs6 {
        constructor(props){
             super(props)
             this.name=props.name||'未知';
             this.color = props.color;
        }

        fly(){
            console.log(this.name + "在飞。。。")
        }
   }

   var bird = new Bird({
        name : '花花',
        color : '白色'
   })

   bird.eat()
   bird.fly()

```