---
title: how use Git and GitHub
date: 2019-03-21 13:20:09
tags: git github
categories: git
---
# 如何简单地使用git

本篇文章主要介绍一些git的核心使用方法，不涉及太多命令

* * *

## 下载git和GitHub的注册

**git**的下载可以直接访问官网<https://git-scm.com/downloads>
官网上也有简洁具体的git安装方法

**GitHub**注册方式也很简单，直接到官网sign up即可<https://github.com/>
关于github如何建仓库、建立分支、pull request、merge等的简单说明可以看官方文档<https://guides.github.com/activities/hello-world/>

* * *

## 本地仓库建立和简单的使用

先创建一个文件夹作为GitHub在你本地的工作文件夹

然后右键选择 Git Bash Here (一般装好git后会有这个命令)

接着输入两个命令来作为git的基础配置

```bash
git config --global user.name "your name"
git config --global user.email "your email"
```

然后接着执行：

```bash
git init //初始化git在这个文件夹（在内部会生成.git文件夹）

git add . //把工作时的所有变化提交到暂存区,不包括被删除的文件

git commit -m "first commit" //first commit是自己的注释，会在仓库显示

git remote add origin https://github.com/elenathFGS/elenathfgs.github.com.git
//与远程仓库关联（这里的origin是主机的名字 后面网址是你仓库地址（可以点clone得到））

git push -u origin master
//推送到远程的主机，-u命令表示选定一个默认的主机，以后再推送到同样主机直接 git push即可
```

> push失败时可以尝试先pull rebase一下  git pull --rebase origin master

* * *

## 如何进行回退

当执行了一个错误的commit的时候，我们希望能够回到之前本地commit的版本，此时可以采取以下操作

1. 先查看日志内容

```bash
$ git log
```

2. 然后复制你所需要回到的commit版本的id（即commit 后面的字符）

```bash
commit 5e88d154e63347a2abd699bab0ee15ea**
Author: ****
Date:   **

commit2

commit b7d2fba3b06461bce3b0e977e6ac8e6***
Author: ***
Date:  ***

commit1

commit 3522ce1fca9082cf1eb273083e2e599c94**
Author: **
Date:   **
```

3. 接着执行本地回退

```
$ git reset --hard commit_id(commit字符)
```

4. 将远程库强制同步

```
$ git push origin HEAD --force
```

> force是强制push到远程端的命令s


