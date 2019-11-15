---
title: iview-admin二次开发---登录
date: 2019-10-23 11:19:02
tags: [笔记]
---

### proxyTable代理设置

``` javascript
module.exports = {
 
  baseUrl: BASE_URL,
 
  chainWebpack: config => {
    config.resolve.alias
      .set('@', resolve('src')) 
      .set('_c', resolve('src/components'))
      .set('_conf', resolve('config'))
  },
 
  productionSourceMap: false,
 
  //添加如下配置
  devServer: {
    proxy: {
      '/api': {
        target: 'http://X.X.X.X:X/',// 服务器接口地址
        changeOrigin: true
      }
    }
  }
}
```

### config目录下的index.js文件做如下修改。

``` javascript
   /**
    * @description api请求基础路径
    */
    baseUrl: {
    dev: 'api/index.php/',
    pro: 'api/index.php/'
    },
```


### 注释掉main.js里的下面这句代码

``` javascript
// if (process.env.NODE_ENV !== 'production') require('@/mock') //本地接口模拟 调用后台地址后 需要注释这句代码 
```


至此本地开发环境配置代理服务修改完毕，重新执行：npm run dev 即可。

### 登录回调res为undefined

/src/store/module/user.js 目录下，登录 handleLogin 返回的 Promise resolve状态需要传值data





### https://blog.csdn.net/httguangtt/article/details/88575996

### https://juejin.im/post/5b71307c6fb9a009d22fc038


