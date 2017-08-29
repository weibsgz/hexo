---
title: Echarts图表数据更新方式
date: 2017-08-29 14:13:23
tags: 工具
category: "工具"  # 分类为 工具
---




[原文摘自陈胜博客](https://halfmoonvic.github.io/2017/08/29/Echarts图表数据更新方式/)


问题表现

页面 echarts 图表 在做一些数据交互的时候会涉及到一些数据的更新。如果只是简单的新数据传递给echarts图表，许多时候我们会发现 旧有的时候依然残留在页面之上
```
$(function() {
    var echartOpt = {
        fz: '15',
        color: '#fff'
    };

    var demo = echarts.init(document.getElementById('trade-history'));

    function initDemo(data, obj) {
        var demoOption = {
            tooltip: {},
            legend: {
                data: [],
            },
            calculable: true,
            xAxis: {
                data: ['2007', "2008", "2009", "2010", "2011"],
            },
            yAxis: [{
                type: 'value',
                name: '贸易量（万吨）',
                min: 0,
            }],
            color: ["#3fa7dc", "#ffea38", "#f9852d", "#F10F0F", "#14EE10"],
        };

        var seriesStyle = [{
            barMaxWidth: 10,
            itemStyle: {
                normal: {
                    // borderColor: '#3fa7dc',
                    color: '#3fa7dc'
                }
            },
        }, {
            barMaxWidth: 10,
            itemStyle: {
                normal: {
                    // borderColor: '#ffea38',
                    color: '#ffea38'

                }
            },
        }]

        // 新的图例数据
        var legend = data.map(function (item, index) {
            return item.name
        })
        var newOption = {
            legend: {
                data: legend
            },
            series: data    // 传入的数据
        }

        // 将数据样式 加载到我们的 新数据上面
        newOption.series.forEach(function(item, index) {
            $.extend(true, item, seriesStyle[index])        
        });

        // 新旧数据合并
        $.extend(true, demoOption, newOption)

        demo.setOption(demoOption, true);
    }

    // 假数据 1
    var dataOne = [{
            name: 'A',
            type: 'bar',
            data: [200, 300, 100, 330, 350],
        }]
    // 假数据 2
    var dataTwo = [{
        name: 'A',
        type: 'bar',
        data: [100, 300, 100, 330, 350],
    }, {
        name: 'B',
        type: 'bar',
        data: [200, 100, 400, 300, 600],
    }]

    // 页面初始化
    initDemo(dataOne)

    var toggleBtn = document.querySelector('.toggle')

    // 无用的循环数据使用
    var n = 0
    toggleBtn.addEventListener('click', function () {
        n = (n + 1) % 2
        var data = n === 0 ? dataOne : dataTwo
        initDemo(data)
    })


})
```









