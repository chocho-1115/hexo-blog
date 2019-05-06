---
title: webpack 一套工程代码 管理多个相似项目
date: 2018-12-27 15:39:25 
updated: 2018-12-27 15:39:25 
tags: [webpack]

---
### 业务场景

经常会有一些相互独立的活动类H5开发，当量多的时候，我们就需要一套工程化管理模式来解放我们的双手。

因为每一个独立的活动H5体量都不大，所以不太可能一个项目对应一套工程。所以我们需要的是一套能管理多个类似项目的模式，实现了一个工程管理多个项目。

<!--more-->

### 功能构想

* ./template/目录下存放模板文件；
* ./src/目录下存放n个项目；
* `npm run create demo` 新建demo项目，实现复制模板文件夹下的所有文件到src目录下；
* `npm run dev demo` 本地运行项目，来进行开发测试；
* `npm run build demo` 打包demo项目至./dist/demo下；
* `npm run template` 实现本地模板的不断迭代更新；

### 实现

[https://github.com/chocho-1115/h5-webp](https://github.com/chocho-1115/h5-webp)

还在继续完善中...


