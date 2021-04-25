---
title: 使用express跨域代理外网域名接口，拿到本地使用
date: 2021-02-23 14:13:23
tags: node
category: 'node'
---

1. 本地页面使用

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="GBK" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <script
      type="text/javascript"
      src="//asserts.xcarimg.com/resource/common/jquery-1.8.3.min.js"
    ></script>
  </head>
  <body>
    this. is index.html
    <script>
      //URL 10.200.16.100 anywhere启动的域名 所以服务端也可以设置这个域名保持同源，
      //如果服务端随便起域名，需要设置允许跨域
      const url = 'http://10.200.16.100:4000/getAutoHome'
      $.ajax({
        url: url,
        success: function (res) {
          console.log(res)
        },
      })
    </script>
  </body>
</html>


```

2. 抓取目标页面接口

```
const axios = require('axios')
const express = require('express')
const app = express()

var Iconv = require('iconv-lite')
//主页 啥都没干
app.get('/', (req, res) => {
  res.json({
    err: 0,
  })
})

//设置请求中间件 设置响应头信息
app.all('*', function (req, res, next) {
  res.setHeader('Access-Control-Allow-Origin', 'http://10.200.16.100:7777')
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST')
  res.setHeader(
    'Access-Control-Allow-Headers',
    'Content-Type,Content-Length, Authorization, Accept,X-Requested-With"'
  )
  res.setHeader('Content-Type', 'application/json;charset=utf-8')
  next()
})

// app.use(express.json())
// app.use(express.urlencoded({ extended: false }))

//设置路由，前端就请求这个路由接口

app.get('/getAutoHome', (request, response) => {
  // 开始请求你要爬的外域名接口
  const url = 'https://www.yourWebSite.com/any/....'
  //他的接口所需要的参数
  const params = {
    cityid: '330600',
  }

  axios
    .get(url, {
      params: { ...params },
      // 如果接口返回的 是  gb2312的 需要用'iconv-lite' 插件 结合设置这个属性使用
      responseType: 'arraybuffer',
      headers: {
        //伪造来源
        referer: 'https://www.yourWebSite.com/',
        origin: 'https://www.yourWebSite.com/',
      },
    })
    .then((res) => {
      const data = res.data
      //因为接口请求的是GB2312的 所以需要转为UTF-8返回前端
      const _json = Iconv.decode(Buffer.from(data), 'gb2312')
      const html = Iconv.encode(_json, 'utf8').toString()
      console.log(html)
      response.json(JSON.parse(html))
    })
})

//这个IP 是anywhere生成的静态服务地址 也可以随便起个域名，那么要设置跨域允许
app.listen(4000, '10.200.16.100', () => {
  console.log('server is listenning at port 4000')
})


```
