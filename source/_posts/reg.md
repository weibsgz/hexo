
---
title: 正则学习
date: 2018-08-24 14:13:23
tags: js
category: "js" 
---

```
 {
            //replace 参数1：要被替换的字符(可以是正则表达式)  参数2：替换的字符
            //将下面语句中的 is 注意不包含this中的is 替换成 IS
            // \b代表单词边界 这里指单独的is 
            var str = 'He is a Boy,This is a Dog';
            var reg = /\bis\b/g
            console.log(str.replace(reg, 'IS')) //He IS a Boy,This IS a Dog

            //用函数表达式  因为字符串的转义问题，字符串的两个\\实际上是一个\。
            var reg1 = RegExp('\\bis\\b', 'g');
            console.log(str.replace(reg1, 'IS')) //He IS a Boy,This IS a Dog

            //非单词边界 比如我只想替换 this 中的 is 
            var reg2 = /\Bis\b/g
            console.log(str.replace(reg2, 'IS')); //He is a Boy,ThIS is a Dog


        }

        {
            //[]中括号代表或者 “|” 也可以
            //匹配  年份-月份-日  或者 年份/月份/日
            var reg = /^\d{4}[-/]\d{2}[-/]\d{2}$/
            var regx = /^\d{4}-|\/\d{2}[-/]\d{2}$/
            console.log(reg.test('2008-08-12'))  //true
            console.log('---------------', regx.test('2008-08-12'))  //true
            //匹配a或者b或者c忽略大小写
            var reg2 = /[abcd]/gi
            console.log('a1b2c3D4'.replace(reg2, '0')) //01020304

            //字符取反 不是a或者b或c或d的 替换
            var reg3 = /[^abcd]/gi
            console.log('a1b2c3D4'.replace(reg3, 0)) //a0b0c0D0

            //范围类 匹配大小写a-z 和 ‘-’
            var reg4 = /[a-zA-Z-]/g
            console.log('a1b2c3D4-'.replace(reg4, 0)) //010203040
        }

        {
            //匹配一个ab+数字+任意字符
            var reg = /ab\d./
            console.log(reg.test('ab9999aaa')) //true

            //贪婪模式 匹配尽量多
            var reg2 = /\d{3,5}/g
            console.log('123456789'.match(reg2)) //["12345", "6789"]

            //非贪婪模式  量词后边加问号 match返回存放匹配结果的数组。该数组的内容依赖于 regexp 是否具有全局标志 g。
            var reg3 = /\d{3,5}?/g
            console.log('123456789'.match(reg2)) //["123", "456", "789"]

            //分组 

            var reg4 = /Br(on|ca)sper/g
            console.log('BronsperBrcasper'.replace(reg4, '0')); //00

            //反向引用
            //将 2015-02-21 修改成 02/21/2015 $1...n 可以捕获分组  
            //如果不想捕获 用 ?:   列(?:XXXXX)
            var reg5 = /(\d{4})-(\d{2})-(\d{2})/g
            console.log('2015-02-21'.replace(reg5, '$2/$3/$1')); //02/21/2015

            var r = '2015-02-212015-03-21'.match(reg5);
            console.log('11111111', r); //["2015-02-21", "2015-03-21"]



        }

        {
            //断言
            //匹配英文字符，他后边必须是个数字，但是数字不在匹配范围内
            //？=\d后边是数字 ?!\d后边不是数字
            var reg = /\w(?=\d)/g
            var reg2 = /\w(?!\d)/g
            console.log('a2bbc3'.match(reg))    //["a", "c"]
            //console.log('a2bbc3a'.replace(reg2,'0')) //a200c3

            //如匹配姓zhao，但名字不叫xianlie的人
            var reg2 = /zhao(?!xianlie)/g

            console.log('zhaook,zhaoxianlie,liuxianlie'.replace(reg2, 'wei'))
            //weiok,zhaoxianlie,liuxianlie


            //split 传入正则 匹配 , 或者 | 或者 *
            console.log('a,b|c*d'.split(/,|\*|\|/g)) //["a", "b", "c", "d"]



            //replace function 参数1：匹配到的字符串 参数2：分组内容，没有则忽略 参数3：匹配项的index 参数4：源字符串
            let newStr = 'a1b2c3d4'.replace(/\d/g, function (match, index, origin) {
                return parseInt(match) + 1
            })

            console.log(newStr) //a2b3c4d5



            function getUrlParams(str) {
                //如果 regexp 没有标志 g，那么 match() 方法就只能在 stringObject 中执行一次匹配。如果没有找到任何匹配的文本， match() 将返回 null。否则，它将返回一个数组，其中存放了与它找到的匹配文本有关的信息。该数组的第 0 个元素存放的是匹配文本，而其余的元素存放的是与正则表达式的子表达式匹配的文本。除了这些常规的数组元素之外，返回的数组还含有两个对象属性。index 属性声明的是匹配文本的起始字符在 stringObject 中的位置，input 属性声明的是对 stringObject 的引用。
                
                //（^|&）意思是从头开始匹配字符&, =([^&]*)意思是匹配=后面零个或多个不是&的字符，直至碰到第一个&为止，(&|$)意思就是匹配最后一个&，在正则表达式中，增加一个()代表着匹配数组中增加一个值, 因此代码中的正则匹配后数组中应包含4个值, 在getUrlParams("name")函数中，此时 r 获取到的数组应该是 ["name=elephant&", "", "elephant", "&"]
                
                // 注意哦，，，如果正则没有 g  标志 match  返回的该数组的第 0 个元素存放的是匹配文本，而其余的元素存放的是与正则表达式的子表达式(就是括号)匹配的文本。
                
                var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i"); //定义正则表达式 
                var r = window.location.search.substr(1).match(reg);
                if (r != null) return unescape(r[2]);
                return null;

                // return r && decodeURIComponent(r[2]);
            }

            window.location = "http://www.baidu.com?name=elephant&age=25&sex=male";
            var name = getUrlParams("name"); //elephant
            var age = getUrlParams("age");   //25
            var sex = getUrlParams("sex");　//male
        }

```


