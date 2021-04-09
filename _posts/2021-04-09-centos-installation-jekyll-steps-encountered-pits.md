---
layout: post
title: "centos 7.5 安装Jekyll运行环境步骤及遇到的坑"
categories: tutorial
---

#### 安装ruby

安装reby及其它组件(默认安装版本太低将会遇到坑)：

```shell
yum -y install ruby ruby-devel rubygems rpm-build
```

默认安装的版本是2.0.0，需要安装2.4的版本，否则安装jekyll将报错

先安装ruby 2.4的yum源：

```shell
yum install centos-release-scl-rh　
```

按装好源后就可以安装ruby2.4：

```shell
yum install rh-ruby24 -y
```

临时切换ruby默认版本为2.4

```shell
scl enable rh-ruby24 bash
```

安装2.4版本的ruby-devel：

下载[rh-ruby24-ruby-devel-2.4.6-92.el7.x86_64.rpm](http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/r/rh-ruby24-ruby-devel-2.4.6-92.el7.x86_64.rpm)并安装：

```
rpm -iv rh-ruby24-ruby-devel-2.4.6-92.el7.x86_64.rpm
```



#### 安装Jekyll和bundler gems

```shell
gem install jekyll bundler
```

在 当前 目录下创建一个全新的 Jekyll 网站：  

```shell
jekyll new myblog
```

允许任意IP访问服务器的4000端口：

```shell
jekyll serve --force_polling -H 0.0.0.0 -P 4000
```



##### 遇到的问题

安装Jekyll各种报错，主要是ruby和ruby-dev安装的版本太低

##### 解决办法：

安装较新版本，ruby可以通过改变源来安装，ruby-dev目前只知道下载[rh-ruby24-ruby-devel-2.4.6-92.el7.x86_64.rpm](http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/r/rh-ruby24-ruby-devel-2.4.6-92.el7.x86_64.rpm)包离线安装