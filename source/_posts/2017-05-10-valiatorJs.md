---
layout: post
title:  验证JS
date:   2017-05-10 01:08:00 +0800
categories: js
tag: js
---


HTML
====================================
```
var  checkTools={       
            /*判断是否为null和undefined*/
            isNull:function(str){       
                //先判断是否undefined
                return (typeof(str) == undefined || str == null || str.length ==0) ? false : true;
            },
            stringCheck : function(str){
                    if(this.isNull(str)){
                        var ruleStr=/^[a-zA-Z0-9_\u4e00-\u9fa5]+$/;
                        return ruleStr.test(str);   
                    }else{
                            return false;   
                    }
            },
            //验证手机号
             phoneCheck : function(str){
                if(this.isNull(str)){
                    var ruleStr=/^(13[0-9]|15[0-9]|17[0-9]|18[0-9]|14[57])[0-9]{8}$/;
                    return ruleStr.test(str);
                }else{
                            return false;   
                }
            },
        //验证邮箱
         emailCheck : function(str){
            if(this.isNull(str)){
                var ruleStr=/^([a-zA-Z0-9_\-\.]+)@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.)|(([a-zA-Z0-9\-]+\.)+))([a-zA-Z]{2,4}|[0-9]{1,3})(\]?)$/;
                return ruleStr.test(str);
            }else{
                        return false;   
            }
        },
        //验证字符长度(验证输入字符串长度)
         strlenCheck : function(str,starNum,overNum){
                if(this.isNull(str)){
                    var len = parseInt(str.replace(/[^\x00-\xff]/g, '__').length);
                    var tag = false;
                    if(len<overNum && len>starNum){
                        tag=true;
                    }
                    return tag;
                }else{
                        return false;   
                }
        },      
        /*去除空格*/
        trimSpace : function(str){
            if(this.isNull(str)){
                var str=str.replace(/(^\s*)|(\s*$)/g,'');
                return str;
            }
        }
}

```





