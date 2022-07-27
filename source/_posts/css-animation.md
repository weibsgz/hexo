---
title: css动画小栗子
date: 2022-07-27 14:13:23
tags: css
category: "css"
---


### requestAnimationFrame 和 定时器执行动画比较
- requestAnimationFrame 会把每一帧中的所有 DOM 操作集中起来，在一次重绘或回流中就完成，使动画和浏览器刷新频率保持一致 保持动画流畅
- 当页面处理未激活的状态下，requestAnimationFrame将不进行重绘回流 节省CPU GPU
- settimeout 任务被放入异步队列，只有当主线程任务执行完后才会执行队列中的任务，因此实际执行时间总是比设定时间要晚；
- settimeout 的固定时间间隔不一定与屏幕刷新间隔时间相同，会引起丢帧。



```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .moveDiv,.moveDiv1{
            position: relative;
            width: 50px;
            height: 50px;
            background-color:red;
        }
        .moveDiv1 {
            background-color:blue;
        }
    </style>
</head>
<body>
    <!-- 定时器和 requestAnimationFrame -->    
    <button id="button1">setTimeout</button>
    <button id="button2">requestAnimationFrame</button>
    <div class="moveDiv"></div>
    <div class="moveDiv1"></div>


    <script>

        // // requestAnimationFrame 和 定时器执行动画比较
        // // requestAnimationFrame 会把每一帧中的所有 DOM 操作集中起来，在一次重绘或回流中就完成，使动画和浏览器刷新频率保持一致 保持动画流畅
        // //当页面处理未激活的状态下，requestAnimationFrame将不进行重绘回流 节省CPU GPU

        // settimeout 任务被放入异步队列，只有当主线程任务执行完后才会执行队列中的任务，因此实际执行时间总是比设定时间要晚；
        // settimeout 的固定时间间隔不一定与屏幕刷新间隔时间相同，会引起丢帧。

        var btn1 = document.querySelector("#button1")
        var btn2 = document.querySelector("#button2")
        var moveObj = document.querySelector(".moveDiv")
        var moveObj1 = document.querySelector(".moveDiv1")
        var timer = null;
        var timer1 = null;

        //setInterval执行动画
        btn1.onclick = function() {
            var count = 0;
            if(timer)  {
                // clearInterval(timer)
                return false
            }
            timer = setInterval(function(){
                go(count)
                count++;
                if(count > 1000 ) clearInterval(timer)
            },10)            
        }
        
        function go(count) {
            moveObj.style.transform = 'translatex(' + count + 'px)'
        }



        //  requestAnimationFrame 执行动画        
        var count1 = 0;
        btn2.onclick = function() {      
            window.cancelAnimationFrame(timer1)      
            timer1 = window.requestAnimationFrame(go1)       
        }

        function go1() {
            
            if(count1<1000) {
                moveObj1.style.transform = 'translatex(' + count1 + 'px)'
                count1++;
                timer1 = window.requestAnimationFrame(go1) 
            }
        }
       
    </script>
</body>
</html>


```


### animation 小动画

- `animation-play-state` 属性指定动画是否正在运行或已暂停。可以在CSS和JS中设置
- webkitAnimationStart-动画开始，webkitAnimationEnd-动画结束，webkitAnimationIteration-动画重复播放 ，即可以很方便的监听动画过程
```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>       

        .warp {
            position: absolute;
            width: 100px;
            height: 300px;
            border: 1px solid #F60093;
            overflow: hidden;
        }

        .progress-in {
            position: relative;
            width: 100%;
            height: 100%;
            background: red;
            opacity: 1;
            transform: translateY(100%);
            animation: 3s linear 0s progress forwards;
            /* 动画初始状态 暂停 */
            animation-play-state:paused;   
        }

        @keyframes progress {
            from {
                transform: translateY(100%);
            }
            to {
                transform: translateY(0);
            }
        }

        #start {
            position: absolute;
            width: 100px;
            height: 30px;
            left: 200px;
        }
    </style>
</head>
<body>  
  
     <div class="warp">
        <div class="progress-in">

        </div>
    </div>
    <button id="start">开始</button>    




    <script>

        /************** 动画  S *******************/

        const progressIn = document.querySelector(".progress-in");
        start.onclick = function () {                
            progressIn.style.animationPlayState = progressIn.style.animationPlayState == `running` ? 'paused' : 'running' 
        }
        //animation动画结束时监听  同理， transiation 的结束状态是 transitionend
        progressIn.addEventListener("webkitAnimationEnd", (ele) => {
            console.log(`过渡动画完成：过渡属性${ele}`);
        }, true);

        /************** 动画  E *******************/

    </script>
</body>
</html>

```