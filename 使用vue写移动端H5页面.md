---
title: 使用vue写移动端H5页面
date: 2019-06-01 22:06:53
tags: vue
---

很久没有更新GeekReading了，二月底来了北京之后很忙，基本上都是工作上的事情。陆陆续续有很多想法但是都没有一个很整块的时间去做去实现想法。那天在微博上看到方糖说自己有一个一闪而过的念头就会赶紧去做，因为他明白很可能只有三分钟热度。我似乎没办法拥有和他一样的执行力。

<!-- more -->

现在的工作大部分时候都在写小程序，有些项目需要写一个活动或者嵌入在APP里的移动端页面。以前在学校写页面都是小打小闹，现在可以在生产环境下来写就需要更规范。这次具体是做一个兑换奖品的H5页面，很简单。

## 开始之前的技术选择

选择了vue来写这个页面，其实用jQuery来写也可以。我是出于自己的技能掌握的考虑选择了vue。具体的方案是：vue/vuex/vue-router/axios。

之前用vue都是写pc端的页面，这次需要考虑在移动端布局的问题。选择使用lib-flexible来适配移动端。

## 如何使用lib-flexible

- 首先安装 `npm install lib-flexible --save` ;

- 在 `main.js` 中引入 `import 'lib-flexible/flexible.js'` ;

- 需要去掉 `public/index.html` 中的 `<meta name="viewport" ...>` 标签;

- 还需要在 `vscode` 里安装一个 `px2rem` 插件，或者用npm安装 `px2rem-loader` 也可以;

- 这次我选择使用 `vscode` 插件，需要在设置中配置 `1rem` 等于多少 `px` ;

## 关于axios请求跨域问题

由于使用了 `vue-cli3` 的脚手架，没有找到 `vue.config.json` ,所以需要手动新建一个;然后在文件里按文档配置 `axios` ，具体如下：

    devServer: {
        open: process.env.NODE_ENV==='development',
        host: '192.168.0.123',
        port: 8080,
        https: false,
        hotOnly: false,
        proxy: {
            '/api': {
                target: 'https://test.com',
                secure: false
            }
        }
    }

当然这也只是前端可以做的，真正克服跨域还是需要后端来放开所有请求。

## 使用节流方法

    /**
    * 节流方法
    */
    export function throttle(fn, gapTime) {
    if (gapTime == null || gapTime == undefined) {
        gapTime = 1500
    }
    let _lastTime = null
    // 返回新的函数
    return function() {
        let _nowTime = +new Date()
        if (_nowTime - _lastTime > gapTime || !_lastTime) {
        fn.apply(this, arguments) //将this和参数传给原函数
        _lastTime = _nowTime
        }
    }
    }
