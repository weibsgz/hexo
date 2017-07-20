---
layout: post
title:  封装js
date:   2017-01-25 01:08:00 +0800
categories: js
tag: js
---

HTML
====================================


```
<!DOCTYPE html>

<head>
    <title>css3滑动搜索</title>
    <meta charset="UTF-8">
    <style>
    html,
    body {
        height: 100%;
        overflow: hidden;
    }
    
    .main {
        width: 900px;
        margin: 0 auto;
        font-size: 0;
    }
    
    .main .tab {
        display: inline-block;
        width: 33%;
        height: 40px;
        line-height: 40px;
        text-align: center;
        border: 1px solid #ccc;
        font-size: 14px;
        border-right: 0px
    }
    
    .main .tab:last-child {
        border-right: 1px solid #ccc;
        position: relative;
    }
    
    .tab i {
        width: 100%;
        height: 2px;
        background-color: red;
        display: none;
        transition: all .5s
    }
    
    .active {
        color: red;
    }
    
    .active i {
        display: block;
    }
    
    .tab a {
        display: inline-block;
        transition: all 0.8s ease-in-out;
    }
    
    .tab a:hover {
        color: green;
        transform: rotateY(360deg);
    }
    
    #dialg {
        width: 400px;
        height: 400px;
        z-index: 10000;
        background-color: #fff;
        position: absolute;
        display: none;
    }
    
    #dialg .close {
        position: absolute;
        right: 0;
        top: 0
    }
    /*loading动画*/
    
    .loadDiv {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        -webkit-transform: translate(-50%, -50%);
        background-color: #fff;
        z-index: 10000;
        color: #fff;
        line-height: 40px;
        padding: 0 10px 0 10px;
        border-radius: 3px;
        white-space: nowrap;
        background-color: rgba(0, 0, 0, 0.5);
        text-align: center;
        min-width: 80px;
    }    
  
    
    .ldGif {
        position: absolute;
        left: 10px;
        top: 5px;
    }
    
    @-webkit-keyframes line-scale {
        0% {
            -webkit-transform: scaley(1);
            transform: scaley(1);
        }
        50% {
            -webkit-transform: scaley(0.4);
            transform: scaley(0.4);
        }
        100% {
            -webkit-transform: scaley(1);
            transform: scaley(1);
        }
    }
    
    @keyframes line-scale {
        0% {
            -webkit-transform: scaley(1);
            transform: scaley(1);
        }
        50% {
            -webkit-transform: scaley(0.4);
            transform: scaley(0.4);
        }
        100% {
            -webkit-transform: scaley(1);
            transform: scaley(1);
        }
    }
    
    .line-scale > div:nth-child(1) {
        -webkit-animation: line-scale 1s 0.1s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
        animation: line-scale 1s 0.1s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
    }
    
    .line-scale > div:nth-child(2) {
        -webkit-animation: line-scale 1s 0.2s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
        animation: line-scale 1s 0.2s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
    }
    
    .line-scale > div:nth-child(3) {
        -webkit-animation: line-scale 1s 0.3s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
        animation: line-scale 1s 0.3s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
    }
    
    .line-scale > div:nth-child(4) {
        -webkit-animation: line-scale 1s 0.4s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
        animation: line-scale 1s 0.4s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
    }
    
    .line-scale > div:nth-child(5) {
        -webkit-animation: line-scale 1s 0.5s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
        animation: line-scale 1s 0.5s infinite cubic-bezier(0.32, 0.96, 0.18, 1.08);
    }
    
    .line-scale > div {
        background-color: #fff;
        width: 4px;
        height: 25px;
        border-radius: 2px;
        margin: 2px;
        display: inline-block;
    }
    </style>
</head>

<body>
    <div class="main">
        <div class="tab active"><a>标题1</a><i></i></div>
        <div class="tab"><a>标题1</a><i></i></div>
        <div class="tab"><a>标题1</a><i></i></div>
    </div>
    <div class="content">1111
        <button id="tcBtn">弹出层</button>
    </div>
    <div class="content" style="display: none;">
        <button id="loadingBtn">loading</button>
        <button id="hideLoadingBtn">hide</button>
        <div id="loading-wrapper" style="width: 500px; height: 500px; border:1px solid #ccc;">
            22222222222222222222222222
        </div>
    </div>
    <div class="content" style="display: none;">3333</div>
    <div id="dialg">
        <p>弹出层自己写</p>
        <div class="close">关闭</div>
    </div>
    <script src="jquery-1.10.1.min.js"></script>
    <script type="text/javascript">
    var Util = (function() {
        var tab = function(el, className, content) {
            var index = $(el).index();
            $(el).addClass(className).siblings().removeClass(className);
            $(content).eq(index).show().siblings(content).hide()
        }
       /* var tc = function(containerId) {
            mengceng('body');
            $(containerId).show();
            $(containerId).css({
                    "position": "absolute",
                    "left": "0",
                    "top": "0",
                    "bottom": "0",
                    "right": "0",
                    "margin": "auto"
                })                
        }*/

        var tc= {
            init:function(containerId){
                mengceng('body');
                $(containerId).show();
                $(containerId).css({
                    "position": "absolute",
                    "left": "0",
                    "top": "0",
                    "bottom": "0",
                    "right": "0",
                    "margin": "auto"
                })
            },
            closeTc:function(containerId){
                 $("#mengceng").remove();
                $(containerId).hide()
            }
        }





        var mengceng = function(dom) {
            if (document.getElementById('mengceng') === null) {
                var maskHtml = '<div id="mengceng"></div>';
                if(dom){
                    $(dom).append(maskHtml)
                }
                else{
                    $("body").append(maskHtml); 
                }
               
                $("#mengceng").css({
                    "background-color": "#000",
                    "width": "100%",
                    "height": "100%",
                    "position": "absolute",
                    "z-index": "9999",
                    "left": "0",
                    "top": "0",
                    "opacity": "0.6"
                });
            } else {
                $("#mengceng").show()
            }
        }


       /* var closeTc = function(containerId) {
            $("#mengceng").remove();
            $(containerId).hide()
        }*/

        var Loading = function(el, config) {
            var defaults = {
                msg: '数据加载中,请稍后',
                mask: true,
                img: true,
                el: $(el),
                callback: function() {
                    alert(1)
                }
            };
            this.config = $.extend(defaults, config);
        }

        Loading.prototype = {
            init: function() {
                console.log(this.config)
                var loadDiv = $("<div class='loadDiv'>" + this.config.msg + "</div>");
                this.config.el.css("position", "relative");
                this.config.el.append(loadDiv);
                if (this.config.mask) {
                    mengceng(this.config.el);
                }

                if (this.config.img) {
                    var img = $("<div class='loader-inner line-scale'>" +
                        "<div></div>" +
                        "<div></div>" +
                        "<div></div>" +
                        "<div></div>" +
                        "<div></div>" +
                        "</div>");
                    this.config.el.find('.loadDiv').append(img);
                } else {
                    var img = $("<div class='loadingGif'><img src='loading2.gif' width='80' /></div>");
                    this.config.el.find('.loadDiv').append(img);
                }

            },
            destroy:function(){
                $("#mengceng").remove();
                $(".loadDiv").remove()
            }
        }



        return {
            tab: tab,
            tc: tc,           
            loading: Loading
        }
    })();

    $(function() {
        $(".tab").on('click', function() {
            Util.tab(this, "active", ".content")
        })

        $("#tcBtn").on("click", function() {
            Util.tc.init("#dialg")
        })

        $(".close").on("click", function() {
            Util.tc.closeTc("#dialg")
        })


        var load = new Util.loading("#loading-wrapper",{
                msg:'123'
            });
        $("#loadingBtn").on("click", function() {            
            load.init()
        })

        $("#hideLoadingBtn").on("click",function(){
            load.destroy()
        })  
    })
    </script>
</body>

</html>


```