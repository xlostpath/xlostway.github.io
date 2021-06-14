---
layout: post
title: "2021年Centos7安装CDH 6.2.1教程"
categories: tutorial
---



### 1.1 虚拟机准备

#### 1.1 设置主机名关闭防火墙

​	克隆三台虚拟机{192.168.1.102(hadoop102)、192.168.1.103(hadoop103)、192.168.1.104(hadoop104)}，基于CentOS7.6，配置好对应主机的网络IP、主机名称、关闭防火墙。

```shell
[root@192.168.1.102 ~]# hostnamectl set-hostname hadoop102 
[root@hadoop102 ~]# chkconfig iptables off
[root@hadoop102 ~]# service iptables stop
[root@hadoop102 ~]# chkconfig --list iptables
iptables        0:关闭  1:关闭  2:关闭  3:关闭  4:关闭  5:关闭  6:关闭
```

**后续操作未特殊说明，另外两台主机做相同设置。**

#### 1.2 关闭SELINUX

```shell
[root@hadoop102 ~]# vim /etc/selinux/config
```

将SELINUX=enforcing改为SELINUX=disabled，执行该命令后重启机器生效。

#### 1.3 主机上修改host

```shell
[root@hadoop002 ~]# vim /etc/hosts
127.0.0.1 localhost  localhost
::1     localhost       localhost.localdomain   localhost6      localhost6.localdomain6
192.168.1.102 hadoop102 hadoop102
192.168.1.103 hadoop103 hadoop103
192.168.1.104 hadoop104 hadoop104
```

#### 1.4 SSH免密登录

​		配置hadoop102对hadoop102、hadoop103、hadoop104三台服务器免密登录。CDH服务开启与关闭是通过server和agent来完成的，所以这里不需要配置SSH免密登录，但是为了我们分发文件方便，在这里我们也配置SSH。
​		1）生成公钥和私钥：

```shell
[root@hadoop102 .ssh]$ ssh-keygen -t rsa
```

​		然后敲（三个回车），就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）

​		2）将公钥拷贝到要免密登录的目标机器上

```shell
[root@hadoop102 .ssh]$ ssh-copy-id hadoop102
[root@hadoop102 .ssh]$ ssh-copy-id hadoop103
[root@hadoop102 .ssh]$ ssh-copy-id hadoop104
```

​		3）重复1和2的操作，配置hadoop103对hadoop102、hadoop103、hadoop104三台服务器免密登录。

#### 1.5 配置NTP时钟同步

1）NTP服务器配置

```shell
[root@hadoop102 ~]# vi /etc/ntp.conf
注释掉所有的restrict开头的配置
修改#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
为restrict 192.168.1.102 mask 255.255.255.0 nomodify notrap
将所有server配置进行注释
添加下面两行内容
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

2）启动NTP服务 service ntpd start

```shell
[root@hadoop102 ~]# service ntpd start
```

3）NTP客户端配置（在agent主机上进行配置hadoop103,hadoop104）

```shell
[root@hadoop103 ~]# vi /etc/ntp.conf