```
// 保存：删除关闭按钮
var reg = /<div\s+data\-del\=\"(\w+)\"[\s\S]+?<\/div>|<span\s+data\-del=\"(\w+)\"[^>]+><\/span>/g; 
// 替换前：
// 图片：
`<div data-del="img" class="_editor_title _editor_img_title">
  <span data-del="img" class="_editor_close"></span>
</div>` 
// 视频
`<div data-del="video" class="_editor_title _editor_video_title">
  <span data-del="video" class="_editor_title_btn" data-ind="1551928713346">添加封面图</span>
  <span data-del="video" class="_editor_close"></span>
</div>`
// 替换后：
// 图片
`<!-- img del btn -->`
// 视频
`<!-- video del btn -->`

/////////////////////////////////////////////////////////////////////////

// 保存：图注
var reg = /<div[^>]*><input\s+class\=\"_editor_input\"[^>]+data\-value=\"(.*?)\"[^>]+\/>.+?<\/span>[^<>]*<\/div>/g;
// 替换前：
`<div class="_editor_label">
  <input class="_editor_input"  value="" data-value="abc"  placeholder="请输入图注1-60个汉字" autocomplete="off"/>
  <span class="_editor_stat">3/60</span>
</div>`
// 替换后：
// input 如果有值：
`<!-- del input start -->
<div class="_editor_label">
  <span>图注</span>
</div>
<!-- del input end -->`
// input 没有值：
`<!-- del input empty -->`

/////////////////////////////////////////////////////////////////////////

// 编辑：视频图片占位图反显
var reg = /<video[^>]+src=\"([^>]+?)\"[^>]*><\/video>/g;
// 替换前：
`<video class="edui-upload-video video-js" controls="" preload="none" width="0" height="0" src="gg" data-setup="{}"></video>`
// 替换后：
`<img class="edui-upload-video" _url="gg" src="./src/images/convertVideo.jpg" alt="">`

/////////////////////////////////////////////////////////////////////////

// 编辑：图注反显
var reg = /<\!\-\- del input start \-\->.*?<span>([^<>]*?)<\/span>.*?<\!\-\- del input end \-\->|<\!\-\- del input empty \-\->/g;
// 替换前：
// input 如果有值：
`<!-- del input start -->
<div class="_editor_label">
  <span>图注</span>
</div>
<!-- del input end -->`
// input 没有值：
`<!-- del input empty -->`
// 替换后：
`<div class="_editor_label">
  <input class="_editor_input"  value="" data-value="图注"  placeholder="请输入图注1-60个汉字" autocomplete="off"/>
  <span class="_editor_stat">3/60</span>
</div>`

/////////////////////////////////////////////////////////////////////////

// 编辑：关闭按钮反显
var reg = /<!--.*?(img|video).*?del btn -->/g
// 替换前：
// 图片
`<!-- img del btn -->`
// 视频
`<!-- video del btn -->`
// 替换后：
// 图片：
`<div data-del="img" class="_editor_title _editor_img_title">
  <span data-del="img" class="_editor_close"></span>
</div>` 
// 视频
`<div data-del="video" class="_editor_title _editor_video_title">
  <span data-del="video" class="_editor_title_btn" data-ind="1551928713346">添加封面图</span>
  <span data-del="video" class="_editor_close"></span>
</div>`


```

替换img标签里的style里为 width:100%;height:100% 其他属性不变
```
  var str = `<img style="width:200px;line-height:40px" src="https://www.baidu.com" data-xxx="123"/>`

    var _str = ''
    var reg = /(<img.*style\=\").*?(\".*>)/gi
    _str = str.replace(reg, '$1width:100%;height:100%$2')

    console.log(_str)//<img style="width:100%;height:100%" src="https://www.baidu.com" data-xxx="123"/>

```



获取URL上的参数
```

var url =  `https://aikahao.xcar.com.cn/video/151932.html` ;    
var reg = /video\/(\d*).html/im
var r = url.match(reg)

console.log(r) //["video/151932.html", "151932", index: 28, input: "https://aikahao.xcar.com.cn/video/151932.html", groups: undefined]

var url2 =  'https://aikahao.xcar.com.cn/video?item=333333333.html';

var reg2 = /\?(?:id|item)=(\d*)/i //?:match时候不要这个分组
var r2 = url2.match(reg2);
console.log(r2) //["?item=333333333", "333333333", index: 33, input: "https://aikahao.xcar.com.cn/video?item=333333333.html", groups: undefined]

```