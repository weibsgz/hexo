---
title: socket.io使用
date: 2017-09-03 14:13:23
tags: 工具
category: "工具"  # 分类为 工具
---
### **安装**
安装一种服务器的 一种客户端的

`npm install socket.io --save`
`npm install socket.io-client --save`

### **EXPRESS服务端**

用server.listen就将express和 socket.io结合起来了
```
const app = express();
const server = require('http').Server(app)
const io = require('socket.io')(server)



// on  是 监听 参数socket是当前这次链接的请求 Io是全局请求
io.on('connection',function(socket){
    console.log('user login')
    socket.on('sendmsg',function(data){
        console.log(data)
        io.emit('recvmsg',data)  //再次把请求放到全局中
    })
})


//
server.listen(9093,function(){
    console.log('node server start at port 9093')
})

```



### **页面处理**

```
import io from 'socket.io-client'
   //因为后台在9093 前台在3000 跨域 需要手动指定下,
   如果不需要跨域直接 socket=io() 就行？
const socket = io('ws://localhost:9093')

//发送一个请求
socket.emit('sendmsg',{text:this.state.text})


componentDidMount(){       
        //监听全局的
        socket.on('recvmsg',function(data){
            console.log(data)
        })

    }
```





### **html**
```

```



