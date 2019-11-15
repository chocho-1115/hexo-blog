---
title: vue-cli 4.0 使用笔记
date: 2019-11-11 11:19:02
tags: [笔记]
---

### console.log的使用
写项目过程中用ESLint遵守代码规范很有必要，但是对于一些规范也很是无语，比如：‘Unexpected console statement (no-console)’，连console都不能用，这就很抓狂了。其实增加一行代码即可。
修改package.json中的eslintConfig:{} 中的 “rules”:{}，增加一行代码: "no-console":"off"


### 全局js
在main.js文件中添加：
~~~ js
import util from "./assets/js/util.js";
Vue.prototype.util = util;
~~~

在view中调用方式
~~~ js
mounted: function() {
  console.log(this.util.getDemo(99));
}
~~~

### 打包后资源调用地址不对
根目录添加vue.config.js文件：
~~~ js
module.exports = {
  publicPath: process.env.NODE_ENV === "production" ? "./" : "/"
  // configureWebpack: config => {}
};
~~~


### css-loader  postcss-loader 的区别


### scss 全局变量的使用

~~~ js vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      scss: {
        prependData: `@import "~@/assets/css/main.scss";`
      }
    }
  }
};
~~~

直接按以上方式配置即可，不需要安装sass-resources-loader。


### vue组件的局部注册与全局注册


### swiper无法获取正确的宽度和高度
添加下面2个参数
~~~ js
observer: true,//修改swiper自己或子元素时，自动初始化swiper
observeParents: true,//修改swiper的父元素时，自动初始化swiper
~~~

### 前端应用快速启动服务 Anywhere
安装命令：npm install -g anywhere

使用：在要启动静态服务的目录下执行

anywhere -p 8080

-p 后跟端口号

