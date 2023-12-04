---
title: git笔记
tags: [笔记]
date: 2019-12-03 15:39:25 
updated: 2020-01-22 15:39:25
dataid : 123
---

https://www.bootcss.com/p/git-guide/

git clone 地址

git clone -b 分支名 地址

git config --list

git pull

git init

git status

git add .

git commit -m 'test'

git push


------------
1.首先切换到分支；

git checkout hellomonkey
2.使用git pull 把分支代码pull下来；

git pull
3.切换到主分支；

git checkout master
4.把分支的代码merge到主分支；

git merge hellomonkey
5.git push推上去ok完成,现在 你自己分支的代码就合并到主分支上了。

git push

------------

### 1.查看所有分支
> git branch -a

### 2.查看当前使用分支(结果列表中前面标*号的表示当前使用分支)
> git branch
 
### 3.切换分支
> git checkout 分支名


git clone -b develop http://www.maijimaiji.cn:15650/web/mjtv-pc.git

git pull 

git clone -b fix-branch-2020-2-29 http://www.maijimaiji.cn:15650/web/mjtv-pc.git




fix-branch-2020-2-29