注释所有restrict和server配置
添加server 192.168.1.102
```

4）手动测试

```shell
[root@hadoop103~]# ntpdate 192.168.1.102
```

显示如下内容为成功：

```shell
17 Jun 15:34:38 ntpdate[9247]: step time server 192.168.1.102 offset 77556618.173854 sec
```

如果显示如下内容需要先关闭ntpd：

```shell
17 Jun 15:25:42 ntpdate[8885]: the NTP socket is in use, exiting
```

5）启动ntpd并设置为开机自启（每个节点hadoop102，hadoop103，hadoop104）

```shell
[root@hadoop102 ~]#  chkconfig ntpd on
```

```shell
[root@hadoop102 ~]#  service ntpd start
```



### 1.2 CM安装部署

#### 1.2.1 安装JDK(三台)

1）在hadoop102创建/usr/java目录(注：jdk必须安装在该目录，否则CM会找不到JDK！)

```shell
[root@hadoop102 opt]# mkdir /usr/java
```

2）将jdk-8u144-linux-x64.tar.gz上传至hadoop102，并解压到/usr/java目录下。

```shell
[root@hadoop102 ~]# tar -zxvf jdk-8u144-linux-x64.tar.gz -C /usr/java/
```

3）配置JAVA_HOME环境变量

（1）打开/etc/profile文件

```shell
[root@hadoop102 software]$ vim /etc/profile
```

在profile文件末尾添加JDK路径

```shell
#JAVA_HOME
export JAVA_HOME=/usr/java/jdk1.8.0_144
export PATH=$PATH:$JAVA_HOME/bin
```

（2）让修改后的文件生效

```shell
[root@hadoop102 jdk1.8.0_144]$ source /etc/profile
```

4）测试JDK是否安装成功

```shell
[root@hadoop102 jdk1.8.0_144]# java -version
```

java version "1.8.0_144"

5）在另外两台节点创建好对应目录，将hadoop102中的JDK和环境变量分发到hadoop103、hadoop104两台主机

```shell
[root@hadoop102 java]# scp -r jdk1.8.0_144 hadoop103:$PWD
```

```shell
[root@hadoop102 java]# scp -r jdk1.8.0_144 hadoop103:$PWD
```

分别在hadoop103、hadoop104上source一下

```shell
[root@hadoop103 ~]$ source /etc/profile
```

```shell
[root@hadoop104 ~]# source /etc/profile
```

#### 1.2. 安装MySQL及其驱动（只在hadoop102上操作）

1）卸载mariadb

```
rpm -qa | grep mariadb | xargs rpm -e --nodeps
```

2）下载msql5.7 yum源

wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

3）安装yum源

```
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
```

4）安装mysql

```
yum -y install mysql-server
```

5）启动mysql

```
service mysqld start
```

6）查看root用户密码

```
grep 'temporary password' /var/log/mysqld.log   d9V,K1..6e.Q
```

7）执行mysql初始化脚本

```
mysql_secure_installation
```

8）输入新密码（设为Atguigu.123456或其他，至少12个字符，至少包含一个大写字母有，一个小写字母，一个数字，一个特殊字符）

![img](https://static01.imgkr.com/temp/7f868b4398cb4eb0822a7064ce16ca18.png)

[]: 

9）配置root用户远程访问权限

```
mysql> grant all privileges on *.* to 'root' @'%' identified by 'Atguigu.123456';
```

```
mysql> flush privileges;
```

10）为CM安装mysql驱动

（1）将mysql-connector-java-5.1.27-bin.jar拷贝到/usr/share/java\*路径下，并**重命名**

```
[root@hadoop102 java]# mv mysql-connector-java-5.1.27-bin.jar mysql-connector-java.jar
```

（2）分发驱动到另外两个节点

```
[root@hadoop102 java]# scp mysql-connector-java.jar hadoop103:$PWD
```

```
[root@hadoop102 java]# scp mysql-connector-java.jar hadoop104:$PWD
```

#### 1.2.3 CM离线安装

1）集群规划

| **节点** | **hadoop102**                         | **hadoop103**      | **hadoop104**      |
| -------- | ------------------------------------- | ------------------ | ------------------ |
| **服务** | cloudera-scm-servercloudera-scm-agent | cloudera-scm-agent | cloudera-scm-agent |

4）搭建本地yum源（离线yum安装）

（1）将压缩包cloudera-repos.tar.gz拷贝到集群中的一台节点，解压到/var/www/html路径下

```
[root@hadoop102 ~]# tar -zxvf cloudera-repos.tar.gz -C /var/www/html
```

（2）进入/var/www/html/路径，并开启http服务

```
[root@hadoop102 ~]# cd /var/www/html/
```

```
[root@hadoop102 html]# python -m SimpleHTTPServer 8900
```

（3）浏览器访问该节点的8900端口，查看http服务是否正常开启

![img](https://static01.imgkr.com/temp/df09c0e72c90473cbc18178734d101a2.png) 

（4）编辑本地yum源配置文件

```
vim /etc/yum.repos.d/cloudera-manager.repo
```

文件内容如下

```shell
[cloudera-manager]
name=cloudera-manager
baseurl=http://hadoop102:8900/cloudera-repos/cm6/6.2.1/redhat7/yum/
enabled=1
gpgcheck=0
```

（5）分发该配置文件

```
scp /etc/yum.repos.d/cloudera-manager.repo hadoop103:/etc/yum.repos.d
```

```
scp /etc/yum.repos.d/cloudera-manager.repo hadoop104:/etc/yum.repos.d
```

4）安装CM server及agent

```
[root@hadoop102 ~]# yum -y install cloudera-manager-daemons cloudera-manager-agent cloudera-manager-server
```

```
[root@hadoop103 ~]# yum -y install cloudera-manager-agent cloudera-manager-daemons
```

```
[root@hadoop104 ~]# yum -y install cloudera-manager-agent cloudera-manager-daemons
```

#### 1.2.4 修改CM配置文件（三台，server_host都配置成hadoop102，不要使用scp同步）

```
vim /etc/cloudera-scm-agent/config.ini
```

![image](https://static01.imgkr.com/temp/eab0acda8b2e4217ac59daef8405b6e3.png)

#### 1.25 在MySQL中建库

1）创建各组件需要的数据库

```
mysql> CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

