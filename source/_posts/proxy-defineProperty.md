---
title: 关于defineProperty和Proxy
date: 2019-12-03 14:13:23
tags: vue
category: "vue"  # 分类为 js
---



### 为什么Vue3.0不再使用defineProperty实现数据监听？
[链接地址](https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247493252&idx=2&sn=417d8c16d501c45d7d00bf435bb21758&chksm=f95253c7ce25dad14c83d57c6f97001dbd98b68c6b20935a6ff590ebbf449b1a9f290ff8efc4&scene=0&xtrack=1&key=ed51e25ae07a7464a6c6330731fb12c159622b6c34d05e7d9fbe763f78a8aaded76168290b60b938410c73af62c040ba2ae3e4a8be6d728de4d5096aeb80b0f0d4e1f1748aa3df84e58ce701f638d34d&ascene=1&uin=MjcxOTIxMDM2MQ%3D%3D&devicetype=Windows+10&version=62070152&lang=zh_CN&pass_ticket=2X%2FLuGI1nlKloU3SMX6OUgfxxRhi4L7kapAQtkW9QHqhl4vCKFnsJE8p2RJZe8Hm)


### 简易使用defineProperty实现双向绑定
    
```     
         <div id="app"></div>
        
        function vue() {
            this.$data = {a:1};
            this.$el = document.getElementById('app')
            this.observe(this.$data)
            this.render(); //初始页面中打印出1
        }

        vue.prototype.observe = function(data) {
            var value;
            var self = this;
            for (var key in data) {
                value = data[key]

                if(typeof value == 'object') {
                    this.observe(value)
                }
                else{
                    Object.defineProperty(this.$data,key,{
                        get(){
                            //此处省略依赖收集
                            return value;
                        },
                        set(newval) {
                            value = newval;
                            self.render() //修改this.$data.a后触发方法再次渲染到页面
                        }
                    })
                }
            }
        }

        vue.prototype.render = function() {
            this.$el.innerHTML = this.$data.a
        }

        var vm = new vue()
        setTimeout(()=>{
            vm.$data.a = 4444444
        },2000)
       
       

```

### Proxy的优势

1. 直接代理整个对象，不用for in 循环了

```
const input = document.getElementById('input');
const p = document.getElementById('p');
const obj = {};

const newObj = new Proxy(obj, {
  get: function(target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function(target, key, value, receiver) {
    console.log(target, key, value, receiver);
    if (key === 'text') {
      input.value = value;
      p.innerHTML = value;
    }
    return Reflect.set(target, key, value, receiver);
  },
});

input.addEventListener('keyup', function(e) {
  newObj.text = e.target.value;
});

```


2. Proxy可以直接监听数组的变化


