---
layout: post
title: error: src refspec master does not match any. 错误处理办法
description: git管理工具
category: blog
---
本地仓库使用如下命令初始化：
```git
$ git init
```
之后使用如下命令添加远程库：
```git
$ git remote add origin git@github.com:hahah/ftpmanage.git
```
然后使用
```git
$ git push -u origin master
```
出现如下错误：
```git
error: src refspec master does not match any.
error: failed to push some refs to 'git@github.com:hahaha/ftpmanage.git'
```
原因：<br>
本地仓库为空<br>
解决方法：使用如下命令 添加文件；
```git
$ git add add.php addok.php conn.php del.php edit.php editok.php ftpsql.sql index.php

$ git commit -m "init files"
```
之后在push过程中出现如下错误：
```git
$ git push -u origin master
Warning: Permanently added the RSA host key for IP address 'xx.xx.xxx.xxx' to the list of known hosts.
To git@github.com:hahaha/ftpmanage.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:hahahah/ftpmanage.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
```
提示使用 git pull 之后在 push<br>
使用如下命令解决：<br>
```git
$ git pull --rebase origin master
warning: no common commits
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:hahah/ftpmanage
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: init files
```
继续push，成功。
```git
$ git push -u origin master
Counting objects: 10, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (10/10), done.
Writing objects: 100% (10/10), 5.15 KiB | 0 bytes/s, done.
Total 10 (delta 3), reused 0 (delta 0)
To git@github.com:hahaha/ftpmanage.git
   a2b5c30..1044f15  master -> master
Branch master set up to track remote branch master from origin.
```
大功告成!!!
<br><br><br>
抄自：https://www.cnblogs.com/jeremylee/p/5715289.html
