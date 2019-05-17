---
title: webpack 一套工程代码 管理多个相似项目
date: 2018-12-27 15:39:25 
updated: 2018-12-27 15:39:25 
tags: [webpack]

---
### 项目地址

[h5-webp](https://github.com/chocho-1115/h5-webp)

### FOUC
使用webpack打包项目后，css以style内嵌样式的方式放在head里，所以打开出现了短暂的无样式状态。

解决办把就是把css抽离出来，用link的形式引入。抽离css可以用 [mini-css-extract-plugin](https://www.npmjs.com/package/mini-css-extract-plugin)（extract-text-webpack-plugin）。

mini-css-extract-plugin只负责抽离css，并不能压缩css。


### webpack中hash、chunkhash和contenthash三者的区别
简单理解：
hash 项目指纹
chunkhash 文件指纹
contenthash 内容（模块）指纹