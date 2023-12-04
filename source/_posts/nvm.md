---
title: [nvm与ncu]
tags: 笔记
date: 2023-08-03 15:39:25 
updated: 2023-08-03 15:39:25 
dataid : 
---

用nvm来管理你本地多个node版本 不要用n来管理你的本地多个node版本

nvm 与 n 的区别
node 版本管理工具还有一个是 TJ大神的 n 命令，n 命令是作为一个 node 的模块而存在，而 nvm 是一个独立于 node/npm 的外部 shell 脚本，因此 n 命令相比 nvm 更加局限。

由于 npm 安装的模块路径均为 /usr/local/lib/node_modules，当使用 n 切换不同的 node 版本时，实际上会共用全局的 node/npm 目录。 因此不能很好的满足『按不同 node 版本使用不同全局 node 模块』的需求。

### nvm

#### 查看nvm下node的安装目录
nvm which [current | <version>] 

#### nvm下全局依赖位置
/Users/[username]/.nvm/versions/node/[vx.x.x]/lib/node_modules

#### 升级nvm
去官方 https://github.com/nvm-sh/nvm 找到安装命令 curl
安装后 重新打开终端查看nvm版本

#### 设置默认的node版本
nvm alias default 18.12

### ncu

#### 升级所有的依赖包
npm install -g npm-check-updates //安装npm-check-updates

ncu // 检查更新

ncu -u // 更新package.json

谨慎使用 依赖包相互之间很可能不兼容

