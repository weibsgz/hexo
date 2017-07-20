---
layout: post
title:  webpack-start
date:   2017-03-1 01:08:00 +0800
categories: 工具
tag: 工具
---



安装步骤
====================================
```
1.npm init

2.进入目录  npm install webpack --save-dev

npm install webpack -g
```



注意事项
====================================
1.读取css需要loader
npm install css-loader style-loader --save-dev

在入口的JS文件引入
require('style-loader!css-loader!./style.css')


2.一些参数

webpack hello.js hello.bundle.js --watch --progress --display-modules --display-reasons


使用webpack.config.js配置
====================================
改写package.json   --config webpack.config.js webpack.config.js改名了也用这个 

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "webpack":"webpack --config webpack.config.js --watch --progress --display-modules --display-reason --colors"
  },
```

npm run webpack 运行



html-webpack-plugin
====================================
```
npm install html-webpack-plugin --save-dev
var htmlWebpackPlugin = require('html-webpack-plugin')


module.exports={
   //单入口
   // entry:'./src/script/main.js',
   //多入口
    //entry:['./src/script/main.js','./src/script/a.js'],
    //穿一个对象，假设有3个页面a,b,c 每个页面都引入main.js 每个页面都有单独的JS a,b,c
    entry:{
        main:'./src/script/main.js',
        a:'./src/script/a.js',
        b:'./src/script/b.js',
        c:'./src/script/c.js',

    },
    output:{
        //注意路径 这里只配到dist根目录下  在filename中才指定js/
        path:'./dist',
        //如果entry 是一个对象，有多个键值对(chunk), filename就不能写死一个名字
        //其中hash是每次打包的哈希值，chunkhash 是每次打包都不同的哈希值 相当于md5
        filename:'js/[name]-[chunkhash].js',

        //打包上线后，替换路径为以下路径开头
       // publicPath:'http://cdn.com'
    },
    plugins: [
        //html-webpack-plugin 这个插件可以把打包生成js自动引入到页面中，因为打包生成的名字带有
        //哈希值，每次都不同
        new htmlWebpackPlugin({
            //模板参照根目录下的index.html,这样根目录的index.
            //html里的内容也会被搬进这个插件生成的index.html
             template: 'index.html',
             //指定带有hash值的名称 index-[hash].html,每次都生成不同的INDEX.HTML
             filename:'a.html',
             //可将JS直接插入到头部，不写就是尾部
            // inject:'head',
            //设置false 手动在 头尾自定义script引入位置
             inject:'body',
             //可在index.html中通过<%= htmlWebpackPlugin.options.title %>
             title:'this is a',
             //任何属性都可以被通过ejs模板方式引入到页面           
             //date:new Date(),
             //仅引入和自己页面相关的chunks
             chunks:['main','a']

             //压缩output
            /* minify:{
                //删除注释，空格
                removeComments:true,
                collapseWhitespace:true

             }*/

        }) ,

        new htmlWebpackPlugin({
            template: 'index.html',             
            filename:'b.html',
            inject:'body',
            title:'this is b',
            //date:new Date(),
            chunks:['main','b']
        }) ,

        new htmlWebpackPlugin({
            template: 'index.html',             
            filename:'c.html',
            inject:'body',
            title:'this is c',
            //date:new Date(),
            chunks:['c']
        })              
    ]
}
```



loader
====================================
1.安装babel loader(es6 转 es5)
 npm install --save-dev babel-loader babel-core

2,安装preset latest

npm install --save-dev babel-preset-latest

建立.babelrc  内容
{
    "presets":["latest"]
}


3.安装css-loaer postcss
 npm install css-loader style-loader --save-dev
 npm install postcss-loader --save-dev
 npm install autoprefixer --save-dev

 可以查看npm官网  postcss用法
webpack.config.js
{
     test: /\.css$/,
     loader:'style-loader!css-loader!postcss-loader'
 }

 根目录下建立postcss.config.js

 module.exports = {
  plugins: [
   require('autoprefixer')({
            browsers:['last 5 versions']
        })
  ]
}


4，安装less-loader
$ npm install -g less
npm install less-loader --save-dev


5.安装html-loader

npm install html-loader --save-dev

6,处理图片  

npm install file-loader --save-dev


npm install url-loader --save-dev
//处理图片可以用file-loader ,也可以用url-loader
 //limit 限制图片大小 200000代表200K 我插入的图片是109K
 //如果小于这个200K ，则插入BASE64编码，大于还是插入图片

 压缩图片

 npm install image-webpack-loader --save-dev

