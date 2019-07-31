---
title: 基于 Hexo + Github 搭建个人博客
date: 2017-12-29 09:40:01
tags: Geek
---


很早就想搭建一个属于自己的个人博客了，可以记录一些学习上掉过的坑和一些生活琐事。由于懒和担心搭建博客很麻烦所以现在才做。

如果你也是我这种屈服于怕麻烦基因的人，又想搭一个像你现在看到的GeekReading一样的博客的话，那么下面的教程很适合你了。

---
**面向的对象**

1.前端小白
    
2.windows用户

<!--more-->
---
**前期准备**
    
1.一个 Github 账户
    
2.在本地安装 Git
    
3.安装 Node.js

---
**创建一个 Github page**

![](https://aloneimg.com/images/2017/12/29/blogd42ec.jpg)
    
1.Repo name 的填写格式为 Github 名 + github.io 。我的 Repository name 为 GeekYmm.github.io ，你需要设置自己的
    
2.Description 可以写 static blog/blog ，其实你随意就好
    
3.点击绿色按钮创建 Repository
    
---
**Hexo 的安装和配置**

1.这一步的前提是你已经装好了 Node.js 和 Git 。打开 Git bash 命令行
    
2.安装 Hexo `$ cnpm install -g hexo-cli`
    
3.创建 blog 根目录 `$ hexo init blog`
    
4.生成静态文件 `$ hexo g`

5.安装 Hexo-server 模块 `$ cnpm install hexo-server --save`

6.运行本地服务器 （一定要在 blog 根目录下运行）`$ hexo s`

7.打开 http://localhost:4000/ 已经可以看到一篇内置的 blog 了
    
---
**安装 Hexo 主题**

1.在本地 Git 安装 Hexo 主题，以 Next 主题为例

`$ hexo clean`
`$ git clone https://github.com/iissnan/hexo-theme-next`

2.把配置文件 _config.yml 中的 theme 修改为 next

3.更新主题
    
`$ hexo c`
`$ hexo g`
`$ hexo s`

---
**添加 SSH key 到 Github**

1.检查 SSH keys 是否存在. Git 执行如下命令，检查 SSH keys 是否存在

`$ ls -al ~/.ssh`

如果有文件 id_rsa.pub 或 id_dsa.pub ，则直接进入步骤3,将 SSH key 添加到 Github 中，否则进入下一步生成 SSH key

2.生成新的 SSH key 。执行如下命令生成 public/private rsa key pair ，注意将 your_email@example.com 换成你自己注册 Github 的邮箱地址

`$ ssh-keygen -t rsa -C "your_email@example.com"`

默认会在相应路径下 (~/.ssh/id_rsa.pub) 生成 id_rsa 和 id_rsa.pub 两个文件

3.将 SSH key 添加到 Github 中。前往文件夹 ~/.ssh/id_rsa.pub 打开 id_rsa.pub 文件，里面的信息即为 SSH key ，将这些信息复制到 Github 的 Add SSH key 页面即可

      进入 Github --> Settings --> SSH keys --> add SSH key:

Title里任意添一个标题，将复制的内容粘贴到Key里，点击下方Add key绿色按钮即可
      
4.最后在 Git 命令行输入执行下面命令就OK了

`$ hexo clean  //清除缓存文件 (db.json) 和已生成的静态文件 (public)`
`$ hexo g //生成缓存和静态文件`
`$ hexo d //重新部署到服务器`

如果需要新建一篇博客可以使用 `$ hexo new post '博客标题'` 来操作