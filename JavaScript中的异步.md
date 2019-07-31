---
title: JavaScript中的异步
date: 2018-09-15 16:56:29
tags: JavaScript
---

上次写了关于js中的闭包，这次来总结关于异步的问题。因为JavaScript的执行环境是单线程的，所以在使用过程中会出现“假死”的现象。在Javascript语言中，任务的执行模式分成两种：**同步（Synchronous）**和**异步（Asynchronous）**。

<!-- more -->

>* 异步：先干一件事，中间去干其他的事，最终在回来干这件事上来。或者说，每一个任务有一个或多个**回调函数（callback）**，前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务则是不等前一个任务结束就执行，所以程序的执行顺序与任务的排列顺序是不一致的、异步的。
>* 同步：就是连续执行，程序的执行顺序与任务的排列顺序是一致的、同步的。

异步的发展流程：callback -> promise -> generator + co -> async+await(语法糖)。最终结果就是，异步像同步一样，简单优雅易懂。

## 回调函数

普通的读到2个文件之后的操作，使用回调函数来异步执行：

    // 本地写3文件，index.js写以下代码 template.txt写些html的代码，data.txt写些json数据，然后命令行运行 node index.js

    let fs = require('fs')
    fs.readFile('template.txt','utf8',function(err,template){ // error-first
        fs.readFile('data.txt','utf8',function(err,data){ // error-first
            console.log({template:template,data:data});
        });
    });

## Promise

    let fs = require('fs')
    function readFilePro(filename){
        return new Promise((resolve,reject)=>{
            fs.readFile(filename,'utf8',function(err,data){err?reject(err):resolve(data)});
        })
    }

    Promise.all([readFilePro('template.txt'),readFilePro('data.txt')]).then(res=>{
        console.log({template:res[0],data:res[1]})
    })

## 生成器 generator

生成器函数虽然是一个函数，但和普通函数不一样，普通函数一旦调用就会执行完。

>* 生成器函数用* 来标识；
>* 调用的结果是一个迭代器，迭代器有一个next方法；
>* 遇到暂停点yield就停下来，直到执行迭代器的next，最后才能返回这个函数的return；
>* yield后面跟着的是value的值；
>* yield等号前面的是我们当前调用next传进来的值；
>* 第一次next传值是无效的；
>* 当done为true的时候就是value就是生成器return的值。

    // 生成器函数有个特点需要加个*
    function *go(a){
        console.log(1)
        // 此处b是外界输入，这行代码实现输入输出
        let b = yield a
        console.log(2)
        let c = yield b
        console.log(3)
        return 'o'
    }
    // 生成器函数和普通函数不一样调用他函数不会立刻执行
    // 返回生成器的迭代器，迭代器是一个对象
    let it = go('a')
    // next第一次执行不需要传参数,想想也是，没有意义
    let r1 = it.next()
    console.log(r1) // {value:'a',done:false}
    let r2 = it.next('B')
    console.log(r2) // {value:'B',done:false}
    let r3 = it.next('C')
    // 当done为true的时候就是return的值
    console.log(r3) // {value:'o',done:true}

## async 和 await

async和await是promise和generator的语法糖。其实下面的go函数就是gen函数里面的yield，现在变成了await。

    // 本地写3文件，index.js写以下代码 template.txt写些html的代码，data.txt写些json数据，然后命令行运行 node index.js
    let fs = require('fs')
    // readFilePro也可以用bluebird生成
    function readFilePro(filename){
        return new Promise((resolve,reject)=>{
            fs.readFile(filename,'utf8',function(err,data){err?reject(err):resolve(data)});
        })
    }
    async function go(){
        let template = await readFilePro('template.txt')
        let data = await readFilePro('data.txt')
        // 这里的return必须用then才能拿到值，因为是语法糖啊~
        return {template,data}
    }

    go().then(res=>console.log(res))