---
title: css多行文字垂直居中
date: 2017-10-09 14:13:23
tags: css
category: "css"  # 分类为 css
---




关键样式：
① 父元素（这里是div）设置和高度一致的 line-height （这里是200px）--- 由后面的vertical-align定义看，这是为了设置div的基线
② 子元素 (这里是span) 设置合适的line-height，并设置display:inline-block、vertical-align: middle;  --- inline水平的元素无法设置line-height。所以这里要设置inline-block。


```
<style>  
    *{padding: 0;margin:0;font-size: 12px;}  
    div{float: left;width: 200px;height:200px;margin: 10px;border:1px solid blue; line-height: 200px;}  
    span{display: inline-block;vertical-align: middle;line-height: 22px;}  
</style>  
  
<div>  
    <span>测试文字测试文字</span>  
</div>  
<div>  
    <span>测试文字 <br/> 测试文字<br/> 测试文字<br/> 测试文字<br/> 测试文字<br/> 测试文字</span>  
</div>  
```









