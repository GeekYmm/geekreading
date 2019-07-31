---
title: JavaScript中计算结果出现NaN和小程序的事件绑定组件
date: 2018-07-15 16:47:04
tags: JavaSctipt
---

最近小程序接单写了一个关于物料计算的小程序，过程中出现了两个问题。第一就是计算结果为NaN；第二个就是小程序事件绑定组件currentTarget的使用问题。

<!-- more -->

## JavaScript出现NaN问题的原因及解决办法

### 为什么？

1、操作的两个数，类型不一致。
2、有一个值为NaN，计算后为NaN。

### 怎么办？

使用转换函数：js提供了**parseInt()**和**parseFloat()**两个转换函数。前者把值转换成整数，后者把值转换成浮点数。只有对String类型调用这些方法，这两个函数才能正确运行；对其他类型返回的都是NaN(Not a Number)。

    parseInt("1234blue"); //returns 1234
    parseInt("0xA"); //returns 10
    parseInt("22.5"); //returns 22
    parseInt("blue"); //returns NaN

## 微信小程序中的事件

1.事件有分类，分为冒泡和非冒泡事件，冒泡事件在触发后会向父类组件传递；而非冒泡事件则不会。

2.事件绑定的写法同组件的属性，以 key、value 的形式。key 以 bind 或 catch 开头，然后跟上事件的类型，如bindtap、catchtouchstart。

value 是一个字符串，需要在对应的 Page 中定义同名的函数。不然当触发事件的时候会报错。

bind事件绑定不会阻止冒泡事件向上冒泡，catch事件绑定可以阻止冒泡事件向上冒泡。

3.事件对象，当组件触发事件时，逻辑层绑定该事件的处理函数会收到一个事件对象。

| 属性        |   类型    |    说明  |
| --------   | -----:  | :--------:  |
| type     | String |   事件类型     |
| timeStamp        |   Integer   |   事件生成时的时间戳   |
| target        |    Object    |  触发事件的组件的一些属性值集合  |
| currentTarget        |    Object    |  当前组件的一些属性值集合  |