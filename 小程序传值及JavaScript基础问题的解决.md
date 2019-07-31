---
title: 小程序传值及JavaScript基础问题的解决
date: 2018-03-22 14:20:05
tags: 微信小程序
---
最近又在做小程序中遇到了一些问题，都是非常基础的问题，很明显的感觉到JavaScript基础掌握的不牢靠，所以接下来给自己的小计划就是重新看一遍[廖雪峰老师的JavaScript教程][1]，把很多没在意的点再次了解一下。

这段时间遇到的几个问题包括：1.小程序不同页面的传值。2.忽略了JSON需要反序列化才可以变成js对象使用。3.关于箭头函数的使用。对于三个问题我的解决方法是这样的：

<!--more-->

----------


1.小程序不同页面的传值有三种办法，包括：本地数据缓存；定义成全局的app对象；使用URL传值。
------------------------------------------------

**本地数据缓存：**

wx.setStorage(OBJECT)将数据存储在本地缓存中指定的key中，会覆盖掉原来该key对应的内容，这是一个异步接口。
    
    wx.setStorage({ 
                    key:"key",
                    data:"value"
                 })

既然有缓存也就有释放，使用wx.getStorage(OBJECT)从本地缓存中异步获取指定 key 对应的内容。

    wx.getStorage({
                    key: 'key',   
                    success: function(res) {
                    console.log(res.data)  
                    }  
                 })

**定义全局的app对象：**

在A页面定义一个app的全局对象

    var app = getApp();
    app.dataA = "Hello Geek";
    
然后在B页面引用这个全局的对象

    var app = getApp();
    var dataB = app.dataA;//dataB的值现在就是 “Hello Geek”

**使用URL传值：**

在A页面使用wx.navigateTo(OBJECT)可以跳转到B页面。这个API可以保留当前页面，然后跳转到应用内的某个页面。在A页面：

    wx.navigateTo({
      url: 'test?id=1'
    })
    
在B页面也就是URL对应的那一页的onLoad生命周期函数中拿到这个值：

    //test.js
    Page({
      onLoad: function(option){
        console.log(option.query)
      }
    })


----------

2.关于JSON序列化和反序列化的使用。
--------------------

把任何JavaScript对象变成JSON，就是把这个对象序列化成一个JSON格式的字符串，这样才能够通过网络传递给其他计算机。**使用JSON.stringify()**

    var xiaoming = {
        name: '小明',
        age: 14,
        gender: true,
        height: 1.65,
        grade: null,
        'middle-school': '\"W3C\" Middle School',
        skills: ['JavaScript', 'Java', 'Python', 'Lisp']
    };
    var s = JSON.stringify(xiaoming);//把小明这个对象序列化成一个JSON
    console.log(s);

如果我们收到一个JSON格式的字符串，只需要把它反序列化成一个JavaScript对象，就可以在JavaScript中直接使用这个对象了。**使用JSON.parse()**

    JSON.parse('[1,2,3,true]'); // [1, 2, 3, true]
    JSON.parse('{"name":"小明","age":14}'); // Object {name: '小明', age: 14}
    JSON.parse('true'); // true
    JSON.parse('123.45'); // 123.45

----------

3.关于JavaScript中箭头函数的使用。
--------------------

ES6标准新增了一种新的函数：Arrow Function（箭头函数）。为什么叫Arrow Function？因为它的定义用的就是一个箭头：

    x => x * x

上面的箭头函数相当于：

    function (x) {
        return x * x;
    }

----------

最近在考虑要不要把我的博客上的内容搬到公众号里，很犹豫。

  [1]: https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000