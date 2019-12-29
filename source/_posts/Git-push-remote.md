---
title: Git上传本地项目到远程仓库
date: 2019-02-19 17:07:46
tags:
   - Git
categories:
   - Git
---

### 初始化本地仓库
- 初始化目录
```bash
# 初始化目录
git init
# 查看你当前目录下文件状态
git status
# git add把项目添加到仓库
git add .
# git commit 提交文件到本地仓库
git commit -m "提交备注"
```

<!-- more -->

### 创建SSH KEY 链接远程仓库

- 本地生成ssh key
```bash
ssh-keygen -t rsa -C "youremail@example.com"
```
当前用户.ssh目录里找到id_rsa和id_rsa.pub这两个文件，将id_rsa.pub文件内容添加到gitHub的Settings下SSH KEY


### 在远程仓库创建一个用来存储代码的仓库
- 本地关联远程仓库
```bash
git remote add origin https://github.com/aikaiqiang/myLittleApp.git
```
- 本地仓库内容推到远程仓库
```bash
# 由于新建的远程仓库是空的，所以要加上-u这个参数，等远程仓库里面有了内容之后；
git push -u origin master
# 下次再从本地库上传内容的时候只需下面命令：
git push origin master
```

如果push失败，==可能是远程仓库有文件readme.md==，建议先合并远程仓库内容到本地再push
```bash
git pull --rebase origin master
```