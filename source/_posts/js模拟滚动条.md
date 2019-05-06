---
title: js模拟滚动条
date: 2016-09-08 12:59:30 
tags: javascript
---
---
不要问我为什么不用css来实现，说多了都是泪。。。一句话就是：项目需要。。。

 "Source Code Pro", Consolas, Monaco, Menlo, Consolas, monospace

### 说明：
 * 基于原生js，不依赖第三方库
 * 兼容性：Trident(ie5+,包括混杂模式)、 Gecko(Firefox3.6.28+)、  Presto(oprea9.0+)、 Webkit、 Safari
 * 支持滚动条多层嵌套
 * 支持内容变化时，更新滚动条状态

### 新版说明：
 * 在内容不足时 隐藏滚动条
 * 添加了水平滚动条的功能
 * con nr scon sbox 都可以添加border
 * 修复了 滚动区域内容不足时 鼠标滚轮不能触发外层滚动条的滚动事件
 * 修复了 在子窗口滚动到端点时  无法触发父窗口的滚动事件
 * 支持滚动条滚动区域的点击触发滚动事件

### 不足：
 * 内容不足时 没有注销相关事件
 * 滚动没有缓动效果
 * 不支持触屏事件

### 调用方式
``` html
<div id="scroll_client_01" class="scroll_client scroll_y">
    <div id="scroll_nr_01" class="scroll_nr">
            <!--内容-->
    </div>

    <div id="scroll_con_01" class="scroll_con">
        <div id="scroll_box_01" class="scroll_box"></div>
    </div>
</div>
<style>
/* ----------------------- */
.scroll_client{position:relative;overflow:hidden;background:#fff;border:1px solid #ccc;padding:0 !important;}
.scroll_nr{position:absolute;left:0;top:0;}

.scroll_con{background:#ccc;position:absolute;padding:0 !important;}
.scroll_box{background:#999;position:absolute;left:0;top:0;border:1px solid #222;padding:0 !important;}

.scroll_x .scroll_nr{overflow:hidden;}
.scroll_x .scroll_con{height:10px;width:100%;bottom:0;left:0;right:auto;top:auto;}
.scroll_x .scroll_box{height:8px;}

.scroll_y .scroll_nr{}
.scroll_y .scroll_con{height:100%;width:10px;right:0;top:0;left:auto;bottom:auto;}
.scroll_y .scroll_box{width:8px;}

/* ----------------------- */
#scroll_client_01{width:500px;height:500px;}
#scroll_client_01 .scroll_nr{min-height:484px;padding:8px 18px 8px 8px;}

</style>
<script>
//调用
    var S1=new WinScroll({
                con  : 'scroll_client_01',
                nr   : 'scroll_nr_01',
                scon : 'scroll_con_01',
                sbox : 'scroll_box_01',
                sboxMinWH : 150
    });

    //内容跳到-100的位置 调用refreshWin会根据内容重新计算滚动条的高度 并跳到指定的位置
    //当参数为false的时候 保持原来位置不变  当参数为true的时候 会刷新窗口 内容置顶
    S1.refreshWin(-100);
</script>
```

[demo](http://www.indent.top/demo/scroll/scroll.html)

### 扩展补充：
[自定义浏览器滚动条的样式，打造属于你的滚动条风格](http://www.lyblog.net/detail/314.html)
[基于jquery的mCustomScrollbar](http://manos.malihu.gr/jquery-custom-content-scroller/)
