---
title: 上传更新本地代码到GitHub
date: 2018-05-01 22:46:01
tags: Geek
---
# **上传本地代码到GitHub**

第一步：在GitHub上创建一个新的Repository，记住新建的仓库的https地址

第二步：

    echo "# Test" >> README.md
    git init //新建本地Git仓库
    git add . //上传全部文件
    git add README.md
    git commit -m "注释语句" //提交到GitHub的仓库
    git remote add origin https://github.com/GeekYmm/Test.git //后面换成新仓库的地址
    git pull origin master
    git push -u origin master

注意：中间可能会让你输入Username和Password，你只要输入GitHub的账号和密码就行了。执行完后，如果没有异常，等待执行完就上传成功了。

<!--more-->

# **更新本地代码到GitHub**

第一步：查看当前的git仓库状态
    
    git status

第二步：更新全部

    
    git add -A  提交所有变化

    git add -u  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
    
    git add .  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件

第三步：接着输入更新说明

    git commit -m "更新说明"

第四步：先git pull,拉取当前分支最新代码

    git pull

第五步：push到远程master分支上

    git push origin master