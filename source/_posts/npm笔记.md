---
title: npm笔记
date: 2019-05-09 01:39:25 
updated: 2019-05-09 01:39:25 
tags: [笔记]

---


<!--more-->



### linux下 npm install 遇到权限问题



{% img cont_img /img/2019/npm笔记-01.jpg title %}

解决办法：npm install 添加参数 `--unsafe-perm`

[参考](https://www.cnblogs.com/ganiks/p/stack-error-eacces-denied-node-gyp.html)


### 升级所有的依赖包
npm install -g npm-check-updates //安装npm-check-updates

ncu //检查更新

ncu -u //更新package.json




