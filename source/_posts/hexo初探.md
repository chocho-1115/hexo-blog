---
title: Hexo初探
date: 2016-06-01 03:10:33 
tags: hexo
---





一直想尝试在github上开一个静态博客，千挑万选之后最终在[Hexo](https://hexo.io/)和[jekyll](http://jekyll.bootcss.com)之间犹豫，看了网上很多网友都从jekyll转用了hexo，于是我决定捣腾Hexo! 而且这名字看起来也顺眼多了，哈哈！

### Hexo初印象
* 源起 [https://zespia.tw/blog/2012/10/11/hexo-debut/](https://zespia.tw/blog/2012/10/11/hexo-debut/)
* [hexo官网](https://hexo.io/zh-cn/)
* A fast, simple & powerful blog framework
* 支持Markdown
* 一键部署博客
* 丰富的插件支持
* hexo依赖Node.js和Git

### 部署
##### 安装
hexo依赖Node.js和Git，所以这两个必须已经安装，然后用下面命令安装hexo：
``` bash
npm install hexo -g
``` 
（ *mac下需sudo提示权限* ）

##### 初始化
新建blog目录并cd blog，再初始化hexo
``` bash
cd blog
hexo init
``` 
初始化完成之后，目录结构如下：
>~  tree -L 1 demo
demo
├── _config.yml
├── package.json
├── scaffolds
├── source
└── themes
3 directories, 2 files

hexo的配置文件是_config.yml，可以在官方文档[Configuration](https://hexo.io/docs/configuration.html)里查看详细解释。

##### 启动本地服务
``` bash
hexo generate
hexo server
``` 
这时可以在浏览器输入 localhost:4000 看到本地hexo博客了

##### 部署到github
复制你的GitHub Pages地址，如图：
{% img cont_img /img/2016/1.jpg title %}

并修改_config.yml配置文件，如下图：

``` html
deploy: 
  type: git
  repository: https://github.com/username/username.github.io.git
  branch: master
``` 
（ *在修改_config.yml时要注意，不要随便添加或增加空格，比如冒号后面有一个空格不能省，前面的缩进也不能动* ）


最后提交代码到github：
``` bash
hexo generate
hexo deploy
``` 

这时如果出现了error deployer not found:github 的错误，那么需要再执行如下命令
``` bash
npm install hexo-deployer-git --save
``` 
然后再执行hexo deploy命令

这时访问 https://github.com/username/username.github.io.git 就可以看到hexo的博客了！当然如果你不喜欢这么长的链接地址，那么github上的博客也可以绑定自己的域名。

##### 更换主题
hexo默认的主题不是那么美观，我们可以自己更换主题，官网提供了丰富的主题选择[https://hexo.io/themes/](https://hexo.io/themes/)
或者这里[https://github.com/hexojs/hexo/wiki/Themes](https://github.com/hexojs/hexo/wiki/Themes)
``` bash
git clone <repository> themes/<theme-name>
``` 
（ *repository为主题托管地址，theme-name为主题名字* ）

很多主题在安装好后，你会发现不管是本地，还是github上访问博客，打开速度都很慢。原因是里面加载了一些国外服务器上的资源（中国特色，你懂得），或者是[google字库导致hexo modernist首页加载变慢](http://ibruce.info/2013/12/03/fonts-googleapis-lead-to-slow/).具体原因还得在谷歌浏览器查看是什么资源阻塞了页面加载，如果是下面这个js加载的问题的话，我们可以把它下载至我们本地，改成相对链接：
``` html
<script src="//ajax.googleapis.com/ajax/libs/jquery/2.0.3/jquery.min.js"></script>
``` 
打开主题安装目录下的layout文件夹，如：blog/themes/主题名/layout，打开里面的文件，查找上面jquery被引入的地方，通常在after_all.ejs文件里面找到。将其地址修改为
``` html
<script type="text/javascript" src="<%- config.root%>js/jquery.min.js"></script>
``` 

然后我们将//ajax.googleapis.com/ajax/libs/jquery/2.0.3/jquery.min.js的js下载至本地的blog/themes/主题名/source/js/下面即可。



### 补充
##### 我在windows下安装hexo时报错
``` bash
npm WARN optional dep failed, continuing fsevents@1.0.14
``` 
解决办法： 需要升级npm
window下  运行以下命令来升级npm：
``` bash
npm install -g npm-windows-upgrade
``` 
> **参考：**[http://stackoverflow.com/questions/18412129/how-do-i-update-node-and-npm-on-windows](http://stackoverflow.com/questions/18412129/how-do-i-update-node-and-npm-on-windows)

mac下升级npm：
``` bash
sudo npm cache clean -f //清缓存
sudo npm install -g n //安装n模块
sudo n stable //升级为稳定版
node -v
``` 
---
##### Markdown 编辑器
在 Mac OS X 上，  [Mou](http://25.io/mou/)。
在 windows 上 有两款：[ MarkdownPad ](http://www.markdownpad.com/)，[ MarkPad ](http://code52.org/DownmarkerWPF/)。
这三款我都有试过，当然还是Mou最好用。

---
##### Markdown 语法
[创始人 John Gruber 的 Markdown 语法说明](http://daringfireball.net/projects/markdown/syntax)
[Markdown 中文版语法说明](http://wowubuntu.com/markdown/#list)


### seo 笔记
查看网站是否被收录
site:www.indent.top

网站层级不要超过三级

网站地址不能有中文，中文的不能生成网站地图xml

虚拟主机不支持ssh提交















