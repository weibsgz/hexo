---
title: jquery 全选/反选/选中所有全选/取消一个取消全选
date: 2017-11-14 14:13:23
tags: js
category: "js"  # 分类为 js
---

```
//全选
  $("#checkall").click(function() {  
        var checked = $(this).is(":checked");
        console.log(checked)
        if(!checked){
            console.log('没选中')
            $("input[name='checkName']").each(function() {  
              $(this).prop("checked", false); 
            }); 
        }
        else{
          console.log('选中')
          $("input[name='checkName']").each(function() {  
            $(this).prop("checked", true); 
          }); 
        }
        
    }); 
    
    var chks =  $("input[name='checkName']")
    chks.click(function(){
            for(var i =0 ; i < chks.length ; i++){   
                if(!chks[i].checked){
                    console.log('有一个没选')
                    $("#checkall").prop("checked", false);
                    return ;
                }
            }
            console.log('都选了 ')
            $("#checkall").prop("checked",true);
        });
```
