---
title: 关于js弹出新窗口被浏览器拦截的问题和解决办法
date: 2018-07-22 11:29:26
tags: JavaScript
---

最近写壁纸网站的过程中，需要用到window.open()来直接打开新窗口，但是发现会被（包括 Chrome / Firefox / IE 10+ / Safari）在内的主流浏览器拦截。所以猜想原因应该是主流浏览器把新窗口认为是弹出广告等用户不想得到的窗体。

于是稍微研究了一下浏览器的弹窗拦截策略，发现如果是用户即时点击而导致的弹窗，浏览器不会进行拦截，就像是a标签的行为那样，浏览器会认为这是用户主动打开的窗口，因此不会将这个弹窗视为恶意弹窗进行拦截。

<!-- more -->

想到的第一个解决办法就是用jQuery来解决这个问题。直接使用jQuery模拟超链接的a标签被点击，当按下一个按钮时，想打开一个新的标签页，可以模拟链接被按下，然后打开链接。

但是在jQuery中，使用a.click(), a.trigger('click')等都不会引起链接默认事件被执行，这个和a标签的点击事件有关。我们每次点击a标签的链接，触发的是a标签包含的文字的原始点击事件而进行的跳转。

示例代码如下所示：

    $('#alert-success').append('<a href="http://www.geekreading.cn" target="_blank"><span id="test">test</span></a>');

    $('#alert-success a #test').click();//这里点击的是a标签包含的文字
    
    $('#alert-success a').remove();//删去a标签