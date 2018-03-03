
---
title: 前后端通信类问题汇总
date: 2018-01-3 14:13:23
tags: js
category: "js" 
---

### **同源策略**

协议，域名 和 端口  http://www.baidu.com:80,三者必须都一样，否则跨域，
非同源操作不了 cookie,ajax,dom



### **前端后端如何跨域通信**

1.JSONP
2.websocket （不限制同源策略）
3.cros   （支持同源，非同源通信）
4.postMessage (h5)


### **如何创建AJAX**
ajax请求过程：创建XMLHttpRequest（兼容性）、连接服务器、发送请求、服务器做出响应、接收响应数据 

### **GET,POST区别**
1.GET后退按钮/刷新无害，POST数据会被重新提交（浏览器应该告知用户数据会被重新提交）
2.GET能被缓存，POST不能缓存 。
3.GET历史参数保留在浏览器历史中。POST参数不会保存在浏览器历史中
4.GET对数据长度有限制，当发送数据时，GET 方法向 URL 添加数据；URL 的长度是受限制的（URL 的最大长度是 2048 个字符）。POST无限制。
5.GET的数据在 URL 中对所有人都是可见的。POST的数据不会显示在 URL 中


### **jsonp**

1.html标签的src属性没有同源限制（支持跨域）,创建script标签，src属性指向外部链接，并有一个和后端约定好的函数执行代码，服务端不再返回JSON格式的数据，而是返回一段调用某个函数的js代码，在src中进行了调用，这样实现了跨域

缺点：没有关于 JSONP 调用的错误处理。如果动态脚本插入有效，就执行调用；如果无效，就静默失败。失败是没有任何提示的