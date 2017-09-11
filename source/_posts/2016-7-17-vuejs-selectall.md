---
layout: post
title:  vuejs实现全选
date:   2016-09-27 01:08:00 +0800
categories: vue
tag: vue
---




```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script type="text/javascript" src="js/vue.js"></script>
</head>
<body>
     <div id="app">
        <input type='checkbox' class='input-checkbox' v-on:click='checkedAll($event)'>全选
        <button @click="checkBtn($event)">全选</button>
        <template v-for='checkb in checkboxData'>
            <input type='checkbox' name='checkboxinput' class='input-checkbox' v-model='checkboxModel' value='{{checkb.id}}'>{{checkb.value}}
         </template>
    </div>


    <div id="app1">
        <input type="checkbox" value="Jack123" v-model="checkedNames">
        <label for="jack">Jack</label>
        <input type="checkbox"  value="John" v-model="checkedNames">
        <label for="john">John</label>
        <input type="checkbox"  value="Mike" v-model="checkedNames">
        <label for="mike">Mike</label>
        <br>
        <span>Checked names: {{ checkedNames | json }}</span>
    </div>
    <script type="text/javascript">
        var vm = new Vue({
            el:"#app",
            data:{
                    checkboxData:[{
                      id:'1',
                      value:'苹果'
                    },{
                      id:'2',
                      value:'荔枝'
                    },{
                      id:'3',
                      value:'香蕉'
                    },{
                      id:'4',
                      value:'火龙果'
                    }],
                    checkboxModel:['3','1','4'],
                    checked:""
            },
            methods:{
              checkedAll: function(event) {
               //event.currentTarget 取得当前的DOM元素
                var ischeck = event.currentTarget.checked
                var _this = this;
                if (!ischeck) {//实现反选
                  console.log(ischeck)
                  _this.checkboxModel = [];
                  // console.log(_this.checkboxModel);

                }else{//实现全选
                  console.log(ischeck)
                  _this.checkboxModel = [];
                  _this.checkboxData.forEach(function(item) {
                    _this.checkboxModel.push(item.id);
                  });
                }
              },
              checkBtn:function(event){
                var btnDom = event.currentTarget;
                var _this = this;
                if(btnDom.innerHTML == "全选")
                {
                     _this.checkboxModel = [];
                     _this.checkboxData.forEach(function(item) {
                     _this.checkboxModel.push(item.id);
                     btnDom.innerHTML = "取消全选"
                  });
                }
                else
                {
                    _this.checkboxModel = [];

                     btnDom.innerHTML = "全选"
                }
              }
            }

        })

        var vm1 = new Vue({
          el: '#app1',
          data: {
            checkedNames: []
          }
        })
    </script>
</body>
</html>
```

