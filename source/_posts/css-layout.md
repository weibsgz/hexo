
---
title: css经典三栏布局解析
date: 2018-03-2 14:13:23
tags: css
category: "css" 
---

### **每个解决方案的优缺点**
<h2>
      <p>1.float布局 兼容性好 但是需要清除浮动</p>
      <p>2.绝对定位 布局快捷，但是子元素也要脱离文档流</p>
      <p>3.flex 布局比较好 优先选择 如果不固定高度也可以用</p>
      <p>4.table 布局 兼容性好 如果不固定高度也可以用</p>
      <p>5.grid布局 新技术 兼容性不太好</p>
</h2>



### **5种CSS**
```
<style type="text/css">
      *{padding: 0; margin: 0}
      .box{height: 300px; margin-top: 50px;}
      .left,.right{width: 300px; height: 100%;}
      .left{background-color: red}
      .right{background-color: blue}
      .center{background-color: yellow; height: 100%;}
      /*第一种 float 布局 */    
      .box1 .left{float: left;}
      .box1 .right{ float: right; }
      .box1 .center{background-color: yellow; height: 100%;}

      /*第二种  绝对定位布局*/
      .box2{position: relative;width: 100%;}
      .box2 .left{position: absolute; left: 0; top: 0; }
      .box2 .right{position: absolute; right: 0; top: 0; }
      .box2 .center{position: absolute; left: 300px; right: 300px; top: 0}

      /*第三种  flex*/
      .box3{display: flex;}
      .box3 .left {flex:0 0 300px;}
      .box3 .right {flex: 0 0 300px;}
      .box3 .center {flex: 1}

      /*第四种 表格布局*/
      .box4 {display: table; width: 100%;}
      .box4 .left {display: table-cell;}
      .box4 .right {display: table-cell;}
      .box4 .center {display: table-cell;}

      /*第5种 grid 分为3列*/
      .box5 { 
          display: grid; 
          width: 100%;
          grid-template-columns: 300px auto 300px;
       }

   </style>
```


### **HTML**

```
<section>
       <div class="box1 box">
         <div class="left"></div>
         <div class="right"></div>
         <div class="center">
           <p>注意要先写left,right的div ,center布局在最后</p>
         </div>
       </div>
    </section>

    <section>
        <div class="box2 box">
           <div class="left"></div>
           <div class="center">绝对定位写法</div>
           <div class="right"></div>
        </div>
    </section>

    <section>
        <div class="box3 box">
           <div class="left"></div>
           <div class="center">flex写法</div>
           <div class="right"></div>
        </div>
    </section>

    <section>
        <div class="box4 box">
           <div class="left"></div>
           <div class="center">table</div>
           <div class="right"></div>
        </div>
    </section>

    <section>
        <div class="box5 box">
           <div class="left"></div>
           <div class="center">grid布局 </div>
           <div class="right"></div>
        </div>
    </section>
```








