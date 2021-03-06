---
title: CentOS下nodejs安装
date: 2018-10-14 18:10:44
tags: [javascript]
---

阿里云购买服务器后，提供了一件安装各类系统的快捷方式。当然你也可以自己捣腾，来远程安装。今天尝试了一下远程安装nodejs，并做了下面的笔记，方便日后温故。

### 客户端连接远程服务器

官方列举了如下几种连接方式：
1. 使用浏览器发起安全连接
2. 客户端使用密钥进行连接
3. 客户端使用账号密码进行连接

如果已经在控制台启用过密钥，使用账号密码登录还需要按照《SSH配置说明》修改SSH服务配置或者删除密钥。

我这里之前没用过秘钥连接，想用账号密码方式连接，所以只要设置下密码即可。设置好后，在终端中输入如下命令：
ssh username@server-ip
{% img cont_img /img/2018/客户端连接远程服务器.png title %}

### CentOS常用基础命令大全
[CentOS常用基础命令大全](https://www.aliyun.com/jiaocheng/150140.html)

### 目录结构

输入命令ls查看：

/
├── bin   // 二进制可执行命令。该目录下存放着普通用户的命令
├── boot                    //启动Linux的核心文件
├── cgroup
├── dev         //系统的设备文件，即设备的驱动程序
├── etc           //系统所有的配置文件都在这个目录中
├── home            //用户主目录的基点
├── lib        // 存放着和系统运行相关的库文件 
├── lib64      // 存放着和系统运行相关的库文件 
├── lost+found  //这个目录平时是空的，当系统非正常关机而留下的“无家可归”的文件便会
                  存在这里
├── media          //存放着可移除的设备，比如软盘，光盘
├── misc        //储存着一些特殊的字符的定义
├── mnt             //挂载目录，是系统管理员临时安装文件的系统安装点
├── net      //存放着和网络相关的一些文件
├── opt //(option : 自由选择)主要给源码安装软件时选择的安装目录位置
├── proc          // 存放着用户与内核的交互信息
├── root          //超级用户的目录
├── sbin          //系统的管理命令，这里存放的是系统管理员使用的程序
├── selinux          //主要用来加固操作系统，提高系统的安全性
├── srv       //系统启动服务时可以访问的数据库目录
├── sys  //管理设备文件
├── tmp  //临时文件，重启后自动清空
├── usr       //最大的目录，存放着应用程序和文件
└── var      //某些大文件的溢出区，比如各种服务的日志文件

### Broken pipe错误
解决ssh的"Write failed: Broken pipe"问题
问题场景
服务器环境：阿里云 Linux CentOS 主机
客户端：Mac OSX Terminal
问题现象
用 ssh 命令连接服务器之后，如果一段时间不操作，再次进入 Terminal 时会有一段时间没有响应，然后就出现错误提示：
Write failed: Broken pipe
只能重新用 ssh 命令进行连接。
解决方法
方法一：如果您有多台服务器，不想在每台服务器上设置，只需在客户端的 ~/.ssh/ 文件夹中添加 config 文件，并添加下面的配置：
ServerAliveInterval 60
方法二：如果您有多个人管理服务器，不想在每个客户端进行设置，只需在服务器的 /etc/ssh/sshd_config 中添加如下的配置：
ClientAliveInterval 60
方法三：如果您只想让当前的 ssh 保持连接，可以使用以下的命令：
$ ssh -o ServerAliveInterval=60 user@sshserver





