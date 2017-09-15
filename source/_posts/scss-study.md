---
title: sass在项目中使用说明
date: 2017-08-18 14:13:23
tags: css
category: "css" 
---



### 结构
```
|-- 项目名
    |-- src 开发环境
        |-- assets  资产目录
            |-- css   样式基础库
                |-- index.sass  webpack main.js 中通过 import '../index.sass' 引入
                |-- reset.scss          重置样式库
                |-- variable.sass       变量文件
                |-- iconfont.css        字体文件
                |-- mixin.sass          宏文件
                |-- base.scss           定义项目 body 字体字号 等
                |-- frame.scss          框架文件
                |-- public.scss         共用样式
            |-- others  其它
```

### SCSS 与 SASS 说明
##### 1. 区别
SASS是Ruby语言写的，其语法与stylus语法大同小异，其语法以缩进定义层级，于前端并不友好。SCSS 借鉴其它预编译语言less对 SASS 做了扩充，使其语法支持大括弧与分号，两者语法不可以混用，但文件间可以相互引用。即 `a.scss` 可以 `@import 'b.sass` 同理 `b.sass` 亦可 `@import a.scss`，实际引入过程中可直接忽略掉文件后缀。如 `@import a`
##### 2. 为什么结构中同时引用 sass 与 scss
此举主要是针对 gulp 框架而做，gulp可以针对不同的文件类型做针对类型的处理，这样，在gulp处理我们自己编写的scss的时候，可以自动过滤掉sass，可以节省一定资源。
##### 3. sass文件
sass 文件主要承载一些 预处理宏 变量，在gulp中只需引入这些sass文件，而无需编译，从而节省资源，而 scss 文件在gulp实践生产当中则负责各个页面的样式和一些框架样式，必定需要编译

### index.sass 文件说明
index.sass 文件主要负责引入其它 基本样式重置文件 框架样式文件等，不可以在其中引入 包含宏 或者 变量的文件，webpack 的 sass-loader 并不能直接将这些宏 使各自的组件中直接使用  
**宏文件 和 变量文件只可在各自组件中单独引入**
```
@import "./reset"
@import "./base"
@import "./iconfont"

@import "./frame"
@import "./public"
```

##### 安装
vue-cli 中通过 `npm install sass-loader node-sass webpack --save-dev`
gulp 宏 `npm install gulp-sass --save-dev `
