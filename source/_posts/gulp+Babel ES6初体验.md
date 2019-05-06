---
title: gulp+Babel ES6初体验
date: 2018-05-23 13:37:40
tags: [笔记,javascript]
description: 搭建一个gulp+Babel的运行环境
keywords: es6,gulp,学习
---

** 新建项目文件夹 **
mkdir gulp-es6-demo

** 进入项目文件夹 **
cd gulp-es6-demo

** 创建src文件夹和dist文件夹 **
mkdir src dist

** 初始化项目 得到package.json文件 **
cnpm init

** 安装babel-cli babel-preset-env **
npm install --save-dev babel-cli babel-preset-env 

** 创建babel配置文件.babelrc **
windows下新建该文件会提示必须键入文件名，解决办法是文件名如下.babelrc.
将规则加入到.babelrc文件中
~~~ json
{
  "presets": ["env"]
}
~~~

** 安装gulp gulp-babel gulp-watch **
npm install --save-dev gulp gulp-babel gulp-watch
  
** 创建gulpfile.js文件 **
将以下内容加入到gulpfile.js中
~~~ js
var gulp = require("gulp"),  
    babel = require("gulp-babel"), 
    watch = require("gulp-watch");  

gulp.task("babeljs", function () {  
return gulp.src("src/js/*.js")  
 .pipe(babel())  
 .pipe(gulp.dest("dist/js"));  
});  
gulp.task("watch",function(){
gulp.watch('src/js/*.js',['babeljs']);
})

gulp.task('default',['watch','babeljs']);
~~~

至此所有工具都已经安装好 并且配置好了。下面来写一段es6试试

在src下，新建js文件夹 并且新建demo.js。并输入如下内容：
~~~ js
[1,2,3].map(n => n + 1);
~~~

** 运行实现转码 **
gulp
我们可以看到在dist下生成了 dist/js/demo.js 文件，打开内容如下：
~~~ js
"use strict";

[1, 2, 3].map(function (n) {
  return n + 1;
});
~~~

