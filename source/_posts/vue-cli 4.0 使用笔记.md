---
title: vue-cli 4.0 使用笔记
date: 2019-11-11 11:19:02
tags: [笔记]
---


### css-loader  postcss-loader 的区别

...

### scss 全局变量的使用

~~~ js vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      scss: {
        prependData: `@import "~@/assets/css/variables.scss";`
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

### vue 跳转

router-link会渲染为a标签

* params跳转
地址不携带参数；
获取参数方式：~ this.$route.params ~
相应连接： /project/details
~~~ html
<router-link :to="{name: 'team-details', params: { id: 1 }}"></router-link>
~~~
~~~ js
{
  path: "/team/details",
  name: "team-details",
  component: () => import("XXX.vue")
}
~~~

* query跳转
地址携带参数；
获取参数方式：~ this.$route.query ~
相应连接： /project/details?id=2
~~~ html
<router-link :to="{name: 'team-details', query: { id: 1 }}"></router-link>
~~~
~~~ js
{
  path: "/team/details",
  name: "team-details",
  component: () => import("XXX.vue")
}
~~~
* js this.$router.push跳转不指定query/params
相应连接： /project/details/2
~~~ js
this.$router.push({
  path: `/team/details/${id}`
})
~~~
~~~ js
{
  path: "/team/details/:id",
  name: "team-details",
  component: () => import("XXX.vue")
}
~~~

注意：如果提供了 path，params 会被忽略