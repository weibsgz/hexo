---
title: 使用express获取参数
date: 2018-02-23 14:13:23
tags: node
category: 'node'
---

```
const express = require('express')
const bodyParser = require('body-parser')

const app = express()
app.use(bodyParser.urlencoded({ extended: false }))
//app.use(bodyParser.json()) //如果需要发送JSON数据

//获取参数 query http://localhost:3333/?a=1
app.get('/', (req, res) => {
  res.send('this is homepage')
  console.log(req.query) //{a:1}
})

//获取参数 params http://localhost:3000/profile/123/user/weibin
app.get('/profile/:id/user/:name', function (req, res) {
  res.send(`your id is ${req.params.id},yourname is ${req.params.name}`) //your id is 123,yourname is weibin
})

//使用bodyParser 获取post的参数,参数挂到了req.body上面  使用postman测试
app.post('/', (req, res) => {
  console.dir(req.body)
})

app.listen(4000)


```
