---
layout: post
title:  vuejs简易购物车
date:   2016-09-28 01:08:00 +0800
categories: vue
tag: vue
---

模拟假数据 data.js
```
var data = [
    {
        name:'ipone6',
        price:5499,
        id:1,
        count:1
    },
    {
        name:'ipad',
        price:3299,
        id:2,
        count:1
    },
    {
        name:'iMac',
        price:15999,
        id:3,
        count:1
    }
]
```

```
<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        *{margin: 0; padding:0;-moz-user-select: none; /*火狐*/
            -webkit-user-select: none; /*webkit浏览器*/-ms-user-select: none; /*IE10*/-khtml-user-select: none; /*早期浏览器*/
            user-select: none;}
        .products .item span{border: 1px solid #ccc; width: 100px; display: inline-block; text-align: center; height:40px; line-height:40px;}
        .products .item span em{width: 20px;height: 20px; text-align: center; border: 1px solid #ccc;
            background-color:orangered; color: #fff; display: inline-block;line-height: 20px; cursor: pointer;}
        .products .item span i{width: 50px; line-height: 30px; text-align: center; background: orangered; color: #fff;
            height:30px; font-size: 14px; font-style: normal; display: inline-block}
        .info{margin-top: 50px}
        .products .item span em.off{background-color: #ccc;}
    </style>
    <script src="vue.js"></script>
    <script src="data.js"></script>
</head>
<body>
    <div id="app">
       <!--用template 判断购物车是不是为空 做不同的显示-->
        <template v-if="data.length">
            <div class="products">
                <div class="item">
                    <span>商品名称</span>
                    <span>购买单价</span>
                    <span>商品数量</span>
                    <span>操作</span>
                </div>
                <div class="item" v-for="item in data">
                    <span>{{item.name}}</span>
                    <span>{{item.price | currency '￥' 0}}</span>
                    <span>
                    <em :class="{off:item.count == 1}" @click="reduce($index)">-</em>
                    {{item.count}}
                    <em @click="add($index)">+</em>
                </span>
                    <span>
                    <i v-on:click="remove($index)">移除</i>
                </span>
                </div>
            </div>
            <div class="info">
                <label for="">收件人:</label>
                <input type="text" v-model="name">
                <label for="">收货地址:</label>
                <input type="text" v-model="address">
            </div>
            <div class="result">
                <h3>清单</h3>
                <!--内置过滤器， 第三个参数代表保留几位-->
                商品总价：{{ price | currency '￥' 0}}<br>
                收件人：{{name}}<br>
                收货地址：{{address}}
            </div>
        </template>
        <template v-else>
            购物车为空
        </template>

    </div>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            data:data,
            name:"",
            address:""
        },
        computed:{
            //自动计算所有单价*数量
          price:function(){
              var price = 0;
              for(var i=0; i<this.data.length; i++)
              {
                  price += this.data[i].price* this.data[i].count
              }
              return price
          }
        },
        methods:{
            reduce:function(index){
                if(this.data[index].count == 1)
                {
                    return false
                }
                this.data[index].count--
            },
            add:function(index){
                this.data[index].count++
            },
            remove:function(index)
            {
                //spilce 方法 第一个参数是要删除的起始位置，第二个参数是要删除的个数
                this.data.splice(index,1)

              //  this.data.$remove(item) //如果上边传进来这个item 可以用这种VUE自带的方法直接删除这个ITEM；
            }
        }
    })
</script>
</body>
</html>
```

