---
title: JavaScript数组去重以及去重耗时
date: 2018-10-02 08:28:41
tags: JavaScript
---

以前不管是在工作中还是自己的项目中关于数组去重一直都是利用Set集合中重复元素会被过滤的特性来去重的。今天遇到一个没办法用Set来去重的情况，所以就想总结一下常用的几种数组去重方法并且还想了解一下这些方法的耗时，也就是看一下它们具体的去重效率如何。

<!-- more -->

这个没办法用Set来去重的情况也需要记录一下：数组里面的元素是object对象，需求是去除对象元素中某个属性相同的对象，只留下其中任意一个元素即可。

为了间接测试这些解法的性能，我用了一个测试模版，用来计算数组去重的耗时。如下：

    let arr1 = Array.from(new Array(100000), (x, index)=>{
        return index
    })//构造一个十万长度的数组

    let arr2 = Array.from(new Array(50000), (x, index)=>{
        return index+index
    })//构造一个五万长度的数组

    let arr3 = arr1.concat(arr2)//用concat连接两个数组

    let start = new Date().getTime()
    function distinct(arr) {
        // 数组去重代码
    }
    let end = new Date().getTime()
    console.log(end - start)//消耗的时间

## 双重 for 循环

最容易理解的方法，外层循环遍历元素，内层循环检查是否重复元素。当有重复值的时候，可以使用push()，也可以使用splice()摘除。

    function distinct(arr) {
        for (let i=0, len=arr.length; i<len; i++) {
            for (let j=i+1; j<len; j++) {
                if (arr[i] == arr[j]) {
                    arr.splice(j, 1);
                }
            }
        }
        return arr
    }

最终的耗时:200355ms，虽然很好理解但是性能却很差。

## Array.filter() + indexOf

这个方法的思路是，使用 ES6 中的 Array.filter() 遍历数组，并结合 indexOf 来排除重复项。

    function distinct(arr) {
        return arr.filter((item, index)=> {
            return arr.indexOf(item) === index
        })
    }

最终耗时:10173ms，相比之下还算是比较快了。

## Array.sort()

首先使用 sort() 将数组进行排序，然后比较相邻元素是否相等，从而排除重复项。

    function distinct(arr) {
        arr = arr.sort()
        let result = [arr[0]]

        for (let i=1, len=arr.length; i<len; i++) {
            arr[i] !== arr[i-1] && result.push(arr[i])
        }
        return result
    }

耗时：138ms，出乎意料的快。

## new Set()

我猜用 Set 肯定会更快，不过还是得测试一下。

    function distinct(arr) {
        return Array.from(new Set(arr))
    }

最后的耗时是：58ms，这也太快了，我没想到会这么快。

## for...of + Object

这个方法我只在一些文章里见过，实际工作中倒没怎么用。首先创建一个空对象，然后用 for 循环遍历。原理就是利用对象的属性不会重复这一特性，校验数组元素是否重复。

    function distinct(arr) {
        let result = []
        let obj = {}
        for (let i of arr) {
            if (!obj[i]) {
                result.push(i)
                obj[i] = 1
            }
        }
        return result
    }

最终耗时：13ms，快到想说脏话了，我日。