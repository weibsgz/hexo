
---
title: 用es6-class封装一个删除节点的方法
date: 2017-10-10 14:13:23
tags: js
category: "js" 
---

需求：实现一个九宫格布局，每个格子都有个删除按钮，点击按钮删除当前的宫格

```
<html>
<head>
  <title></title>
</head>

<style type="text/css">
  *{padding: 0; margin: 0}
      ul{display: flex; width: 300px;flex-wrap: wrap; list-style-type: none; margin:50px;}
      li{
            flex:0 0 100px;
            height: 100px;
            line-height: 100px;
            text-align: center;
            border: 4px solid #ccc;
            box-sizing: border-box;
            margin-top: -4px;
            margin-right: -4px;
      }
      li:hover{
            border-color: red;
            z-index: 2
      }
</style>
<body>
      <ul id="list">
       <li>1 <span class="del">X</span></li>
       <li>2 <span class="del">X</span></li>
       <li>3 <span class="del">X</span></li>
       <li>4 <span class="del">X</span></li>
       <li>5 <span class="del">X</span></li>
       <li>6 <span class="del">X</span></li>
       <li>7 <span class="del">X</span></li>
       <li>8 <span class="del">X</span></li>
       <li>9 <span class="del">X</span></li>
      </ul>
  <script type="text/javascript">
            class List{
                  constructor(dom){
                        this.ulObj = document.getElementById(dom);
                        this.ulObj.addEventListener('click',(e)=>{
                              
                              if(e.target.className === 'del') {
                                    this.removeItem(e.target)
                              }
                        })
                  }
                  removeItem(target){
                        let parent = target.parentNode
                        this.ulObj.removeChild(parent)

                  }
            } 

            window.addEventListener('DOMContentLoaded',function(){
                  new List('list')
            })
       

            
      </script>
</body>
</html>

```
