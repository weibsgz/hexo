---
layout: post
title:  闭包封装方法
date:   2017-01-18 01:08:00 +0800
categories: js
tag: js
---
}


====================================
```
<!DOCTYPE html>
<html>

<head>
    <title>css style</title>
    <link rel="stylesheet" type="text/css" href="test.css">
</head>
<style type="text/css">
    #a{width: 300px; height: 50px; background-color: red;}
</style>
<body>
<div id="a">
    
</div>
</body>
<script type="text/javascript">
//公共方法
var Util = (function() {
    function fn1(a) {
        return alert(a)
    }
    var prefix = 'ficiton_reader_';
    var StorageGetter = function(key) {
        return localStorage.getItem(prefix + key);
    }
    var StorageSetter = function(key, val) {
        return localStorage.setItem(prefix + key, val);
    }
    return {
        fn1: fn1,
        StorageGetter:StorageGetter,
        StorageSetter:StorageSetter
    }
})();

//具体功能实现
function method_1()
{
    //var a = 1;
    var init = function(){
        Util.fn1("hahaha")
    }
    var fn2 = function(){
    alert("method里的方法")
    }
    return {
        init:init,
        fn2:fn2
    }
}

main();
//入口文件
function main(){
    Util.fn1("first")
    var obj= new method_1();
    obj.init()
    obj.fn2() 
    
    EventHandler()
}

//处理页面DOM的方法  可以先将DOM存起来 不用每次都去取
function EventHandler(){
    var dom = {
        a:document.getElementById("a")
    }
    dom.a.onclick=function(){
        alert(1)
    }
}
</script>

</html>

```
