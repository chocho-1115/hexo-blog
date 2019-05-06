---
title: window下修改npm全局安装模式的路径
date: 2016-10-06 03:08:31
tags: [笔记]
---
window用npm安装的全局模块，默认是在C:\Users\Administrator\AppData\Roaming\npm\node_modules下（可以通过modules root -g查看）。这没什么问题，但是如果你觉得这有点不爽，想修改这个安装目录，那么可以通过下面的步骤来实现。

先运行下面两句命令

npm config set prefix "E:\nodejs\node_global" //模块安装目录
npm config set cache "E:\nodejs\node_cache" //缓存目录

当然这两个目录不一定是nodejs的安装目录，你可以随意。

当我们运行这两句之后，在C:\Users\Administrator目录下会多了一个 .npmrc的文件，其内容如下：

prefix=E:\nodejs\node_global
cache=E:\nodejs\node_cache

ok 修改完毕！

接下来你可以在任何目录下运行下面的代码 npm -v

此时你可以通过安装一个模块来测试

npm install gulp -g

这个时候的gulp安装到了E:\nodejs\node_global\node_modules目录下了！

但是当我们通过`gulp -v`来测试安装是否成功的时候，却不能返回正确的结果。为什么npm可以全局访问，而gulp不能呢？

原因在与访问npm的时候，其实访问的是C:\Users\Administrator目录 然后通过.npmrc重新定向到E:\nodejs\node_global。而访问gupl的时候没有这样的重定向步骤，所以我们还需要修改系统的环境变量PATH的值为：
C:\Users\Administrator\AppData\Roaming\npm;E:\nodejs\node_global
window的环境变量在哪里设置这里就不赘述了。

这样当我们在命令行内运行全局变量的时候，如果在本地目录找不到就会在C:\Users\Administrator\AppData\Roaming\npm目录找 再找不到就在E:\nodejs\node_global目录下找了










