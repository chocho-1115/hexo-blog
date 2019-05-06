---
title: ajax封装
date: 2016-09-03 12:05:39 
tags: [javascript, ajax]

---
每次在项目中需要用到ajax时，就会立马引入jquery。要是遇到的是一个小小小的项目，比如报个名什么的，为了要用ajax必须引入jquery就显得有点尴尬了。为什么非得要用别人封装好的东西，虽然jquery很不错，但是自己的孩子才是最好的，于是我决定自己造这个轮子。
<!--more-->
  
轮子造多了，就需要一个放轮子的地方，JSeasy 就是我放轮子的地方：
``` javascript
window.JSeasy = window.JSeasy || {};
window.JSeasy.ajax = window.JSeasy.ajax || function(opts){
	...
};
```
  
jquery用习惯了，所以顺便仿照jquery加上get和post方法：
``` javascript
window.JSeasy.get = window.JSeasy.get || function(url,data,func,dataType){
	this.ajax({
		 method: 'get',
			url: url,
		   data: data,
	   dataType: dataType,
		success: func || function (){}
	})
};
window.JSeasy.post = window.JSeasy.post || function(url,data,func,dataType){
	this.ajax({
		 method: 'post',
			url: url,
		   data: data,
	   dataType: dataType,
		success: func || function (){}
	})
};
```

然后剩下的工作就是对window.JSeasy.ajax函数内部的编码了：

### 配置管理
``` javascript
	/*
	*参数说明：
	**method: 请求方式:GET/POST,默认值:'GET';
	**url:    发送请求的地址;
	**data:   传给服务器的数据;
	**async:  是否异步:true/false,默认值:true;
	**contentType: HTTP头信息，默认值：'application/x-www-form-urlencoded';
	**success: 请求成功后的回调函数;
	**error: 请求失败后的回调函数;
	*/
  
	var defaults = {
             method: 'get',
                url: '',
               data: '',
		   dataType: 'json',
		      jsonp: 'callback',//在一个jsonp请求中重写回调函数的名字。这个值用来替代在"callback=?"这种GET或POST请求中URL参数里的"callback"部分，比如{jsonp:'onJsonPLoad'}会导致将"onJsonPLoad=?"传给服务器。
	  jsonpCallback: '',
              async: true,
        contentType: 'application/x-www-form-urlencoded',
            success: function (){},
              error: function (){}
    };
  
    //参数覆盖
    for(var key in opts){
        defaults[key] = opts[key];
    }
  
	// data转成需要的字符串.
	defaults.data = (function(obj){
		var str = "";
		for(var prop in obj){
			str += prop + "=" + obj[prop] + "&"
		}
		return str.substring(0,str.length-1);
	})(defaults.data);
```
### jsonp
ajax的核心是通过XmlHttpRequest获取数据，而jauery中的get jsonp请求是通过动态添加 script 标签来调用服务器提供的js脚本，它与XmlHttpRequest对象无关。所以我们可以先考虑get jsonp请求
首先看一下jquery的get jsonp请求的简单原理示例：

``` html
<!-- 客户端 html代码 -->
<script type="text/javascript">
            function jsonpCallback(data){
           	 	alert(data);
            }
</script>
<script type="text/javascript" src="XXX.php?callback=jsonpCallback"></script>
```

``` php
<!-- 服务器端 php代码 -->
	$callback = $_REQUEST['callback'];
  
	$res['list'] = array();
	$res['time'] = time();
	$res['type'] = $type;
  
	$result = json_encode( $res );
	echo $callback."($result)";
```
当我们打开客户端页面的时候 会完成请求并运行jsonpCallback函数，并获取到服务器端返回的数据。其中src="XXX.php?callback=jsonpCallback"中的callback和jsonpCallback是可以自定义的，jsonpCallback代表请求完成后运行的回调函数名。

我们在jquery中的jsonp请求，可以通过get函数的第3个参数来传一个匿名函数。当回调函数是一个匿名函数时，jquery在jquery内部会自动生成一个临时的全局函数，这个函数的函数名是一个随机的字符串。这个字符串我们可以在浏览器的调试模式下看到，例如：

url?callback=jQuery11020056439777318201045_1474620208579
在这个名为jQuery11020056439777318201045_1474620208579的临时全局函数会在请求成功后调用，并在内部运行我们调用$get方法时传过来的匿名回调函数。当完成了这些步骤后，这个临时函数也将会注销掉。

所以在我们的jsonp异步请求中，如果传过来的回调函数是匿名函数，那么我们也需要这样一个临时函数:

