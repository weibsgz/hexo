
---
title: rem相关问题
date: 2018-02-28 14:13:23
tags: css
category: "css" 
---

### **利用SASS转换REM**
这里以IPONE6标准375宽度 / 10 = 37.5作为基准值 10这个数字是随便写的， 当然也可以选择不除，只要在后面动态js计算时保证一样的值就可以
```
@function px2rem($px) {
  $rem: 37.5px;  
  @return ($px / $rem) + rem; 
} 



//使用
  .header{
    height:px2rem(40px);
  }

```



### **配合用JS转换**
```
<script>

    window.onresize = function() {
       change()
     }
     change()
    function change() {
      let html = document.documentElement || document.body;
      html.style.fontSize = html.clientWidth / 10  + "px";
    }
</script>
```


### **炎黄JS方案**

```
new function (){
  var _self =  this ;
  _self.width = 640; // 设置默认最大宽度 
  _self.fontSize = 100; // 默认字体大小 
  _self.widthProportion =  function (){ var p = (document.body&&document.body.clientWidth||document.getElementsByTagName("html")[0].offsetWidth)/_self.width;return p>1?1:p<0.5?0.5:p;};
  _self.changePage =  function (){
  document.getElementsByTagName("html")[0].setAttribute("style","font-size:"+_self.widthProportion()*_self.fontSize+"px !important");
  }
  _self.changePage();
  window.addEventListener('resize', function (){_self.changePage();}, false );
  };
```








