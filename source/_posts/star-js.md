---
title: 星星评价JS
date: 2017-09-03 14:13:23
tags: js
category: "js"  # 分类为 js
---

分为两种封装形式  对象封装 和 函数封装


### **html**
```
<html>
    <head>
        <style>
            *{margin:0; padding:0}
            div ul{
                display: flex;
            }
            div ul li{
                list-style-type:none;
                width: 50px; height: 50px; background-color:yellow; margin:0 10px; transition: all .3s;
            }
            div ul li.active{
                background: red
            }
        </style>
    </head>
    <body>
        <div>
            <ul>
                <li class="active">1</li>
                <li>2</li>
                <li>3</li>
                <li>4</li>
                <li>5</li>
            </ul>
        </div>
      </body>
</html>
```

<!-- more -->

### **对象封装JS**

``` 
 // var starObj = {
            //     lis:document.getElementsByTagName("li"),
            //     selectStar: 0,   
            //     run :function(){
            //         this.hover()
            //         this.click()
            //         this.mouseout()
            //     } ,
            //     starShow : function(index){
            //         for(var i = 0; i<this.lis.length; i++){
            //             this.lis[i].className = i <= (index || this.selectStar) ? 'active' : ''
            //         }
            //     },
            //     hover :function(){
            //         var me = this;
                 
            //         for(var i =0 ; i<this.lis.length; i++){
            //             //解决闭包问题
            //             this.lis[i].index = i;                        
            //             this.lis[i].onmouseover = function(){
            //                me.selectStar = 0;
            //                me.starShow(this.index)
            //             }
            //         }
            //     },
            //     click : function(){
            //         var me = this;
            //         for(var i =0 ; i<this.lis.length; i++){
            //             //解决闭包问题
            //             this.lis[i].index = i;                        
            //             this.lis[i].onclick = function(){
            //               me.selectStar = this.index
            //               me.starShow()
            //               console.log(`you pinfen is ${this.index + 1}`)
            //             }
            //         }
            //     },
            //     mouseout :function(){
            //         var me = this;
                  
            //         for(var i =0 ; i<this.lis.length; i++){
            //             //解决闭包问题
            //             this.lis[i].index = i;                        
            //             this.lis[i].onmouseout = function(){
                              
            //                  me.starShow(0)
            //               //console.log(`you pinfen is ${this.index + 1}`)
            //             }
            //         }
            //        // this.starShow(0)
            //     }
            // }

            //starObj.run()
```



### **函数封装JS**

``` 
function Star(){
                this.lis = document.getElementsByTagName("li"),
                this.selectStar =  0  
            };

            Star.prototype  = {
                run:function(){
                    this.hover()
                    this.click()
                    this.mouseout()
                },
                starShow : function(index){
                    for(var i = 0; i<this.lis.length; i++){
                        this.lis[i].className = i <= (index || this.selectStar) ? 'active' : ''
                    }
                },
                hover :function(){
                    var me = this;
                 
                    for(var i =0 ; i<this.lis.length; i++){
                        //解决闭包问题
                        this.lis[i].index = i;                        
                        this.lis[i].onmouseover = function(){
                           me.selectStar = 0;
                           me.starShow(this.index)
                        }
                    }
                },
                click : function(){
                    var me = this;
                    for(var i =0 ; i<this.lis.length; i++){
                        //解决闭包问题
                        this.lis[i].index = i;                        
                        this.lis[i].onclick = function(){
                          me.selectStar = this.index
                          me.starShow()
                          console.log(`you pinfen is ${this.index + 1}`)
                        }
                    }
                },
                mouseout :function(){
                    var me = this;
                  
                    for(var i =0 ; i<this.lis.length; i++){
                        //解决闭包问题
                        this.lis[i].index = i;                        
                        this.lis[i].onmouseout = function(){
                              
                             me.starShow(0)
                          //console.log(`you pinfen is ${this.index + 1}`)
                        }
                    }
                   // this.starShow(0)
                }
            }


            var starObj = new Star()
            console.log(starObj)

            starObj.run()
```