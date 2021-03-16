---
layout: post
title:  "google fonts 国内使用地址替换方案"
categories: tutorial
---

因为 fonts.googleapis.com 在国外，加载过程会很慢。  
因此，看到网上的很多解决方案都是选择 360 的 fonts.useso.com 镜像服务来做加载。  
殊不知，Google 字体已经有中国站了：[http://googlefonts.cn/](http://googlefonts.cn/)

但是引用地址不是 googlefonts.cn，所以很容易错把网站地址当作引用地址而出现错误。  
其引用地址是 https://fonts.font.im  
![](https://img-blog.csdnimg.cn/20210203112438303.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FsbHl3YXRlcg==,size_16,color_FFFFFF,t_70#pic_center)  
![](https://img-blog.csdnimg.cn/20210203112450512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FsbHl3YXRlcg==,size_16,color_FFFFFF,t_70#pic_center)

所以，只要把 fonts.googleapis.com 修改为 fonts.font.im，就可以实现快速的加载。

也可以通过 @import 方式，或者进行进一步定制。

总之，很方便！

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/allywater/article/details/113516276)