``` javascript
	if(defaults.dataType=='jsonp'){
		var jsonpCallback = defaults.jsonpCallback;
		if(!jsonpCallback){
			jsonpCallback = randomString(32);
			window[jsonpCallback] = function(data){
				defaults.success(data);
				window[jsonpCallback] = undefined;
			}
		}
  
		var ele= document.createElement("script");
		ele.type = "text/javascript";
		ele.src = defaults.url+'?'+defaults.data+'&'+ defaults.jsonp +'='+jsonpCallback;
		document.getElementsByTagName("HEAD")[0].appendChild(ele);
		return false;
	}
  
    function randomString(len) {
    	　　len = len || 32;
    	　　var $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';// 默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1
    	　　var maxPos = $chars.length;
    	　　var sttr = '';
    	　　for (i = 0; i < len; i++) {
    	　　　　sttr += $chars.charAt(Math.floor(Math.random() * maxPos));
    	　　}
    	　　return 'JSeasy_' + sttr + '_' + new Date().getTime();
    }
  
```
### XMLHttpRequest
然后我们考虑通过 XMLHttpRequest 的请求,这一部分的内容，网上一大把，而且很多都写的很详细了，这里就直接上代码吧：
```javascript
    var xhr = createXHR();
	xhr.onreadystatechange = function(){
		 if(xhr.readyState==4){//AJAX所经历过的几种状态
			if((xhr.status>=200&&xhr.status<300)||xhr.status == 304){//由HTTP协议根据所提交的信息 表示响应的HTTP状态码
				 var callbackData = xhr.responseText;
				 if(defaults.dataType=='json'){
					callbackData = -[1,]?JSON.parse(callbackData):eval("("+callbackData+")");
				 }
				 defaults.success(callbackData);
				 xhr=null;
			}else{
				defaults.error(xhr.status);
				throw("Request was unsuccessful : " + xhr.status);
			}
		}
	};
  
	if(defaults.method=='post'){
		xhr.open(defaults.method,defaults.url,defaults.async);
		xhr.setRequestHeader('Content-Type',defaults.contentType);
		xhr.send(defaults.data);
	}else{
		xhr.open(defaults.method,defaults.url+'?'+defaults.data,defaults.async);
		xhr.send(null);
	}
	//XMLHttpRequest 对象兼容性处理
	function createXHR(){
		if(typeof XMLHttpRequest != 'undefined'){return new XMLHttpRequest();}
		else if(typeof ActiveXObject != 'undefined'){//ie7之前的版本
			if(typeof arguments.callee.activeXString !="string"){
				var versions=['MSXML2.XMLHttp.6.0','MSXML2.XMLHttp.3.0','MSXML2.XMLHttp'];
				for (var i=0,len=versions.length;i<len;i++){
					try{
						var xhr=new ActiveXObject(versions[i]);
						arguments.callee.activeXString=versions[i];
						return xhr
						}catch(ex){
							//跳过
							}
					}
				}
				return new ActiveXObject(arguments.callee.activeXString);
		}else{throw new Error('No XHR object available.');
		}
	}
```
下载完整代码点击[这里](http://www.indent.top/demo/ajax/ajax.js)。当然我这里只是简单封装了一下,还是比不上jquery的ajax强大。

下面是我本地测试的代码：
``` javascript
$.post("url", {type:'jq_post',act:1}, function(data){
				console.log(data)
},'json');
  
$.get("url", {type:'jq_get_jsonp',act:2}, function(data){
				console.log(data)
},'jsonp');
  
$.get("url", {type:'jq_get_json',act:1}, function(data){
				console.log(data)
},'json');
  
  
/*++++++++++++++++++++++++     +++++++++++++++++++++++++++++++*/
  
JSeasy.get('url',{type:'js_get_json',act:1},function(data){
	//var data=JSON.parse(data);
	console.log(data)
},'json');
  
JSeasy.get('url',{type:'js_get_jsonp',act:2},function(data){
	//var data=JSON.parse(data);
	console.log(data)
},'jsonp');
  
JSeasy.post('url',{type:'js_post_json',act:1},function(data){
	//var data=JSON.parse(data);
	console.log(data)
},'json');
  
JSeasy.ajax({
             method: 'get',
                url: 'url',
               data: {type:'js_ajax_get_jsonp',act:3},
		   dataType: 'jsonp',
			  jsonp: 'a',//在一个jsonp请求中重写回调函数的名字。这个值用来替代在"callback=?"这种GET或POST请求中URL参数里的"callback"部分，比如{jsonp:'onJsonPLoad'}会导致将"onJsonPLoad=?"传给服务器。
	  jsonpCallback: 'k',
              //async: true,
              //cache: true,
        //contentType: 'application/x-www-form-urlencoded',
            //success: function (data){console.log(data)},
              //error: function (status){console.log(status)}
});
  
function k(data){
	console.log(data)
}
  
```
{% img cont_img /img/2016/ajax.png title %}


另外还有一个CORS跨域的方式：[AJAX POST&跨域 解决方案 - CORS](http://www.cnblogs.com/Darren_code/p/cors.html)