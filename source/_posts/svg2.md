---
layout: post
title: SVG实战
date: 2021-01-22 01:08:00 +0800
categories: 可视化
tag: 可视化
---

效果链接 https://github.com/weibsgz/svg-

# SVG 实现 LOADING

    <div class="container">
        <svg width="200" height="200" viewBox ="0 0 50 50">
            <!--
                要做一个只显示上下两截的圆 圆的周长是 2 * pi * r
                所以stroke-dasharray要用周长平分4等分（dasharray：实 - 虚 - 实。。）
                (2*3.1415926*22 )/ 4 = 34
                stroke-linecap = "round"   圆角
            -->
            <circle
                cx = "25"
                cy = "25"
                r="22"
                fill="none"
                stroke="#3bedcb"
                stroke-width="3"
                stroke-dasharray = "34"
                stroke-linecap = "round"
            >
             <!--
                from 0代表0度  25 25 代表旋转中心点
             -->
            <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                from="0 25 25"
                to="360 25 25"
                dur="2s"
                repeatCount="indefinite"
            >
             </animateTransform>
             <!-- 外圆变色 -->
             <animate
                attributeName="stroke"
                values="#3be6cb; #82bcfe; #3be6cb"
                dur="2s"
                repeatCount="indefinite"
             >

          </animate>
            </circle>

            <circle
                cx = "25"
                cy = "25"
                r="12"
                fill="none"
                stroke="#02bcfe"
                stroke-width="3"
                stroke-dasharray = "19"
                stroke-linecap = "round"
            >
             <!--
                逆时针旋转 调换下FRORM 和 TO
                from to 可以简写为values= "360 25 25; 0 25 25"
             -->
            <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                from = "360 25 25"
                to = "0 25 25"
                dur="2s"
                repeatCount="indefinite"
            >
             </animateTransform>
              <!-- 内圆变色 -->
              <animate
              attributeName="stroke"
              values="#82bcfe;#3be6cb;  #82bcfe;"
              dur="2s"
              repeatCount="indefinite"
           >

            </circle>
        </svg>
    </div>

# 流动边框

    1. 需要画2个path 正方的框,将第二个线框设置为MASK
    2. 设置蒙版里边画一个圆 设置为白色 这样蒙版圆的这个区域就显示出线段二了
    3. 将白色改为渐变色 白色透明到白色不透明
    <style>
        body{
            background-color: #000;

        }
        .container{
            position: relative;
        }
        #svg{
            position: absolute;
            left: 0;right: 0;top: 0;bottom: 0;
            margin: auto;
        }
        #content{
            box-sizing: border-box;
            padding: 20px;
            width: 400px;
            height: 400px;
            position: absolute;
            left: 0;right: 0;top: 0;bottom: 0;
            margin: auto;
            z-index: 1;
        }
    </style>
    <div class="container">
        <svg id="svg" width="400" height="400" viewBox="0 0 400 400">
            <defs>
                <path
                id="fly-box-path"
                d="M5 5 L395 5 L395 395 L5 395 Z"
                fill="none"
                ></path>
                <mask id="fly-box-mask">
                    <circle cx="0" cy="0" r="150"  fill="url(#grad2)">
                        <animateMotion
                            path="M5 5 L395 5 L395 395 L5 395 Z"
                            dur="3s"
                            repeatCount="indefinite"
                            rotate="auto"
                        >
                        </animateMotion>
                    </circle>
                </mask>

                <!-- 设置渐变  fx="100%" 流行头部白光-->
                <radialGradient id="grad2" fx="100%">
                    <stop offset="0%" style="stop-color:#fff;
                    stop-opacity:1" />
                    <stop offset="100%" style="stop-color:#fff;stop-opacity:0" />
                </radialGradient>
            </defs>
            <!-- 第一个线段 -->
            <use
                href="#fly-box-path"
                stroke-width="1"
                stroke="#235fa7">
            </use>
            <!-- 第二个线段 -->
            <use
                href="#fly-box-path"
                stroke-width="3"
                stroke="#4fd2dd"
                mask="url(#fly-box-mask)"
            >
            </use>


        </svg>
        <div id="content" style="color: #fff;">
            <slot>123</slot>
        </div>
    </div>
