---
title: addEventListener的第三个参数
date: 2018-04-12 00:31:35
tags: [笔记,javascript]
---


微信h5手势下滑，会拖动页面，露出网页来源的文字。对于一些需要页面滑动交互的h5，可以通过下面的代码来阻止微信浏览器的这一行为。但是前几天突然发现部分ip手机下e.preventDefault()居然不起作用了。。。
<!--more-->
``` javascript
var stopDefaultScroll = function(e){
	//console.log(e.cancelable) true
	e.preventDefault();
	e.stopPropagation();
}
	
$(window).load(function(e) {
	document.addEventListener('touchmove',stopDefaultScroll,false);
})
```
于是各种查资料，发现最新的api里addEventListener的第三个参数跟以往所认知的不一样了：
[api](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
[passive 的事件监听器](https://www.cnblogs.com/ziyunfei/p/5545439.html)

直接把addEventListener的第三个参数改成{passive: false}就可以了（额，装不下去了。。。睡觉！！！）
