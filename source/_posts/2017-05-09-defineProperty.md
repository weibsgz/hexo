---
layout: post
title:  双向绑定原理
date:   2017-05-09 01:08:00 +0800
categories: vue
tag: vue
---


HTML
====================================
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div class="box">
        <input type="text" id="a">
        <span id="b"></span>
    </div>
    <script type="text/javascript">
        var  obj = {}
        Object.defineProperty(obj,"hello",{
            get:function(){
                console.log("get方法触发了")
            },
            set:function(val){
                document.getElementById("a").value = val;
                document.getElementById("b").innerHTML = val
            }
        })

        document.getElementById("a").addEventListener('keyup',function(e){
            obj.hello = e.target.value
        });
    </script>
</body>
</html>
```





