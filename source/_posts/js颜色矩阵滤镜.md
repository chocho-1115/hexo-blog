---
title: js颜色矩阵滤镜
date: 2016-08-01 23:10:33 
tags: [filter, javascript]
---

[github地址](https://github.com/chocho-1115/filter)

---
本文主要介绍 colorMatrixFilter.js
 * filter.js //主文件
 * preloadImage.js //图片加载处理
 * colorMatrixFilter.js //颜色矩阵滤镜
 * convolutionMatrixFilter.js //卷积矩阵滤镜
---
# colorMatrixFilter.js
## 颜色矩阵滤镜原理介绍

学习参考
[as 颜色矩阵滤镜使用示例](http://www.emanueleferonato.com/2009/04/28/understanding-as3-colormatrixfilter-class/)
[as 颜色矩阵滤镜API](http://help.adobe.com/zh_CN/FlashPlatform/reference/actionscript/3/flash/filters/ColorMatrixFilter.html)
[blog 里面有很多示例](http://blog.gskinner.com/)
[矩阵乘法的原理介绍](http://www.ruanyifeng.com/blog/2015/09/matrix-multiplication.html)
[CSDN博客-颜色空间总结 RGB、HSV、YUV](http://m.blog.csdn.net/article/details?id=48317339)
[百度百科-色彩空间](http://baike.baidu.com/link?url=DxeUqw7B5JwKGzQETc2OQi0H3bAM1CxaiAHKr_g63wmWGhe-0xYnHClI_F9S4lRyztHsdifXeRax7y1KMLspS_)
[知乎-色彩空间与色彩模型的本质区别是什么](https://www.zhihu.com/question/38303244/answer/77581900)


用一句话介绍就是：**把位图中的每一个颜色值与一个4行5列的多维矩阵(20个元素的数组)相乘，得出新图像的颜色数据。**即公式如下：

```perl
RGBA = [ R, G, B, A ]
  
matrix =[ 1, 0, 0, 0, 0,
          0, 1, 0, 0, 0,
          0, 0, 1, 0, 0,
          0, 0, 0, 1, 0 ]
  
新的颜色信息 = RGBA * matrix = [
                matrix[0]*R + matrix[1]*G + matrix[2]*B + matrix[3]*A + matrix[4] // R通道
                matrix[5]*R + matrix[6]*G + matrix[7]*B + matrix[8]*A + matrix[9] // G通道
                matrix[10]*R + matrix[11]*G + matrix[12]*B + matrix[13]*A + matrix[14] // B通道
                matrix[15]*R + matrix[16]*G + matrix[17]*B + matrix[18]*A + matrix[19] // A通道
             ]
``` 
举例说明：如果想要一个值为 rgba(200, 100, 50, 1 ) 的颜色，只保留红色通道的色彩。我们只需要调整 matrix 并做如下运算：

```perl
[ 200, 100, 50, 1 ] * [ 1, 0, 0, 0, 0,
                        0, 0, 0, 0, 0,
                        0, 0, 0, 0, 0,
                        0, 0, 0, 1, 0 ] = [
                            1*200 + 0*100 + 0*50 + 0*1 + 0,
                            0*200 + 0*100 + 0*50 + 0*1 +0,
                            0*200 + 0*100 + 0*50 + 0*1 +0,
                            0*200 + 0*100 + 0*50 + 1*1 +0,
                        ] = [ 200, 0, 0, 1 ]
``` 
如果我们想对颜色应用多个滤镜，比如我们想要在得到红色通道的基础上，再把颜色亮度 +10，那么我们可以这样：
```perl
[ 200, 100, 50, 1 ] * [ 1, 0, 0, 0, 0,
                        0, 0, 0, 0, 0,
                        0, 0, 0, 0, 0,
                        0, 0, 0, 1, 0 ]
                    * [ 1, 0, 0, 0, 10,
                        0, 1, 0, 0, 10,
                        0, 0, 1, 0, 10,
                        0, 0, 0, 1, 0 ]
``` 
这里有个小技巧，在实际应用中，图片的颜色信息肯定要多很多。在一张100px×100px大小的图片上就有10000个像素点。在每个像素点跟矩阵做乘法运算的时候，这个运算量是很大。上面的计算是从左至右运算的，我们需要遍历图片所有的像素点至少两次，也就是说一共要计算2×10000次。还好矩阵乘法是适用结合律的，也就是说我们可以这样：

```perl
[ 200, 100, 50, 1 ] * ([ 1, 0, 0, 0, 0,
                         0, 0, 0, 0, 0,
                         0, 0, 0, 0, 0,
                         0, 0, 0, 1, 0 ]
                     * [ 1, 0, 0, 0, 10,
                         0, 1, 0, 0, 10,
                         0, 0, 1, 0, 10,
                         0, 0, 0, 1, 0 ])
``` 
先算后面的乘积，再把他去跟图片信息相乘，这样运算效率会提高很多。关于多维矩阵之间的乘法可以参考上面提到到的[矩阵乘法的原理介绍](http://www.ruanyifeng.com/blog/2015/09/matrix-multiplication.html)。


## 创建colorMatrixFilter对象

假如我们想要获取图片的红色通道：

```javascript
    var F = new FILTER.Create(imgEle);
    var cmf = new FILTER.ColorMatrixFilter();
    cmf.redChannel();
    cmf.apply(F.imgData);
``` 
FILTER.ColorMatrixFilter 的实例 cmf 带有一个 matrix 的属性，这里保存了4行5列的多维矩阵(20个元素的数组)。
matrix 的属性的默认值为：
        [ 1, 0, 0, 0, 0,
          0, 1, 0, 0, 0,
          0, 0, 1, 0, 0,
          0, 0, 0, 1, 0 ]
然后通过 redChannel() 方法来得到一个红色通道的矩阵滤镜，此时 matrix 的属性值变为：
        [ 1, 0, 0, 0, 0,
          0, 0, 0, 0, 0,
          0, 0, 0, 0, 0,
          0, 0, 0, 1, 0 ]
最后通过 cmf.apply(F.imgData) 把滤镜应用到 imgData 上。

另外在实例化ColorMatrixFilter时,可以指定matrix的值：
```javascript
    var matrix = [];
    matrix=matrix.concat([1, 0, 0, 0, 0]);// red
    matrix=matrix.concat([0, 0, 0, 0, 0]);// green
    matrix=matrix.concat([0, 0, 0, 0, 0]);// blue
    matrix=matrix.concat([0, 0, 0, 1, 0]);// alpha
   
    var F = new FILTER.Create(imgEle);
    var cmf = new FILTER.ColorMatrixFilter(matrix);
    cmf.apply(F.imgData);
``` 
以上代码的cmf同样是一个红色通道的矩阵滤镜

---
## colorMatrixFilter methods
``` javascript
  
cmf.add(matrix) //添加滤镜 其实是在已有滤镜的基础上累加 即参数matrix与cmf.matrix做乘法运算
cmf.reset() //复位滤镜 将matrix的值设置为创建cmf对象时的值
cmf.clear() //清除滤镜
cmf.clone() //克隆cmf对象，返回新的ColorMatrixFilter对象
cmf.apply(imgData)//将滤镜应用到imgData
cmf.CMconcat(mat1,mat2)//两个矩阵的乘积
  
//获取红色通道 asGray是否去色
cmf.redChannel(asGray);
   
//通道调整 绿色通道乘以1.5 并便宜10
cmf.reviseGreen(1.5,10);
  
//desaturate 去色
cmf.desaturate();
  
//average 加权平均
cmf.average(1,0,0);
  
//brightness 明度 红色通道偏移80 绿色通道偏移50 蓝色通道便宜80（默认等于第一个参数）
cmf.brightness(80,50);
  
//colorize 着色 0xff9900 透明度为.5
cmf.colorize(0xff9900,.5);
  
//invert 反向
cmf.invert();
cmf.invertRed();
cmf.invertGreen();
cmf.invertBlue();
cmf.invertAlpha();
  
//saturate 饱和度
cmf.saturate(0.2);
  
//contrast 对比度
cmf.contrast(2);
  
//快速校正对比度
cmf.quickContrastCorrection();
  
//adjustHue 0-360色环调整
cmf.adjustHue(180);
  
//褐色调旧照片。
cmf.sepia(1);
  
//红褐色调旧照片。
cmf.sepia2(1);
  
//threshold 灰度阈值
cmf.threshold(125,66);
  
//threshold_rgb 彩色阈值
cmf.threshold_rgb(125,66);
  
//RGB转YCbCr
cmf.RGB2YCbCr();
  
//YCbCr转RGB
cmf.YCbCr2RGB();
  
//swapChannels  交换通道 红绿通道的交换
cmf.swapChannels(0,1);
  
``` 
其中的 RGB2YCbCr 与 YCbCr2RGB 是色彩空间YUV和RGB的相互转换。

最后来看一下效果:
{% img cont_img /img/2016/3.jpg?v=1 title %}




