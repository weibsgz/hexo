---
title: reduce 示例
date: 2020-06-27 14:13:23
tags: js
category: "js"
---


### 概念


1. 语法 `arr.reduce(callback,[initialValue])`

2. reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce 的数组。

```
callback （执行数组中每个值的函数，包含四个参数）

    1、previousValue （上一次调用回调返回的值，或者是提供的初始值（initialValue））
    2、currentValue （数组中当前被处理的元素）
    3、index （当前元素在数组中的索引）
    4、array （调用 reduce 的数组）

initialValue （作为第一次调用 callback 的第一个参数。）

```

### 示例

```
 //计算数组中每个元素出现的次数
    const names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];

    function CharacterTimes(arr) {
      return arr.reduce((prev, cur, i) => {
        if (prev.hasOwnProperty(cur)) {
          prev[cur]++;
        }
        else {
          prev[cur] = 1;
        }
        return prev
      }, {})
    }
    console.log(CharacterTimes(names))

    //将二维数组转化为一维
    let arr = [[0, 1], [2, 3], [4, 5]];
    const _arr = arr.reduce((prev, cur) => {
      return prev.concat(cur)
    }, [])

    console.log(_arr)

    //数组 对象 互相转换
    const testData = [
      { id: 1, name: 'a' },
      { id: 2, name: 'b' }
    ]

    const testData2 = {
      1: { id: '1', name: 'a' },
      2: { id: '2', name: 'b' }
    }
    //将 testData 转为 testData2 形式 （数组转对象）
    function ArrayToObj(arr) {
      return arr.reduce((prev, cur) => {
        if (cur.id) {
          prev[cur.id] = cur
        }
        return prev
      }, {})
    }
    const res = ArrayToObj(testData)
    console.log(res)

    //将 testData2 转为 testData 形式 （对象转数组）
    function ObjToArray(obj) {
      return Object.keys(obj).map(key => obj[key])
    }
    console.log(ObjToArray(testData2))

```