---
layout: post
title:  sticky-footer
date:   2016-09-21 01:08:00 +0800
categories: css
tag: css
---


sticky-footer的含义就是用在手机端，如果内容过短 footer就固定在底部 ,如果内容长footer就跟着页面走
```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="maximum-scale=1.0,minimum-scale=1.0,user-scalable=0,width=device-width,initial-scale=1.0" />
    <title>sell</title>
    
    <style>
    	*{margin:0; padding: 0;}
    	body,html{height: 100%}
    	.clearfix{display: inline-block;}
    	.clearfix:after {content: "."; display: block; height:0; clear:both; visibility: hidden;}
    	.detail-wrapper{min-height: 100% ; width: 100%}
	   .detail-wrapper .detail-main{margin-top: 64px; padding-bottom: 64px; text-align: center; color: #000;}
	   .detail-wrapper .detail-main p{text-align: center;}
	   .detail-close{position: relative; width: 32px; height: 32px; margin:-64px auto 0 auto; clear: both; font-size: 32px;}
    </style>
</head>

<body>
  
   	<div class="detail-wrapper clearfix">
          <div class="detail-main">
              <p>111111111</p> 
              <p>111111111</p> 
              <p>111111111</p> 
              <p>111111111</p> 
              <p>111111111</p> 
              <p>111111111</p>
          </div>
     </div>
     <div class="detail-close">X</div>
</body>

</html>

```

