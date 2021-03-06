---
title: js卷积矩阵滤镜
date: 2016-11-03 13:10:39
tags: [filter, javascript]
---

[github地址](https://github.com/chocho-1115/filter)


学习参考
[图像处理－线性滤波－1 基础（相关算子、卷积算子、边缘效应）](http://www.cnblogs.com/wuyuankun/p/4149499.html)
[很多看不懂的东西](http://blog.sina.com.cn/s/articlelist_1272649483_0_1.html)
[高斯模糊的算法（高斯卷积 高斯核）](http://www.cnblogs.com/zxj015/archive/2013/05/12/3074612.html)
[彻底理解数字图像处理中的卷积](http://www.cnblogs.com/freeblues/p/5738987.html)
[图像卷积与滤波的一些知识点](http://blog.csdn.net/zouxy09/article/details/49080029)




闲来无事，写了一个简单的卷积矩阵滤镜，再次做下记录。

卷积 ： 大矩阵和小矩阵对应位置元素相乘再求和的操作就叫卷积；

卷积矩阵滤镜需要我们把待处理的平面数字图像看做一个大的矩阵, 图像的每个像素对应着矩阵的每个元素, 假设我们平面的分辨率是 1024*768, 那么对应的大矩阵的行数= 1024, 列数=768。
然后还需要有一个卷积核，卷积核也是一个小矩阵，一般是个方阵, 也就是行数和列数相同。
整个运算过程就是对于图像矩阵中的每个像素, 计算它周围像素和卷积核对应位置元素的乘积, 然后把结果相加到一起, 最终得到的值就作为该像素的新值, 这样的一个过程也称作一次线性滤波过程.

上面的处理过程可以参考网络上的这个示意图:
{% img cont_img /img/2016/2.png title %}

关于卷积核，也有一定的规则要求：

- 滤波器的大小应该是奇数，这样它才有一个中心，例如3x3，5x5或者7x7。有中心了，也有了半径的称呼，例如5x5大小的核的半径就是2。卷积核半径越大，计算量越大。一般的图像处理，半径在1或2就可以满足需要了

- 滤波器矩阵所有的元素之和应该要等于1，这是为了保证滤波前后图像的亮度保持不变。当然了，这不是硬性要求了。如果滤波器矩阵所有元素之和大于1，那么滤波后的图像就会比原图像更亮，反之，如果小于1，那么得到的图像就会变暗。如果和为0，图像不会变黑，但也会非常暗。



对于图像处理来说每一个像素对象的是4个不同的通道（红，绿，蓝，alpha），所以在做卷积运算的时候，每一次的运算都是单通道的卷积运算（一般不包括alpha通道），也就是说在卷积核移到了某一个像素位置上的时候需要进行三次卷积运算。所以最后我们就需要4个for循环的嵌套：

``` javascript
/*
r 卷积半径
m 卷积核
w,h imageData的宽高
offset 偏移
divisor 阈值


*/
var mLen = m.length;
for (var y = r; y < h-r; y += 1) {//图像行
    for (var x = r; x < w-r; x += 1) {//图像列
        for (var c = 0; c < 3; c += 1) {//rgb
            var i = (y*w + x)*4 + c;//imageData对象的下标
            var v = 0;
            for(var k = 0;k < mLen;k++){//卷积核
                v += m[k]*iD[i-w*4*(Math.floor(k/mW)-r)+4*(k%mW-r)]  ///Math.floor(k/mW) 第几行  k%mW 第几列
            }
            oD[i] = (offset||0)+v/(divisor||1); ///设置偏移与阈值
        }
        oD[(y*w + x)*4 + 3] = 255; // 设置透明度
    }
}
```
很明显，卷积核的半径越大，最后的计算量也会越大。对于一般的图像处理来说，卷积核的半径在 1 或者 2即可满足大部分的需求。所以为了提高性能，可以单独考虑半径为1或者2的情况：

``` javascript
switch(r){
    case 1:
        for (var y = 1; y < h-1; y += 1) {//行
            for (var x = 1; x < w-1; x += 1) {//列
                for (var c = 0; c < 3; c += 1) {//rgb
                    var i = (y*w + x)*4 + c;
                    oD[i] = (offset||0)
                        +(m[0]*iD[i-w*4-4] + m[1]*iD[i-w*4] + m[2]*iD[i-w*4+4]
                        + m[3]*iD[i-4]     + m[4]*iD[i]     + m[5]*iD[i+4]
                        + m[6]*iD[i+w*4-4] + m[7]*iD[i+w*4] + m[8]*iD[i+w*4+4])
                        / (divisor||1);
                }
                oD[(y*w + x)*4 + 3] = 255; // 设置透明度
            }
        }
    break;
    case 2:
        for (var y = 2; y < h-2; y += 1) {//行
            for (var x = 2; x < w-2; x += 1) {//列
                for (var c = 0; c < 3; c += 1) {//rgb
                    var i = (y*w + x)*4 + c;
                    oD[i] = (offset||0)
                        +(m[0 ]*iD[i-w*8-8] + m[1 ]*iD[i-w*8-4] + m[2 ]*iD[i-w*8] + m[3 ]*iD[i-w*8+4] + m[4 ]*iD[i-w*8+8]
                        + m[5 ]*iD[i-w*4-8] + m[6 ]*iD[i-w*4-4] + m[7 ]*iD[i-w*4] + m[8 ]*iD[i-w*4+4] + m[9 ]*iD[i-w*4+8]
                        + m[10]*iD[i-8]     + m[11]*iD[i-4]     + m[12]*iD[i]     + m[13]*iD[i+4]     + m[14]*iD[i+8]
                        + m[15]*iD[i+w*4-8] + m[16]*iD[i+w*4-4] + m[17]*iD[i+w*4] + m[18]*iD[i+w*4+4] + m[19]*iD[i+w*4+8]
                        + m[20]*iD[i+w*8-8] + m[21]*iD[i+w*8-4] + m[22]*iD[i+w*8] + m[23]*iD[i+w*8+4] + m[24]*iD[i+w*8+8])
                        / (divisor||1);
                }
                oD[(y*w + x)*4 + 3] = 255; // 设置透明度
            }
        }
    break;

    default:
        var mLen = m.length;
        for (var y = r; y < h-r; y += 1) {//图像行
            for (var x = r; x < w-r; x += 1) {//图像列
                for (var c = 0; c < 3; c += 1) {//rgb
                    var i = (y*w + x)*4 + c;//imageData对象的下标
                    var v = 0;
                    for(var k = 0;k < mLen;k++){//卷积核
                        v += m[k]*iD[i-w*4*(Math.floor(k/mW)-r)+4*(k%mW-r)]  ///Math.floor(k/mW) 第几行  k%mW 第几列
                    }
                    oD[i] = (offset||0)+v/(divisor||1); ///设置偏移与阈值
                }
                oD[(y*w + x)*4 + 3] = 255; // 设置透明度
            }
        }

}

```
### 图片边界处理

通过上面的图或者计算方式，我们很容易发现最后得出来的图像的四周是没有做任何处理的。当卷积核的半径越大，周围就有更多的数据没做处理，如下图：

{% img cont_img /img/2016/卷积矩阵图.gif title %}

这是因为把卷积核的中心移到图像边界时，图像边界外没有像素数据来做卷积运算，所以我们要填充这些没有的像素数据。

这篇文章最后有介绍几种边界处理的方法：[here](http://blog.csdn.net/zouxy09/article/details/49080029)。我们也可以把图片的周围作裁剪处理，不过我认为这样的办法不是很合适，特别是卷积半径过大时，裁切后丢失的数据就会更多，虽然一般卷积半径不会很大。

我这里直接对缺失的数据用卷积核中心对应的像素数据来填充的，这样不用对每一个缺失的数据都去找他的填充数据。我们只要判断这个区域是否需要填充即可。

我们以卷积核半径为2，图像width，height为10像素来举例：



如上图，我们先遍历横向绿色区域（包括四个角），再去遍历垂直蓝色区域。

图像看作一个大的矩阵，每一个格子都有它的一个坐标。以图中的橙色格子为例，它的坐标可以看作是x（列）为3，y（行）为4的格子（这里是从0行0列开始算起的，方便后面计算）。

卷积核可以看作是卷积中心为（0，0）坐标的平面坐标。

这样我们就可以通过图像坐标和卷积核坐标的和，来判断所要取的像素信息是否在图像矩阵外面了。以图像坐标为（1，0）的格子为例，把卷积核中每个格子的坐标分别与（1，0）相加就会得到要取的25个像素信息在图像中的坐标位置，如果这个坐标在图像之外就可以用卷积核中心对应的像素数据来填充了。








