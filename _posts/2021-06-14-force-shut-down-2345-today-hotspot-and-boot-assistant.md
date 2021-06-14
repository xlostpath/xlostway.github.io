---
layout: post
title: "强制关闭 2345今日热点和开机助手"
categories: tutorial
---

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.talklee.com](https://www.talklee.com/blog/531.html)

​		最近电脑开机 2345 开机助手显示那么一瞬间，没等你手动设置就关闭了，然后电脑安装的腾讯管家还找不到这个选项，使用 win10 自带系统的开机启动项也找不到，我电脑只是安装了 2345 好压和看图王，并没有安装 2345 安全卫士，所以不能去关闭开机助手，2345 这就有点恶心了，而且这个开机提示很鸡贼的，我为了要设置关闭他，重新启动和重新开关机都没再出现，不知道后台是怎么设置了，一天出来一次么？这个办法行不通就找其他方法吧，这里您可能会有疑问，为什么怀疑是 2345，因为我电脑没有安装 3 某 0 的软件，除了浏览器，所以对于流氓捆绑来说，就只剩下 2345 了。

​		首先排除安装了 2345 安全卫士的小伙伴们，在那可以直接设置关闭。今天要聊的是没有安装 2345 安全卫士，但还是有开机提示怎么强制的关闭和删除。

​		[百度](https://www.talklee.com/tags-1.html)[教程](https://www.talklee.com/tags-27.html)但是看图王并没有的那个文件 “**2345PicMiniPage.exe**” 这个软件，但是我在好压文件包找到了 “**HaozipAssistant.exe**”（开机助手）和 “**HaozipMiniPage.exe**”（今日热点）两个执行文件，路径 “**Program Files\2345Soft\HaoZip\Protect**”，估计就是这俩了，然后把这俩文件剪切出来，**新建 txt 文件**，将文本**重新命名 “HaozipAssistant.exe” 和“HaozipMiniPage.exe”** 然后**设置为只读模式**，不给权限。然后把剪切的两个**源文件删除**，替换之后的样子，如图，都是 **0 字节**，因为什么内容都没有哈。

[![](https://www.talklee.com/zb_users/upload/2020/07/202007091594257259260475.png)](https://www.talklee.com/zb_users/upload/2020/07/202007091594257259260475.png)