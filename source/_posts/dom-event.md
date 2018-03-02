
---
title: dom事件
date: 2018-03-2 14:13:23
tags: js
category: "js" 
---

### **dom事件级别**
DOM0：element.oncick= fn
DOM2: element.addEventListener('click',fn,false) / attachEvent
DOM3: element.addEventListener('keyup',fn,false) 

### **事件模型**
冒泡或者捕获
事件流：事件通过捕获到达目标元素（捕获阶段），从目标元素上传到window对象（冒泡阶段）
捕获：从window->document->html


### **事件对象**
event.preventDefault() 阻止默认行为(链接跳转)
event.stopProgatation() 阻止冒泡
```
document.getElementById('list').addEventListener('click', function (e) {
  // 兼容性处理
  var event = e || window.event;
  var target = event.target || event.srcElement;
  // 判断是否匹配目标元素
  if (target.nodeName.toLocaleLowerCase === 'li') {
    console.log('the content is: ', target.innerHTML);
  }
});

```







