---
title: 用vue和Bootstrap写一个壁纸网站
date: 2018-07-07 17:06:46
tags: vue
---

## 壁纸网站
 
最近在学习vue.js这个前端框架，就想做个练手的小项目，所以有了这个壁纸网站。前端使用了vue.js和Bootstrap框架，后台使用的第三方的比目Saas云服务，后面计划使用node.js重写后台。

<!--more-->

## 功能
    
功能上，一个壁纸网站的首页当然是做一个壁纸列表对其进行展示；然后可以点击下载和查看详情。壁纸的来源全都是从各种摄影网站上拿到的，没有版权且遵循CC0协议。

还有就是登陆和注册功能，方便用户反馈意见。

除了首页的展示之外，还会精选出一些壁纸（目前还是人工主观的精选）。


支持壁纸投稿，如果你想把自己的摄影作品展示给大家欢迎投稿，需要后台审核，并且保证授权给本站。

## 技术上遇到的问题

1. HTML <label> 标签的 for 属性

   for 属性规定 label 与哪个表单元素绑定。

2. 关于 form 表单的内容，如何传值到 js

   我是直接使用了 vue 的 v-model 绑定表单元素，然后按钮使用了 v-on：click

3. vue 中如何为 img 添加 src 变量

   `<img v-bind:src="src">`
或者简写
  `<img :src='src'>`

4. vue 里报错：[Vue warn]: Cannot find element: #app

   js 代码需要放在 body 的最后

## 项目地址

[线上地址](https://geekymm.github.io/Wallpaper-web/)

[壁纸网站GitHub地址](https://github.com/GeekYmm/Wallpaper-web)
