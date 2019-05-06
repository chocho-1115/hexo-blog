---
title: sound.js
date: 2017-01-14 13:11:35
tags: [createjs]
---

因为对SoundJs的使用还不是很熟悉，在好几次赶项目的时候，对音频的管理是基于audio标签的。虽然没什么不可，但是既然用了createjs，而音频这块却避开SoundJs，显得多少有点不专业，何况作为一名还算有点追求的前端er，对待工作怎么能敷衍了事呢。于是抽点时间研究了下SoundJs，顺便记下这点笔记。

<!--more-->

SonudJs作为CreateJS的音频处理套件，为我们提供了一套简单而强大的音频处理方法。大多数情况下这个类库都静态方式使用（无需创建实例）。

### 初始化SoundJs插件
通过`createjs.Sound.initializeDefaultPlugins()`来初始化，SoundJs，将返回布尔值，告诉我们是否初始化成功。一般来说，我们无需手动初始化插件，当我们播放或注册任何音频时，将自动调用此方法。

---

### 设备判断
音频在不同的设备下，存在一些差异。所以载入SoundJs后，在createjs.BrowserDetect下储存了当前设备的信息。下面是相关源码：

```
(function() {

	"use strict";
	
	function BrowserDetect() {
		throw "BrowserDetect cannot be instantiated";
	};

	var agent = BrowserDetect.agent = window.navigator.userAgent;
	BrowserDetect.isWindowPhone = (agent.indexOf("IEMobile") > -1) || (agent.indexOf("Windows Phone") > -1);
	BrowserDetect.isFirefox = (agent.indexOf("Firefox") > -1);
	BrowserDetect.isOpera = (window.opera != null);
	BrowserDetect.isChrome = (agent.indexOf("Chrome") > -1);  // NOTE that Chrome on Android returns true but is a completely different browser with different abilities
	BrowserDetect.isIOS = (agent.indexOf("iPod") > -1 || agent.indexOf("iPhone") > -1 || agent.indexOf("iPad") > -1) && !BrowserDetect.isWindowPhone;
	BrowserDetect.isAndroid = (agent.indexOf("Android") > -1) && !BrowserDetect.isWindowPhone;
	BrowserDetect.isBlackberry = (agent.indexOf("Blackberry") > -1);

	createjs.BrowserDetect = BrowserDetect;

}());

```

---

### 载入音频

先看示例：
```
createjs.Sound.alternateExtensions = ["mp3","WAV"];
createjs.Sound.addEventListener("fileload", function(event){
	 createjs.Sound.play(event.id);//参数：String, 可以是载入时指配的id，也可以是src地址。
});
createjs.Sound.registerSound('media/sound1.mp3','sound1');//参数：src String; [id] String
createjs.Sound.registerSound('media/sound2.ogg','sound2');
```
我们通过registerSound方法来注册音频，第一个参数为音频地址，第二个参数为音频id（可选）。
当然如果想一次注册多个音频，可以通过另一个方法registerSounds来实现：

```
createjs.Sound.registerSounds([
	{src:'media/sound1.mp3',id:'sound1'},
	{src:'media/sound1.ogg',id:'sound2'}
]);
```
不同的终端对音频格式的支持情况有所不同。如果终端不支持src所指定的音频格式，registerSound会按createjs.Sound.alternateExtensions数组里指定的扩展名顺序，来选择自己支持的音频格式载入。
所以上面的示例中，如果终端不支持ogg音频，registerSound会选择载入'media/sound1.mp3'。如果也不支持mp3，就会尝试载入'media/sound1.WAV'。


---

### AbstractSoundInstance

要用Sound来管理音频，就需要创建AbstractSoundInstance。创建AbstractSoundInstance有2种方式：



* 通过createjs.Sound.createInstance创建:

	`var sound = createjs.Sound.createInstance(id/src)`
	
* 通过调用createjs.Sound.play创建:

	` var sound = createjs.Sound.play(id/src)` //音频将直接播放
	通过createjs.Sound.play创建时，可以通过第二个可选参数来指定音频播放属性：
```
var sound = createjs.Sound.play(id/src,{
	loop:10, //循环，>=1 为播放总次数；==0不循环播放一次；<0 播放无限次
	delay:0, //延迟 单位毫秒
	offset:0, //偏移 单位毫秒
	volume:1, //音量
	interrupt:0,
	pan:0,
	startTime:0, 
	duration:0
	
})
```

创建的AbstractSoundInstance带有能控制音频的方法与属性：
```
sound.play(Parameters);//可传递音频播放属性
sound.stop();//停止

sound.paused
sound.muted
sound.volume

sound.setMuted()//设置静音
sound.setPaused(Boolean)//设置暂停
sound.setVolume(Number)//设置音效

sound.getMuted()//获取静音
sound.getPaused(Boolean)//获取暂停
sound.getVolume(Number)//获取音效


```

### 主音频
通过对AbstractSoundInstance的操作能控制单个音频。要控制所有音频，可以通过设置createjs.Sound来实现：
createjs.Sound.stop();
createjs.Sound.volume = 1;
createjs.Sound.muted = false;



























