---
layout: post
title:  vue1.0 transition效果
date:   2016-12-1 01:08:00 +0800
categories: vue
tag: vue
---


html
====================================
```
     <div v-show="detailShow" class="detail" transition="fade">
     .
     .
     .
     </div>
```




css
====================================


```
   .detail{position: fixed;z-index: 100;width: 100%; height: 100%;overflow:auto;background: rgba(7,17,27,0.8);top: 0; left: 0;transition: all 0.5s;backdrop-filter:blur(10px);
   }
   /*动画结束的状态*/
   .detail.fade-transition{
    opacity: 1;
    background: rgba(7,17,27,0.8);
   }
   /*动画开始的状态*/
   .detail.fade-enter,.detail.fade-leave{
    opacity: 0;
    background: rgba(7,17,27,0);
   }

```
