---
title: ubuntu部署nginx+php后的配置
date: 2021-12-09
updated: 2023-07-09
tags: [笔记]

---

<!--more-->

Ubuntu 22.04.2 LTS
nginx/1.18.0
PHP 8.1.2

##### 重装系统具体步骤
1、备份：备份项目代码、nginx配置、ssl证书
2、重装系统，重新登录。登录前去本地用户目录~/.ssh/known_hosts删除过期的ssh key，然后重新登录远程服务器。
3、执行`apt update`更新 Linux 系统的包索引或包列表
4、安装 `apt install nginx php-fpm`，不指定版本，下载最新稳定版
5、`apt list --installed php*` 查看php-fpm的版本
6、安装php扩展`apt install php8.1-curl php8.1-mysql`，与php-fpm版本一致
7、上传项目代码、nginx配置、ssl证书
8、重启nginx
9、go可执行文件权限修改为555 某些txt文件权限修改为777
9、重启go服务 
10、新建用户组与用户

##### ？
重装ecs系统后 远程登录时报错：
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
解决办法：
ssh-keygen -R 120.76.97.207

##### 查看系统版本
`lsb_release -a`

##### 安装nginx+php
`apt install nginx`
`apt install php-fpm`

这里直接安装php-fpm即可，如果安装的是php，默认会用apache。

##### apt update
首次安装会报错 `E: Unable to locate package nginx`，需要执行 `apt update`。
apt update 命令更新 Linux 系统的包索引或包列表。它不会升级任何软件包，不要被update误导，运行此命令非常安全。

### 查看安装目录
whereis nginx

##### 搜索已安装的软件包
`apt list --installed nginx*`
`apt list --installed php*`

##### systemctl与service
优先使用systemctl

##### 启动（重启）
systemctl restart php8.1-fpm
systemctl restart nginx

##### 查看启动状态
systemctl status php8.1-fpm
systemctl status nginx

##### 查看进程
ps -ef | grep nginx

##### 安装php扩展
apt install php8.1-curl php8.1-mysql

安装php及其扩展时都带上版本号，就会少下载几个apt包，与php-fpm版本一致。

### 卸载php
apt remove php8.1 卸载php
apt purge php8.1 卸载php并删除相关配置
apt autoremove 自动删除所有未使用的包

### 查看所有用户组和用户
cat /etc/group // 查看所有用户组 查看用户组配置文件
cat /etc/shadow // 查看所有用户 查看用户配置文件
cat /etc/passwd //

### 新建用户组
groupadd web // 新建用户组：web

### 新建用户
useradd -g web -m chocho // 新建用户chocho属于web组 -m是代表需要新建用户主目录 默认地址为：/home/chocho 新建的目录和用户名同名；
useradd -g web -m -d /home/chocho chocho // 同上 -d是代表-m新建的目录位置 所以-m只是代表需要新建目录 这里的目录名可以与用户名不一样；
useradd -g web -d /web chocho //不加-m参数，将会新建一个没有主目录的用户 登录后的目录为-d指定的，此时该目录不是主目录，只是登录后的目录。通过`userdel -r chocho`是无法删除该目录的

### 将用户添加到组
usermod -a -G web root 将用户添加到用户组的命令 `-a`选项表示添加用户到用户组，`-G`选项后跟用户组名

### 将用户从某个用户组中移除
gpasswd -d root web

### 修改用户信息
usermod -d /web chocho 可以修改/etc/passwd内的主目录配置信息，重新指定登录后的目录，但是主目录内的文件不移动
usermod -m -d /web chocho 移动主目录位置，也可重命名主目录

-d：单独使用时，只是把保存在/etc/passwd这个配置文件当中的源目录名改成指定的新目录名，并不会把源目录下的内容移动到新目录下，如果要把源目录下的内容移动新目录下，则要和-m选项一起使用，才会把源目录下的内容移动到新目录下。

-m：与-d一起使用时会将原有的家目录移动要指定目录并根据需要改名

### 设置用户密码
passwd chocho // 设置密码

### 删除用户
userdel -r chocho // 删除用户 -r 选项表示在删除用户的同时删除用户的家目录。

### 查看当前用户所属组
whoami // 当前用户所在组组名
groups root

### 查看目录所属组及这个目录相应的权限
ls -l

### 查看目录及文件类型
ll

### 将某目录归属到某个组
chgrp -v -R web /web // -R递归该目录下所有文件和文件夹 -v显示指令执行过程 将/web目录移到web组下

### 设置组权限
[参考](https://www.runoob.com/linux/linux-comm-chmod.html)
chmod g=rwx /web // web目录所属的组权限设置为：可读、可写、可执行 g是代表组权限（该目录所属的组拥有的权限）

##### 配置nginx
http：
```
server {
    listen 80;   #监听端口
    server_name  indent.top www.indent.top;   #监听地址       
    location  / {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
        root /;  #根目录
        index index.html;  #设置默认页         
    }
    location ~ \.php$ {
        root /;  #根目录
        index index.html;  #设置默认页  
        # include snippets/fastcgi-php.conf;
        include /etc/nginx/snippets/fastcgi-php.conf;
    
        # With php-fpm (or other unix sockets):
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        # With php-cgi (or other tcp sockets):
        # fastcgi_pass 127.0.0.1:9000;
    }
}
```
https：
```
server {
        listen 443 ssl;   #监听端口

		ssl_session_cache shared:SSL:10m;
		ssl_session_timeout 10m;  
		ssl_certificate /etc/nginx/ssl_key/6682508_www.indent.top.pem; # 证书路径
		ssl_certificate_key /etc/nginx/ssl_key/6682508_www.indent.top.key; # 请求认证 key 的路径
		
}
```
*
这里的fastcgi-php.conf和php8.1-fpm.sock两项配置要和实际的文件目录一致。
因为是阿里云ecs这里需要在控制台-安全组添加80端口，如果是https对应443端口。。
*

### 查看nginx的配置文件位置已经它的有效性
nginx -t

##### php开放错误提示
find / -name php.ini -> /etc/php/7.4/fpm/php.ini
cd /etc/php/7.4/fpm/
cp php.ini php.ini.bak
vi php.ini
按i进入编辑模式
查找到display_errors把后面的off修改成on
按esc退出编辑模式
:wq 保存退出
service php8.1-fpm restart 重启php

* 这个配置建议可以不修改 保持默认 在php代码内 通过ini_set("display_errors","On");来灵活开放 *

### 查看nginx日志

tail /var/log/nginx/access.log
