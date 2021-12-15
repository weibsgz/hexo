---
layout: post
title:  express+babel+node-sass-middleware 处理简单页面
date:   2021-12-15 01:08:00 +0800
categories: node
tag: node
---

 - 我们处理一些简单的页面，不愿意使用构建工具，但是又想要使用scss和ES6语法
 - scss和js(es6)文件在 `./css` , `./js` 下 打包出来对应的文件在根目录下
 - 使用babel-cli命令行工具 参考文档 https://www.babeljs.cn/docs/babel-cli  安装 `npm install --save-dev @babel/core @babel/cli @babel/preset-env`
 - 安装`concurrently` 可以同时运行多个命令 不必等待第一个命令完成。
1. `package.json`

```
{
  "name": "y",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "dev": "concurrently \"node server.js\" \"npx babel ./js/index.js --watch --out-file ./index-es5.js --presets=@babel/preset-env\""
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/cli": "^7.16.0",
    "@babel/core": "^7.16.5",
    "babel-preset-env": "^1.7.0",
    "express": "^4.17.1",
    "node-sass-middleware": "^0.11.0"
  },
  "dependencies": {
    "@babel/preset-env": "^7.16.5"
  }
}


```

2. `server.js`

```
const express = require('express')
var path = require('path')
/**node sass */

var sassMiddleware = require('node-sass-middleware')

const app = express()

app.use(
  sassMiddleware({
    /* Options */
    src: path.join(__dirname, 'css'),
    dest: __dirname,
    debug: true
  })
)
 app.use(express.static('./'))


app.get('/', (req, res) => {
  res.sendFile(__dirname + '/' + 'index.html') //设置/ 下访问文件位置
})
//'10.200.16.100'
app.listen('3000', () => {
  console.log('your1 app listening at port 3000')
})


```

3. 运行`npm run dev`