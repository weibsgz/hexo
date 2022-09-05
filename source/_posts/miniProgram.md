---
title: 小程序一些问题
date: 2022-08-31 14:13:23
tags: 小程序
category: "小程序"
---



### github 地址 
https://github.com/weibsgz/miniProgram

1. 关于WXS

- 小程序的运行环境分成渲染层和逻辑层，其中 WXML 模板和 WXSS 样式工作在渲染层，JS 脚本工作在逻辑层。响应的数据绑定系统通过this.setData()在两者之间进行数据传输。有频繁用户交互的效果在小程序上表现是比较卡顿的。比如你在bind:touchmove事件中去不停的setData试图改变一个节点的位置，这是行不通的，太卡了。
- 基于以上情况，微信小程序的设计者就提出了wxs的概念。 WXS 直接在渲染层做通信 性能消耗低
- WXS 不依赖于运行时的基础库版本，可以在所有版本的小程序中运行
- WXS 的运行环境和其他 JavaScript 代码是隔离的，WXS 中不能调用其他 JavaScript 文件中定义的函数，也不能调用小程序提供的API。
- WXS只能使用 JavaScript 的 ES5 语法
- 本项目中 components/tabs  中首页滑动内容区域执行wxs的 touchstart touchend方法。
- wxs也可以做数据格式化处理 /common/wxs/status-text.wxs


2. 如果多个页面都要用到的公共组件 可以放到app.json中声明

```
  "usingComponents": {
    "c-icon" : "/components/icon/icon",
    "c-tabs":"/components/tabs/tabs",
    "c-service-preivew" : "/components/service-preview/service-preview"
  },

```

3. 下拉刷新，需要在目标页JSON或者全局app.json中配置enablePullDownRefresh,默认加载中的样式是白色的，需要在app.json中修改backgroundTextStyle


4. IOS手机 
    - 底部橡皮筋白色 通过在app.json中设置 backgroundColorBottom为你的背景色解决
    - 顶部橡皮筋白色 通过在业务页面的JSON中设置 backgroundColorTop解决


5. position: sticky吸顶效果  如果他的父元素是 height:100%  那么他滚动到100%高度的时候就无效了 ，需要把父元素设置为 min-height:100%

- fixed 生成固定定位的元素，相对于浏览器窗口进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。
sticky 粘性定位，该定位基于用户滚动的位置。它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。


6. safe-area  只有IOS10以上才有底部一个黑条 他所在的区域就是安全区域，我们的业务内容需要避开这个区域

`components/safe-area/safe-area.wxss` 中是处理安全区域的组件  使用不同的边距样式


7. 外部样式类

- 组件JS中加入配置 `  externalClasses: [ 'i-button-class', 'i-button-special-class' ],`
- 组件WXML中 `<view wx:if="{{!special}}"class="i-button i-button-class>`
- 父组件 `<c-button i-button-class="my-i-button-class">`
- 父组件 wxss `.my-i-button-class {color: red !important;}`


8. 引入 WEUI

- https://developers.weixin.qq.com/miniprogram/dev/platform-capabilities/extended/weui/

- app.json中
```
  //mp-msg是某个组件

  "usingComponents": {    
    "mp-msg":"weui-miniprogram/msg/msg"
  },


"useExtendedLib": {
    "weui":true
}

```

9. 引入TIM SDK 

https://cloud.tencent.com/document/product/269/37411
https://www.npmjs.com/package/tim-wx-sdk


- 在小程序目录下     node.js > v12
```
npm init
npm install tim-wx-sdk --save
npm install tim-upload-plugin --save
```
- 小程序编辑器中 开发者工具菜单——工具——构建 npm


- https://console.cloud.tencent.com/ 注册账号 在搜索中搜索IM 在IM控制台创建项目 获取SDKAPPID 

`model/tim.js` 中初始化TIM

