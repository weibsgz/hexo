---
title: VueJS 开发常见问题集锦
date: 2017-07-28 14:13:23
tags: vue
category: "vue" 
---

### **封装echarts组件**
在父组件 data里 必须定义好图形初始的必须值，否则报错
```
 lineChartsData:{
                id:'lineChart',
                height : 500,
                option:{
                    "xAxis":{
                        "name":"",
                        "data":[]
                    },
                    "series":{
                        "name":"",
                        "data":[]
                    }
                }
  },

```

传递数据只改变option的值 需要在封装的echarts组件里 watch
```
watch:{
        lineChartsData: {
          handler: function (val, oldVal) { 
            console.log("watch")
            this.initLineChart()
          },
          deep: true  //增加deep 观察对象的子对象变化
        }
    }

```

### **element-ui 的样式需要写在public.css里  因为如果写在封装的 elementui组件里 即使去掉scoped 打包build后还是不能覆盖原有样式**



### **给分页组件传递当前页page 分页组件内部要通过watch 把page变成currentPage 否则会有警告 大义是子组件通过不正确的方法更改了父组件的值**
父组件：
```
    <pagination
            :pageSize = "pageSize"
            :total="total"
            :layout = "layout"
            :page="page"
            @pageChange="pageChange"
            >
    </pagination>
```

分页组件:
```
    <el-pagination
        :page-size="pageSize"
        :total="total"
        :layout = "layout"
        :current-page.sync = "currentPage"
        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        >
    </el-pagination>

      watch: {
        page: function (val, oldVal) {
              this.currentPage = val
           }
      }
```




### **polyfill 与 transform-runtime**

首先，vue-cli 为我们自动添加了 babel-plugin-transform-runtime 这个插件，该插件多数情况下都运作正常，可以转换大部分 ES6 语法。
但是，存在如下两个问题：

异步加载组件时，会产生 polyfill 代码冗余
不支持对全局函数与实例方法的 polyfill

两个问题的原因均归因于 babel-plugin-transform-runtime 采用了沙箱机制来编译我们的代码（即：不修改宿主环境的内置对象）。

由于异步组件最终会被编译为一个单独的文件，所以即使多个组件中使用了同一个新特性（例如：Object.keys()），那么在每个编译后的文件中都会有一份该新特性的 polyfill 拷贝。如果项目较小可以考虑不使用异步加载，但是首屏的压力会比较大。

不支持全局函数（如：Promise、Set、Map），Set 跟 Map 这两种数据结构应该大家用的也不多，影响较小。但是 Promise 影响可能就比较大了。

不支持实例方法（如：'abc'.include('b')、['1', '2', '3'].find((n) => n 等等），这个限制几乎废掉了大部分字符串和一半左右数组的新特性。

一般情况下 babel-plugin-transform-runtime 能满足大部分的需求，当不满足需求时，推荐使用完整的 babel-polyfill。

首先，从项目中移除 babel-plugin-transform-runtime
卸载该依赖：
```
npm un babel-plugin-transform-runtime -D
```
修改 babel 配置文件
```
// .babelrc
{
  //...
  "plugins": [
    // - "transform-runtime"
  ]
  //...
}
```
然后，安装 babel-polyfill 依赖：
```
npm i babel-polyfill -D
```
最后，在入口文件中导入
```
// src/main.js
import 'babel-polyfill'
```

### **Moment.JS 与 Webpack**

在使用 Moment.js 遇到一些问题，发现最终打包的文件中将 Moment.js 的全部语言包都打包了，导致最终文件徒然增加 100+kB。查了一下，发现可能是 webpack 打包或是 Moment.js 资源引用问题（?），目前该问题还未被妥善处理，需要通过一些 trick 来解决这个问题。

在 webpack 的生产配置文件中的 plugins 字段中添加一个插件，使用内置的方法类 ContextReplacementPlugin 过滤掉 Moment.js 中那些用不到的语言包：
```
// build/webpack.prod.conf.js
new webpack.ContextReplacementPlugin(/moment[\\/]locale$/, /^\.\/(zh-cn)$/)
```
解决方案采自 oleg-nogin@webpack/webpack#3128。
问题讨论详见 GitHub Issue: moment/moment#2373、webpack/webpack#3128。


### **自定义路径别名**

```
// build/webpack.base.js
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      'common':resolve('src/common'),
      'components':resolve('src/components'),
      'api': resolve('src/api'),
      'base':resolve('src/base')
    }
  }
```

### **获取表单控件值**
通常我们可以直接使用 v-model 将表单控件与数据进行绑定，但是有时候我们也会需要在用户输入的时候获取当前值（比如：实时验证当前输入控件内容的有效性）。

这时我们可以使用@input或 @change事件绑定我们自己的处理函数，并传入 $event象以获取当前控件的输入值：
```
<input type="text"/>

change (e) {
  let curVal = e.target.value
  if (/^\d+$/.test(curVal)) {
    this.num = +curVal
  } else {
    console.error('%s is not a number!', curVal)
  }
}
```


### **项目路径配置**

由于 vue-cli 配置的项目提供了一个内置的静态服务器，在开发阶段基本不会有什么问题。但是，当我们把代码放到服务器上时，经常会遇到静态资源引用错误，导致界面一片空白的问题。

这是由于 vue-cli 默认配置的 webpack 是以站点根目录引用的文件，然而有时候我们可能需要把项目部署到子目录中。

我们可以通过 config/index.js 来修改文件引用的相对路径：
```
build.assetsSubDirectory: 'static'
  build.assetsPublicPath: '/'
 
  dev.assetsSubDirectory: 'static'
  dev.assetsPublicPath: '/'
```
我们可以看到导出对象中 build 与 dev 均有 assetsSubDirectory、assetsPublicPath 这两个属性。

其中 assetsSubDirectory 指静态资源文件夹，也就是打包后的　js、css、图片等文件所放置的文件夹，这个默认一般不会有问题。

assetsPublicPath 指静态资源的引用路径，默认配置为 /，即网站根目录，与 assetsSubDirectory 组合起来就是完整的静态资源引用路径 /static。

写到这里解决方法已经很明显了，只要把根目录改为相对目录就好了：
```
 build.assetsSubDirectory: 'static'
  build.assetsPublicPath: './'
```
没错！就是一个 . 的问题。ㄟ( ▔, ▔ )ㄏ

### **使用vue-cli开发时跨域问题**

打开config文件夹下的index.js,配置proxyTable：

![百度](/uploads/vue1.png)

其中，taget是要访问的api的域名，配置完proxyTable后，若要访问'http://apis.haoservice.com/lifeservice/chicken/duck'，ajax里的url只需设为'/lifeservice/chicken/duck'(即绝对路径)即可，webpack会帮你把‘/lifeservice’下的请求转发至'http://apis.haoservice.com/lifeservice/chicken/duck'！





