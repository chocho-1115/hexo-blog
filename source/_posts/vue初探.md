---
title: vue.js初探笔记
date: 2017-09-24 06:32:33 
tags: [框架]
---
前端技术框架的发展在过去的几年里发展极快，在填补原有技术框架空白和不足的同时也渐渐趋于成熟。Vue.js作为目前最热门最具前景的前端框架之一，其提供了一种帮助我们快速构建并开发前端项目的新的思维模式。本文主要记录在窥视vue神奇魅力的过程中的一些简单笔记，以备日后温故。

<!--more-->

### 准备工作

安装/升级node和npm,最简单的方式直接下载node安装文件重新安装，或者覆盖安装即可。

安装webpack 
```
# 全局安装
npm install  webpack -g
```

记录下 常用命令：
```
# 查看安装目录地址 
which node 

# 清空终端 眼花的代码
clear

#模块安装过程中如果报如下错误 是权限不够 需 sudo 提升权限
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules
```

### 安装vue.js

```
# 安装 vue
$ npm install vue
# 全局安装 vue-cli
$ npm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
$ vue init webpack vuedemo

```
在安装node后，我们可以通过node -v命令来查看是否安装成功。但是vue安装后，并不能通过vue -v命令来查看是否安装成功：
```
chochodeMacBook-Pro:~ chocho$ vue -v
-bash: vue: command not found
```
** vue -V （V大写）才能查看到vue的版本消息。**

安装vue-cli的时候按需选择模块：
{% img cont_img /img/2017/vue1.jpeg title %}

这个时候项目现在还只是一个结构框架，整个项目需要的依赖资源都还没有安装，需要运行 cnpm install。安装完成之后，会在我们的项目目录下多出一个node_modules文件夹，这里边就是我们项目需要的依赖包资源。

### 运行vue
npm run dev
这里简单介绍下 npm run dev 命令，其中的“run”对应的是package.json文件中，scripts字段中的dev，也就是 node build/dev-server.js命令的一个快捷方式。

项目运行成功后，浏览器会自动打开localhost:8080

### 发布打包



先说说一些打包配置问题：
在config文件夹下可以看到一个index.js文件，打开其中可以看到有几个属性分别是：

assetsRoot: path.resolve(__dirname, '../dist')//其实这个就是刚才打包后的根目录文件夹dist,所有的文件都会输出到这里
assetsSubDirectory: 'static'  //默认是把css,fonts,images,js输出到这个文件夹下
assetsPublicPath: '/'  这个就是静态文件的引用前缀


```
# 打包命令
$ npm run build 
```
打包完后，目录下多了一个dist的文件夹，里面存放的就是最后需要发布的文件了。在本地不能直接运行打包的文件，查看浏览器报错，资源路径不对。这里需要调整上面说到的index.js里的assetsPublicPath参数即可。