```
import TIM from 'tim-wx-sdk';
import TIMUploadPlugin from 'tim-upload-plugin';

let options = {
  SDKAppID: 你申请的SDKAPPID // 接入时需要将 0 替换为您的云通信应用的 SDKAppID，类型为 Number
};
// 创建 SDK 实例，`TIM.create()`方法对于同一个 `SDKAppID` 只会返回同一份实例
let tim = TIM.create(options); // SDK 实例通常用 tim 表示

// 设置 SDK 日志输出级别，详细分级请参见 setLogLevel 接口的说明
tim.setLogLevel(0); // 普通级别，日志量较多，接入时建议使用
// tim.setLogLevel(1); // release级别，SDK 输出关键信息，生产环境时建议使用

// 注册腾讯云即时通信 IM 上传插件
tim.registerPlugin({'tim-upload-plugin': TIMUploadPlugin});

// 接下来可以通过 tim 进行事件绑定和构建 IM 应用

``` 

- 项目中使用

```
import Tim from '../../model/tim'

new Tim()

```

- 看到IDE的控制台打印出TIM的信息 代表引入成功

- userSig生成 https://console.cloud.tencent.com/im/detail
- 用sdkAppid 和 密钥生成签名 本项目中前端生成签名 （开发阶段）实际使用时候必须后端生成签名
- 前端生成签名方法：
 - `libs/tim/两个文件`
 - generate-test-usersig.js 里 配置你的 用sdkAppid和密钥



 10. 安装MBOX

- package.json
 ```
  // 配置以下这两个依赖信息
  "mobx-miniprogram": "4.13.2",
  "mobx-miniprogram-bindings": "2.0.0"
 ```
 - 构建NPM

 - `/store/tim.js` 配置文件

 - page中调用   

 ```

import { createStoreBindings } from "mobx-miniprogram-bindings";
import { timStore } from "../../store/tim";


 onLoad: function (options) {
   /**  mbox调用
   * this 当前页面的this
   * store 
   * fields //使用store 中哪个state
   * actiion  //store中用哪个action
   * 
   * 配置好后 createStoreBindings 就可以把我们在store中配置的状态绑定到我们当前page对象下
   */

    this.storeBindings = createStoreBindings(this, {
      store: timStore,
      fields: ['sdkReady'],
      actions: ['login']
    })
    //当前页面的this直接可以调用store的方法了
    // this.data.sdkReady 也会有值  页面中可以使用{{sdkReady}}
    this.login()
 }

//页面销毁时候同时 销毁STORE的绑定
 onUnload: function () {
    this.storeBindings.destroyStoreBindings()
  },
  
 ```

 - js中使用同样

 ```
import { createStoreBindings } from "mobx-miniprogram-bindings";
import { timStore } from "../store/tim";

this.storeBindings = createStoreBindings(this, {
    store: timStore,
    fields: ['sdkReady'],
    actions: { timLogout: 'logout' },
})

 if (this.sdkReady) {
        this.timLogout()
 }


 ```


 - 在自定义组件中使用 用behaviors 
```
import { storeBindingsBehavior } from "mobx-miniprogram-bindings";
import { timStore } from "../../../../store/tim";

Component({
  /**
   * 组件的属性列表
   */
  behaviors:[storeBindingsBehavior],
  storeBindings:{
    store:timStore,
    fields:['messageList'],
    actions:['getMessageList','setTargetUserId']
  },
  properties: {
      
  },

  /**
   * 组件的初始数据
   */
  data: {

  },

  /**
   * 组件的方法列表
   */
  methods: {

  }
})

```




 11. 本项目为了测试聊天  需要 下载的腾讯小程序IM DEMO  在里边创建了一个用户weibsgz 输入我们当前这个项目的userId(微信授权登录后是2573) 和 2573聊天 在conversation中可以看到聊天

- model/tim.js login方法里边可以知道我们的微信登录后获取接口给我们设置的用户ID （2573）

- 在`conversation.js`中设置目标聊天人(weibsgz) 和本项目中登录的用户和weibsgz的聊天列表
`this.setTargetUserId('weibsgz')`

