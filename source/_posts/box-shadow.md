
---
title: 用box-shadow做背景部分透明效果
date: 2017-11-09 14:13:23
tags: css
category: "css" 
---

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body{
            background:#6EC3FF;
        }
        .hl-box{
            position: absolute;
            top: 400px;
            left: 300px;
            width: 300px;
            box-shadow: 0 0 1px 2000px rgba(0,0,0,.5), 0 2px 15px rgba(0,0,0,.4);
            height: 200px;
            border-radius: 4px;
            transition: all 1s;
        }
    </style>
</head>
<body>
    <div class="hl-box"></div>
    <h1>爱上大还是打开就卡萨爱上大还是打开就卡萨爱上大还是打开就卡萨丁和</h1>
    <h1>爱上大还是打开就卡萨爱上大还是打开就卡萨爱上大还是打开就卡萨丁和</h1>
    <h1>爱上大还是打开爱上大还是打开就卡萨爱上大还是打开就卡萨就卡萨丁和</h1>
    <h1>爱上大还是打开爱上大还是打开就卡萨爱上大还是打开就卡萨就卡萨丁和</h1>
    <h1>爱上大还是打爱上大还是打开就卡萨爱上大还是打开就卡萨开就卡萨丁和</h1>
    <h1>爱上大还是打爱上大还是打开就卡萨爱上大还是打开就卡萨开就卡萨丁和</h1>
    <h1>爱上大还是打爱上大还是打开就卡萨爱上大还是打开就卡萨开就卡萨丁和</h1>
    <h1>爱上大还是打爱上大还是打开就卡萨爱上大还是打开就卡萨开就卡萨丁和</h1>
    <h1>爱上大还是打爱上大还是打开就卡萨爱上大还是打开就卡萨开就卡萨丁和</h1>

<script>
    // window.onload=function(){
    //     setTimeout(function(){
    //        document.getElementsByClassName('hl-box')[0].style.left='500px';
    //     },1000)
    // }
</script>
</body>
</html>
```



![显示效果](/uploads/box-shadow.png)












