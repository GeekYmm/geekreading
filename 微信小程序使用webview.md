---
title: 微信小程序使用webview
date: 2019-07-29 21:55:09
tags: 微信小程序
---

最近公司项目需要在已有的小程序中加入一个限时活动，之前我们是直接使用小程序页面来构建，但是这次就尝试把活动做成网页然后通过小程序的webview组件引入。这样做的好处在于活动相对独立，方便上线下线，再加上原生页面拥有更好的性能，适合做动画效果。

<!-- more -->

## 需要解决的问题

- 活动中需要用到用户的基础信息怎样获取
- 关于小程序页面和网页的通讯问题
- 网页如何做到使用小程序分享来邀请好友

## 用户数据获取

在小程序中，用户基础信息需要用户主动授权之后再通过GetUserInfo方法才可以拿到，但是我们的活动进入之前没办法确定用户是否已经授权信息，所以需要在网页内来进行授权。也就是需要做独立的一套用户信息获取，最终是利用微信服务号授权来实现。

## 网页传递信息给小程序

通过[webview文档](https://developers.weixin.qq.com/miniprogram/dev/component/web-view.html)可以了解到，首先需要引入[JSSDK](https://res.wx.qq.com/open/js/jweixin-1.3.2.js)，然后在网页内使用**wx.miniProgram.postMessage**方法向小程序发送消息，小程序可以在webview组件上绑定**bindmessage**方法接收到网页发来到信息。

但是需要注意的是网页向小程序**postMessage**时，会在特定时机（小程序后退、组件销毁、分享）触发并收到消息。最终小程序内收到**e.detail = { data }**，data就是多次**postMessage**的参数组成的数组。

## 小程序给网页传递信息

小程序给网页传值，我们利用webview中src携带QueryString的方式实现。

## 小程序分享邀请好友

发出邀请的用户的userid通过：网页postMessage-->小程序bindmessage接收-->放进小程序分享卡片的转发路径

被邀请的用户接收userid的过程：小程序onLoad中通过options拿到-->拼接在webview的src里-->网页内解析src取出邀请者userid

    /**
     * 在小程序内通过bindmessage获取网页传过来的数据
     */
    getMsg(e) {
        console.log('userid', e.detail.data[0].userid);
        this.userid = e.detail.data[0].userid;
    }

    /**
     * 在分享链接中传入userid
     */
    onShareAppMessage: function(e) {
        return {
            path: `/index/index?userid=${this.userid}`
        };
    }

    /**
     * 在onLoad中获取userid再拼接在src里
     */
    onLoad: function(options) {
        this,userid = null;
        if (options.userid) {
            this.userid = options.userid;
        }
        let src = `https://wxgame-test.xrunda.com/open-award/index?userid=${this.userid}`;
        this.setData({
            src: src
        })
    }
    /**
     * index.wxml的代码
     */
    <web-view src='{{src}}' bindmessage='getMsg'></web-view>
