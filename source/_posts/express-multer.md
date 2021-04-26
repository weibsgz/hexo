---
title: 使用express 结合multer中间件上传文件
date: 2018-02-23 14:13:23
tags: node
category: 'node'
---

1. server.js

```
const express = require('express')
const fs = require('fs')
var multer = require('multer')
//Multer 是一个 node.js 中间件，用于处理 multipart/form-data 类型的表单数据
//指定上传文件的目录，再当前文件夹下的uploads  但是存储的是一段哈希值的文件，所以用下面的storage方法
// var upload = multer({ dest: 'uploads/' })

//判断目录不存在就创建目录
var createFolder = (folder) => {
  try {
    fs.accessSync(folder)
  } catch (e) {
    fs.mkdirSync(folder)
  }
}
var uploadFolder = './upload/'
createFolder(uploadFolder)

//控制存储 将上传的文件存在指定文件夹下
var storage = multer.diskStorage({
  //放置目标位置
  destination: function (req, file, cb) {
    cb(null, uploadFolder)
  },

  //上传文件的名称和扩展名一起originalname
  filename: function (req, file, cb) {
    cb(null, file.originalname)
  },
})

var upload = multer({ storage: storage })

const port = 8000

const app = express()

//form路由打开页面 是个表单 可提交文件
app.get('/form', (req, res) => {
  var form = fs.readFileSync('./index.html', { encoding: 'utf-8' })
  res.send(form)


  //或者直接使用express提供的sendFile
  // res.sendFile(__dirname + '/index.html')
})

//提交的name 是 logo
app.post('/upload', upload.single('logo'), (req, res) => {
  res.send({ code: 0 })
})

app.listen(port, () => {
  console.log('server is running at port: ' + port)
})



```

2. 表单页面 同目录下

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <!-- formdata处理数据 -->
    <form action="/upload" method="post" enctype="multipart/form-data">
      <h2>单图上传</h2>
      <input type="file" name="logo" />
      <input type="submit" value="提交" />
    </form>
  </body>
</html>


```