```
mysql> CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

```
mysql> CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

```
mysql> CREATE DATABASE hive DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

```
mysql> CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

```
mysql> CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

2）为CM配置数据库（自带脚本）

```
/opt/cloudera/cm/schema/scm_prepare_database.sh mysql scm root Atguigu.123456
```

#### 1.2.6 启动CM服务

1）启动服务节点：hadoop102

```
[root@hadoop102 ~]# systemctl start cloudera-scm-server
```

2）启动工作节点：hadoop102、hadoop103、hadoop104

```
[root@hadoop102 ~]# systemctl start cloudera-scm-agent
```

```
[root@hadoop103 ~]# systemctl start cloudera-scm-agent
```

```
[root@hadoop104 ~]# systemctl start cloudera-scm-agent
```

3）查看Server启动日志

```
[root@hadoop102 cm]# tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log
```

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps2.jpg) 

等待几分钟，出现Started Jetty server字样及表明启动成功。

4）访问http://hadoop102:7180（初始用户名、密码均为admin）

![img](https://static01.imgkr.com/temp/bfe37bf0f18440a795d5e877fa3b4dab.jpg) 

#### 1.2.7关闭CM服务

1）关闭工作节点：hadoop102、hadoop103、hadoop104

```
[root@hadoop102 ~]# systemctl stop cloudera-scm-agent
```

```
[root@hadoop103 ~]# systemctl stop cloudera-scm-agent
```

```
[root@hadoop104 ~]# systemctl stop cloudera-scm-agent
```

2）关闭服务节点：hadoop102

```
[root@hadoop102 ~]# systemctl stop cloudera-scm-server
```



### 1.3 CDH服务安装

Cloudera Manager提供了十分方便的安装向导，大大简化了CDH的安装和部署。

#### 1.3.1 选择免费版本

1）欢迎页面

![img](https://static01.imgkr.com/temp/b87e370b21854a4fa12dfd8b09e862d3.jpg) 

2）用户协议

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps5.jpg) 

3）选择免费版

![img](https://static01.imgkr.com/temp/84ae9b9fa9fb46baae07c69d228f5ac6.jpg) 

#### 1.3.2 部署CDH集群

1）欢迎页面

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps7.jpg) 

2）集群命名

![img](https://static01.imgkr.com/temp/3320806c078d45c39f42b5ceed1362c1.jpg) 

3）选定集群物理节点

![img](https://static01.imgkr.com/temp/ef485f0f3a584754b2bb7bb037754d4b.jpg) 

4）添加本地parcel库

![img](https://static01.imgkr.com/temp/28328949e9fc4f9bb6a7a9d05a2d20e1.jpg) 

![img](https://static01.imgkr.com/temp/cfbbbe85e0aa49c39a05248b9a4e21cc.jpg) 

![img](https://static01.imgkr.com/temp/f56d08b8ea4a4832a197520ce3e392e7.jpg) 

5）等待parcel的下载、分配、解压和激活

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps13.jpg) 

6）检查集群网络环境

![img](https://static01.imgkr.com/temp/26a9a3b727494d69af56082bab49e9d4.jpg) 

7）选择要安装的CDH组件，选择自定义安装

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps15.jpg) 

8）选择需要安装的组件，如下

![img](https://static01.imgkr.com/temp/ae5453ef99ad484a8190cd1aa5de9cad.jpg) 

9）CDH各组件角色分布

![img](https://static01.imgkr.com/temp/b61439e0a66e4fca904eac0836db7a2d.jpg) 

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps18.jpg) 

10）数据库连接测试

![img](https://static01.imgkr.com/temp/eed2540a9eab4fafb2dc4d3d858ce13f.jpg) 

11）各组件基本设置，使用默认即可

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps20.jpg) 

12）等待安装部署和启动

![img](https://static01.imgkr.com/temp/04d87e9fc644433b8818a8f92c5525d2.jpg) 

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps22.jpg) 

![img](https://static01.imgkr.com/temp/20ed477b288c454fbe058ff9d0da8227.jpg) 

13）启动成功

![img](file:///C:\Users\xiaohu\AppData\Local\Temp\ksohtml13428\wps24.jpg) 



### 1.4 问题及解决

- 出现“start request repeated too quickly for cloudera-scm-server.service”错误

解决办法：CDH默认从/usr/java目录下找JAVA_HOME，jdk必须放在该目录下



- 连接HIVE的元数据库MYSQL时出现“Unexpected error.Unable to verify database connection”错误

解决办法：应该是mysql连接驱动没找到，在三台节点上分别将mysql-connector-java-5.1.27-bin.jar拷贝到/usr/share/java路径下，并重命名为mysql-connector-java.jar