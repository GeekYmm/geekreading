---
title: 关于JavaScript中的闭包
date: 2018-08-20 20:43:05
tags: JavaScript
---

本文参考了[MDN上关于闭包的介绍][1]，当然网络上有很多关于JavaScript异步，闭包的说明和文章，但是我觉得还是需要记录下来才可以加深理解。

<!-- more -->

## 认识闭包

在js中一个函数可以作为另一个函数的参数，也就是构成**高阶函数**，举个例子：
    
    function add(x, y, f) {
        return f(x) + f(y);
    }

其中的f参数是一个函数，那么add就是一个高阶函数。既然函数可以做参数，那么在高阶函数中，函数当然也可以作为另一个函数的返回值。比如下面这个例子：

    function lazy_sum(arr) {
        var sum = function () {
            return arr.reduce(function (x, y) {
                return x + y;
            });
        }
        return sum;
    }

    var f = lazy_sum([1, 2, 3, 4, 5]);// 函数sum()
    f();// 结果等于15

函数lazy_sum中定义了函数sum，并且内部函数sum可以引用外部函数lazy_sum的参数和局部变量，当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中，这就是**闭包**结构。

我理解闭包的方法是通过类比背包。当一个函数被创建并传递或从另一个函数返回时，它会携带一个背包，而这个背包包含函数声明时作用域内的所有变量。

## 闭包的作用

通常你使用只有一个方法的对象的地方，都可以使用闭包。

在 Web 中，你想要这样做的情况特别常见。大部分我们所写的 JavaScript 代码都是基于事件的 — 定义某种行为，然后将其添加到用户触发的事件之上（比如点击或者按键）。我们的代码通常作为回调：为响应事件而执行的函数。

假如，我们想在页面上添加一些可以调整字号的按钮。一种方法是以像素为单位指定 body 元素的 font-size，然后通过相对的 em 单位设置页面中其它元素（例如header）的字号：

    body {
    font-family: Helvetica, Arial, sans-serif;
    font-size: 12px;
    }

    h1 {
    font-size: 1.5em;
    }

    h2 {
    font-size: 1.2em;
    }

我们的文本尺寸调整按钮可以修改 body 元素的 font-size 属性，由于我们使用相对单位，页面中的其它元素也会相应地调整。

以下是 JavaScript：

    function makeSizer(size) {
    return function() {
        document.body.style.fontSize = size + 'px';
    };
    }

    var size12 = makeSizer(12);
    var size14 = makeSizer(14);
    var size16 = makeSizer(16);

size12，size14 和 size16 三个函数将分别把 body 文本调整为 12，14，16 像素。我们可以将它们分别添加到按钮的点击事件上。如下所示：

    document.getElementById('size-12').onclick = size12;
    document.getElementById('size-14').onclick = size14;
    document.getElementById('size-16').onclick = size16;
    <a href="#" id="size-12">12</a>
    <a href="#" id="size-14">14</a>
    <a href="#" id="size-16">16</a>

## 用闭包封装私有变量

    function create_counter(initial) {
        var x = initial || 0;
        return {
            inc: function () {
                x += 1;
                return x;
            }
        }
    }

    var c1 = create_counter();
    c1.inc(); // 1
    c1.inc(); // 2
    c1.inc(); // 3

    var c2 = create_counter(10);
    c2.inc(); // 11
    c2.inc(); // 12
    c2.inc(); // 13

在返回的对象中，实现了一个闭包，该闭包携带了局部变量x，并且，从外部代码根本无法访问到变量x。换句话说，闭包就是携带状态的函数，并且它的状态可以完全对外隐藏起来。

[1]: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures