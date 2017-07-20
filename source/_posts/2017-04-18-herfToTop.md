---
layout: post
title:  锚点平滑滚动
date:   2017-04-18 01:08:00 +0800
categories: js
tag: js
---






====================================

```
      $(".navbar-item").on("click", "li > a", function(){
        // 激活态
        $(".navbar-item ul a").removeClass("active");
        $(this).addClass("active");

        var href = $(this).attr("href");
        if (href.substring(href.length - 1) != "#") {
            var pos = $(href).offset().top;
            $("html,body").animate({scrollTop: pos}, 300);
            return false;
        }
    });

```




