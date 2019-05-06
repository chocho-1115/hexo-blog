---
title: php笔记
date: 2016-09-18 03:59:31 
tags: [php, 笔记]
---

---
#### php标记
XML风格(推荐)：`<?php  ...?>`
简短风格(需在配置文件中开启对此格式的支持)：`<? ..?>`
script风格:`<script language="php">...</script>`
asp风格(需在配置文件中开启对此格式的支持)：`<% ... %>`

---
#### 注释
多行：`/*  ... */`
单行：`// ...`，`# ..`

---
#### 获取表单数据
获取表单数据可以通过下面三个超级全局变量来获取：
`$_GET[ 'name' ]`，`$_POST[ 'name' ]`，`$_REQUEST[ 'name' ]`。


---
#### 单引号和双引号的区别
``` php
$name = 'Tom';
echo "My name is $name"; # 输出 My name is Tom
echo 'My name is $name'; # 树池 My name is $name
```
>用一个字符串的内容来代替一个变量的操作就是**插值**

---
#### heredoc语法写多行文本
``` php
	echo <<<theEnd
		line 1
		line 2
    	My name is $name
theEnd
```
heredoc字符串是插补的，所以第三行会输出 My name is Tom
theEnd作为标记符应该打头写(QAQ)，不然会报错:
```
Parse error: syntax error, unexpected end of file, expecting variable (T_VARIABLE) or heredoc end (T_END_HEREDOC)
```

---
#### php标识符与js标识符的区别
* 标识符是区分大小的，函数是个例外
* 变量名可以和函数名相同。php函数是语法上的结构体，不是一个变量，不能被赋值。尽管变量名可以和函数名相同，但应尽量避免。


#### 数据类型 
基本类型：

* Integer -- 整数
* Float -- 浮点数/Double/双精度值，用来表示所有实数
* String -- 字符串
* Boolean -- 布尔值
* Array -- 数组 用来表示相同类型的多个数据项
* Object -- 对象


特殊类型：

* NULL -- 空
* resource -- 资源

---
#### 数据类型转换
``` php
$num1 = 0;
$num2 = (float)$num1;
```
运行上面的代码后，$num2将使一个浮点数，而$num1还将保持整数类型

---
#### 可变变量

``` php
	$varname = 'a';
	$$varname = 5;
	echo $a;//5
	$varname = 'b';
	echo $varname;//b
	echo $a;//5
	echo $b;//没输出
```

---
#### 定义常量
`define('NAME' ,'Tom');`
常量名通常都是大写字母，但不是必须的。
引用常量的时候，变量名前面并没有$符号。
常量只可以保存标量数据（布尔值，整数，浮点数，字符串）。


---
#### Mysql 时间字段自动自动生成 无需应用程序生成再插入数据库


1、将字段类型设为  TIMESTAMP 

2、将默认值设为  CURRENT_TIMESTAMP



---
#### Mysql 取数据时的排序



 ORDER BY id DESC
 
 ORDER BY id ASC





---
#### empty()和isset()的区别

 * empty() 测试变量是否已经配置。若变量已存在、非空字符串或者非零，则返回 false 值；反之返回 true。
 * isset() 测试变量是否已经配置。若变量已存在则返回 true 值。其它情形返回 false 值。

它们都用来判断变量是否已经配置，它们却是有一定的区别：**empty还会检测变量是否为空、为零。当一个变量值为0，empty() 认为这个变量同等于空，即相当于没有设置。**

猜猜下面的代码会输出什么：
```php
<?php
    $id=0;

    echo(empty($id));
    print "<br>";

    echo(isset($id));
    print "<br>";

    empty($id)?print "It's empty .":print "It's $id .";
    print "<br>";

    isset($id)?print "It's $id ":print "It's empty ";


?>
```
输出：
1
1
It's empty .
It's 0

---
### $_REPUEST

$_REQUEST不是一个函数,它是一个超全局变量,它接收了  $_GET  $_POST  $_COOKIE  三个的集合

``` php
<?php
var_dump($_GET['a'],$_POST['a'],$_REQUEST['a']);
?>
<html>
<head><title>demo</title></head>
<body>
<form method= 'post' action = 'req.php?a=xxx'>
<input type='hidden' name='a' value='yyy'/>
<input type='submit' name='submit' value='submit'/>
</form>
</body>
</html>
```
当我提交表单的时候，我获取的页面内容为：
string(3) "xxx" string(3) "yyy" string(3) "yyy"
同样的内容，在$_REQUEST里面，POST的值覆盖了GET的值，这到底是怎么回事呢？

其实这是在PHP的配置文件里面设置的，让我们来看一下php.ini这个配置文件，在其中有这么一段内容如下：
; This directive describes the order in which PHP registers GET, POST, Cookie,
; Environment and Built-in variables (G, P, C, E & S respectively, often
; referred to as EGPCS or GPC). Registration is done from left to right, newer
; values override older values.
variables_order = "EGPCS"
这个EGPCS就是说明用$_REQUEST数组获取内容的优先级，其字母的含义分别代表为：**E代表$_ENV，G代表$_GET，P代表$_POST，C代表$_COOKIE，S代表$_SESSION。后面出现的数据会覆盖前面写入的数据，其默认的数据写入方式就是EGPCS**，所以POST包含的数据将覆盖GET中使用相同关键字的数据。

---
### 修改根目录
#### 1.修改Apache默认根目录
打开httpd.conf（主配置文件）
将``DocumentRoot “D:/wamp/www/”``
改成``DocumentRoot “e:/xx/”``
同时将``<Directory “D:/wamp/www/“>``
改成 ``<Directory “e:/xx/“>``
如果你的目录放在其他盘里一定要设置目录的权限 httpd.conf：
``` php
########################################################### start

<Directory />
    AllowOverride none
    Require all denied
</Directory>
改为：
<Directory />
    Options FollowSymLinks
    AllowOverride None
    Order deny,allow
    #Deny from all
    Allow from all
    #允许所有访问
    Satisfy all
</Directory>
########################################################### end
```

#### 2.修改wampserver的配置文件
打开wamp\scripts\config.inc.php
`` $wwwDir =$c_installDir.’/www’;``（ $c_installDir是个变量，指WAMPserver安装根目录。）
修改为：``$wwwDir = ‘E:/www;``

---
### 配置虚拟主机
#### 1.修改主配置
打开httpd.conf  把#Include conf/extra/httpd-vhosts.conf 前的#去掉开启虚拟主机
#### 2.修改虚拟主机的配置文件
打开配置文件：\bin\apache\apache2.4.17\conf\extra\httpd-vhosts.conf
```
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "E:\web\learning"
    ServerName learning.com
    ServerAlias www.learning.com
    ErrorLog "logs/dummy-host.example.com-error.log"
    CustomLog "logs/dummy-host.example.com-access.log" common
</VirtualHost>
```
#### 3.修改hosts文件
127.0.0.1 learning.com
127.0.0.1 www.learning.com

**另外需要注意的是：如果你的目录放在其他非安装盘下一定要在httpd.conf里设置目录的权限（同上）**