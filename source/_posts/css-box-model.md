
---
title: css盒模型
date: 2018-03-2 14:13:23
tags: css
category: "css" 
---

### **标准模型 和 IE模型区别**
1.标准模型不包含border padding , IE模型相反
2.用box-sizeing:border-box/content-box设置   默认是IE模型  content-box



### **js如何设置**
```
dom.style.width/height 取得内联样式
window.getComputedStyle(dom).width/height //兼容GOOGLE，MOZ的写法
dom.currentStyle.width/height //只有IE支持
dom.getBoundingClientRect().width/height //根据屏幕左上角取得的  返回4个值
                                         // top left width height
```


### **BFC**

https://www.w3cplus.com/css/understanding-block-formatting-contexts-in-css.html

概念：块级格式化上下文
用途：解决边距重叠问题（分别创建不同的BFC）
原理：bfc是一个独立的容器，外边的元素不会影响里边的，里边的也不会影响外边的
创建：overflow:hidden 或者 position不为static或者relative
清除浮动也可以用  设置父元素 overflow:auto/hidden 或者 float:left 让子元素高度也可以参与计算








