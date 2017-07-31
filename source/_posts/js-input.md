---
title: 输入框输入延迟提交（避免每次输入都发请求）
date: 2016-07-15 14:13:23
tags: js
category: "js"  # 分类为 js
---







```
     <!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
<input id="inputObj"  />
<script type="text/javascript">
  
var inputObj = document.getElementById('inputObj')
var timer = null;
inputObj.addEventListener('input',function(){
  
  if(timer){
    clearTimeout(timer)
  }
  timer = setTimeout(function(){
    console.log(inputObj.value)
  },500)
},false)
</script>
</body>
</html>

```




