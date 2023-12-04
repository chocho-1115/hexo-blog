---
title: GO笔记
date: 2022-09-13 11:39:25 
updated: 2022-09-13 18:32:41 
tags: [笔记]

---


<!--more-->

### 修改go的环境变量
`go env -w GOPATH=""`

### go初始化项目
`go mod init mygo`

### 编译可执行文件到指定目录
`go build -o ./bin/hw/ ./hw`

### go交叉编译（Mac 下编译 Linux 可执行程序）
登录服务器 查看目标平台的体系架构（386、amd64、arm）
`cat /proc/version`

`CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go build -o ./bin/ ./main`

GOOS：目标平台的操作系统（darwin、freebsd、linux、windows） GOARCH：目标平台的体系架构（386、amd64、arm） 交叉编译不支持 CGO 所以要禁用它

`ps -ef | grep main`
`kill pid`

### 执行可执行程序
`./main &`

### go 修改代理
`go env -w GOPROXY="https://goproxy.io,direct"`
`go env -w GO111MODULE=on`

### 整理依赖包
`go mod tidy`
命令执行后，go mod会去项目文件中发现依赖包，将依赖包名单添加到go.mod文件中，自动添加缺少的依赖包，自动删除那些有错误或者没有使用的依赖包。
不只是对依赖配置文件的修改，也会下载依赖包

### 下载项目依赖包
`go mod download`
下载modules到本地cache
所有模块版本数据均缓存在 $GOPATH/pkg/mod和 ​$GOPATH/pkg/sum 下

### 将依赖转移到项目的vendor目录下
go mod vendor
不建议转到项目目录下
用git管理项目时，该目录也不需要同步

### fmt.Print，fmt.Printf，fmt.Println的区别
Print函数直接输出内容，Print 中不能使用%s ,%d 或%c；
Printf函数支持格式化输出字符串，Printf 中能使用%s ,%d 或%c；
Println函数会在输出内容的结尾添加一个换行符，后面再输出内容会另起一行。

###
Go语言中，还可以使用 new 关键字对类型（包括结构体、整型、浮点数、字符串等）进行实例化，结构体在实例化后会形成指针类型的结构体。

### 切片和数组的区别
数组是值拷贝而切片是引用
数组是固定长度 切片是动态数组 长度可动态变化

### go接受前端的请求参数
https://www.cnblogs.com/liuhe688/p/11063945.html

### strconv.FormatFloat
[这里](https://blog.csdn.net/xixihahalelehehe/article/details/104672069/)解释的比较详细。


### 数组
数组不能调整大小；
数组的大小是类型的一部分；
... 可以代替数组长度 a := [...]int{12, 78, 50}

### gin热编译
gin -p 3000 -a 8081 --all run main.go



### go依赖查找顺序
当前包下的vendor目录。
向上级目录查找，直到找到src下的vendor目录。
在GOPATH下面查找依赖包。
在GOROOT目录下查找


go run *  和  go mod tidy  都会下载本地没有的依赖


然后这里再说下go install和go build，go build直接编译生成二进制文件在当前目录下

与build命令相比，install命令在编译源码后还会将可执行文件或库文件安装到约定的目录下。

go install编译出的可执行文件以其所在目录名(DIR)命名

go install将可执行文件安装到与src同级别的bin目录下，bin目录由go install自动创建

go install将可执行文件依赖的各种package编译后，放在与src同级别的pkg目录下

在使用go install编译二进制文件到GOPATH/bin目录下时，需要注意要配置环境变量GOBIN



现在输入 go install /imageServer/main，编译上述程序。该命令会在main文件夹内搜索拥有 main 函数的文件,然后生成一个二进制文件到GOPATH/bin中



