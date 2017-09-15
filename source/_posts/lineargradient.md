---
title: liner-gradient学习写一个省略号
date: 2017-08-28 14:13:23
tags: css
category: "css" 
---



### 代码
```
  div {
            width: 100px;
            height: 2px;
            margin-top: 10px;
           
        }
        #test {

            background-image: linear-gradient(90deg,red 0, red 25%, transparent 25%,transparent 50%, red 50%,red 75%,transparent 75%,transparent 100%);  
            
            background-size: 8px 2px;
    
        }
        
```  
从0-25% 红色  25%-25%红色和透明色混合 就是突变为透明色 25%-50%透明色混合 还是透明色
50% - 50% 突变为红色  50%-75%红色....
 



