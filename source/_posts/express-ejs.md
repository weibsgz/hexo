---
title: express ejs模板使用
date: 2018-02-25 14:13:23
tags: node
category: 'node'
---

1. server.js

```
const express = require('express')
const fs = require('fs')
const path = require('path')
const port = 8000

const app = express()

// 设置模板引擎 路径是views目录下
app.set('views', path.join(__dirname, 'views'))
app.set('view engine', 'ejs')

//form路由打开页面 是个表单 可提交文件  访问路由：http://localhost:8000/form/weibin
app.get('/form/:name', (req, res) => {
  const person = req.params.name

  const data = { age: 18, hobbie: ['eating', 'sleeping', 'working'] }

  //渲染数据并返回html.index.ejs文件
  res.render('index', { person: person, data: data })
})

app.listen(port, () => {
  console.log('server is running at port: ' + port)
})



```

2. server.js 下同目录文件夹 views 下 index.ejs

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <%- include('header') -%>
    <h2><%= person %></h2>
    <h2><%= data.age %></h2>
    <h2>hobbie：</h2>
    <ul>
      <% data.hobbie.forEach((item)=>{ %>
      <li><%= item %></li>
      <% }) %>
    </ul>
    <!-- formdata处理数据 -->
    <form action="/upload" method="post" enctype="multipart/form-data">
      <h2>ejs模板引擎</h2>
      <input type="file" name="logo" />
      <input type="submit" value="提交" />
    </form>
  </body>
</html>


```
