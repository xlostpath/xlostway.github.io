---
layout: post
title: "Airflow系列教程-Airflow在CentOS6.8中安装部署"
category: tutorial
---


> 本文由转载并部分修改， 原文地址 [tech.cuixiangbin.com](https://tech.cuixiangbin.com/?p=1380)

1. 安装前准备工作
----------

安装版本说明

<table><thead><tr><th>安装工具</th><th>版本</th><th>用途</th></tr></thead><tbody><tr><td>Python</td><td>3.6.5</td><td>安装 airflow 及其依赖包、开发 airflow 的 dag 使用</td></tr><tr><td>MySQL</td><td>5.7</td><td>作为 airflow 的元数据库</td></tr><tr><td>Airflow</td><td>1.10.10</td><td>任务调度平台</td></tr></tbody></table>

2. 安装 Python3
-------------

```shell
#python依赖
yum -y install zlib zlib-devel
yum -y install bzip2 bzip2-devel
yum -y install ncurses ncurses-devel
yum -y install readline readline-devel
yum -y install openssl openssl-devel
yum -y install openssl-static
yum -y install xz lzma xz-devel
yum -y install sqlite sqlite-devel
yum -y install gdbm gdbm-devel
yum -y install tk tk-devel
yum install gcc

#安装wget命令
yum -y install wget
#使用wget下载Python源码压缩包到/root目录下
wget -P /root https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz
#在当前目录解压Python源码压缩包
tar -zxvf Python-3.6.5.tgz
#进入解压后的文件目录下
cd /root/Python-3.6.5
#检测及校验平台
./configure --with-ssl --prefix=/service/python3
#编译Python源代码
make
#安装Python
make install
#备份原来的Python软连接
mv /usr/bin/python /usr/bin/python2.backup
#制作新的指向Python3的软连接
ln -s /service/python3/bin/python3 /usr/bin/python
#建立pip的软连接
ln -s /service/python3/bin/pip3 /usr/bin/pip
#查看Python版本
python -V
#检测pip是否可用
pip
#升级pip
pip install --upgrade pip
#获取yum命令所在位置
whereis yum
#yum: /usr/bin/yum /etc/yum /etc/yum.conf /usr/share/man/man8/yum.8
#编辑yum文件
vi /usr/bin/yum /etc/yum /etc/yum.conf /usr/share/man/man8/yum.8
#进入编辑模式
i
#修改第一行内容（看系统版本，centos7对应2.7，centos6对应2.6）
#修改前：
#!/usr/bin/python
#修改后：
#!/usr/bin/python2.7
#退出编辑模式
esc
#保存文件
：wq
#按上述方式编辑以下文件，修改第一行内容
/usr/libexec/urlgrabber-ext-down
```

3. 安装 MySQL
-----------

```shell
#卸载mariadb
rpm -qa | grep mariadb
rpm -e --nodeps mariadb-libs-5.5.52-1.el7.x86_64
#sudo rpm -e --nodeps mariadb-libs-5.5.52-1.el7.x86_64
rpm -qa | grep mariadb
```

```shell
#下载mysql的repo源
wget https://dev.mysql.com/get/mysql57-community-release-el6-9.noarch.rpm
#通过rpm安装
rpm -Uvh mysql57-community-release-el6-9.noarch.rpm 
或
yum localinstall -y mysql57-community-release-el6-9.noarch.rpm
#安装mysql
yum install mysql-community-server
#开启Mysql服务
service mysqld start
#mysql安装成功后创建的超级用户'root'@'localhost'的密码会被存储在/var/log/mysqld.log，可以使用如下命令查看密码
grep 'temporary password' /var/log/mysqld.log
#使用mysql生成的'root'@'localhost'用户和密码登录数据库
mysql -uroot -p
#重置mysql密码
use mysql;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyPassWord?123';
flush privileges;
#为Airflow建库、建用户
#建库:
create database airflow;
#建用户:
create user 'airflow'@'%' identified by 'airflow';
create user 'airflow'@'localhost' identified by 'airflow';
#为用户授权:
grant all on airflow.* to 'airflow'@'%';
grant all on airflow.* to 'root'@'%';
flush privileges;
exit;
```

4. 安装 Airflow
-------------

```shell
#设置临时环境变量
export SLUGIFY_USES_TEXT_UNIDECODE=yes
#添加编辑环境变量
vi /etc/profile
#在最后添加以下内容：
----》
export PS1="[\u@\h \w]\$ "
#Python环境变量
export PYTHON_HOME=/service/python3
export PATH=$PATH:$PYTHON_HOME/bin
#Airflow环境变量
export AIRFLOW_HOME=/root/airflow
export SITE_AIRFLOW_HOME=/service/python3/lib/python3.6/site-packages/airflow
export PATH=$PATH:$SITE_AIRFLOW_HOME/bin
----》
#生效环境变量
source /etc/profile
#安装apache-airflow并且指定1.10.0版本
pip install apache-airflow===1.10.10

```

airflow 会被安装到 python3 下的 site-packages 目录下，完整目录为：

```shell
${PYTHON_HOME}/lib/python3.6/site-packages/airflow
#绝对路径/service/python3/lib/python3.6/site-packages/airflow
```

执行`airflow`命令做初始化操作

```shell
airflow
####
[2019-07-17 04:40:01,565] {__init__.py:51} INFO - Using executor SequentialExecutor
usage: airflow [-h]
               {backfill,list_tasks,clear,pause,unpause,trigger_dag,delete_dag,pool,variables,kerberos,render,run,initdb,list_dags,dag_state,task_failed_deps,task_state,serve_logs,test,webserver,resetdb,upgradedb,scheduler,worker,flower,version,connections,create_user}
               ...
airflow: error: the following arguments are required: subcommand
####
#到此，airflow会在刚刚的AIRFLOW_HOME目录下生成一些文件。当然，执行该命令时可能会报一些错误，可以不用理会！
#报错如下：
[2019-07-17 04:40:01,565] {__init__.py:51} INFO - Using executor SequentialExecutor
usage: airflow [-h]
               {backfill,list_tasks,clear,pause,unpause,trigger_dag,delete_dag,pool,variables,kerberos,render,run,initdb,list_dags,dag_state,task_failed_deps,task_state,serve_logs,test,webserver,resetdb,upgradedb,scheduler,worker,flower,version,connections,create_user}
               ...
airflow: error: the following arguments are required: subcommand
#生成的文件logs如下所示：
[root@test01 ~]$ cd airflow/
[root@test01 ~/airflow]$ ll
total 28
-rw-r--r--. 1 root root 20572 Jul 17 04:40 airflow.cfg
drwxr-xr-x. 3 root root    23 Jul 17 04:40 logs
-rw-r--r--. 1 root root  2299 Jul 17 04:40 unittests.cfg

```

```shell
#为airflow安装mysql模块
pip install 'apache-airflow[mysql]'
#出现报错：
    ERROR: Complete output from command python setup.py egg_info:
    ERROR: /bin/sh: mysql_config: command not found
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-install-dq81ujxt/mysqlclient/setup.py", line 16, in <module>
        metadata, options = get_config()
      File "/tmp/pip-install-dq81ujxt/mysqlclient/setup_posix.py", line 51, in get_config
        libs = mysql_config("libs")
      File "/tmp/pip-install-dq81ujxt/mysqlclient/setup_posix.py", line 29, in mysql_config
        raise EnvironmentError("%s not found" % (_mysql_config_path,))
    OSError: mysql_config not found
    ----------------------------------------
ERROR: Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-install-dq81ujxt/mysqlclient/
#解决方案,查看是否有mysql_config文件
[root@test01 ~]$ find / -name mysql_config
#如果没有
[root@test01 ~]$ yum -y install mysql-devel
#安装完成后再次验证是否有mysql_config
find / -name mysql_config
#采用mysql作为airflow的元数据库
pip install mysqlclient
#安装MySQLdb
pip install MySQLdb
#报错不支持
Collecting MySQLdb
  ERROR: Could not find a version that satisfies the requirement MySQLdb (from versions: none)
ERROR: No matching distribution found for MySQLdb
#所以使用python-mysql
pip install pymysql
pip install cryptography
#避免之后产生错误
#airflow.exceptions.AirflowException: Could not create Fernet object: Incorrect padding
#需要修改airflow.cfg (默认位于~/airflow/)里的fernet_key
#修改方法
python -c "from cryptography.fernet import Fernet; 
print(Fernet.generate_key().decode())"
#这个命令生成一个key，复制这个key然后替换airflow.cfg文件里的fernet_key的值，如下图
#可能出现报错
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ModuleNotFoundError: No module named 'cryptography'
#处理方式：
pip install cryptography
#文件中进行fernet_key值修改
cd  ${AIRFLOW_HOME}
vi airflow.cfg
#查找fernet_net
/fernet_net
#编辑替换fernet值
```

[![][img-0]



```shell
#修改airflow.cfg文件中的sql_alchemy_conn配置
sql_alchemy_conn = mysql+mysqldb://airflow:airflow@localhost:3306/airflow
#保存文件
#为避免初始化数据库时有如下报错
#Global variable explicit_defaults_for_timestamp needs to be on (1) for mysql
#修改MySQL配置文件my.cnf
#查找my.cnf位置
mysql --help | grep my.cnf

```

```shell
#修改my.cnf,注意如果使用centos默认安装的mysql-5.1不支持改参数，会导致重启失败，5.6.6之后的版本才支持
vi /etc/my.cnf
#在[mysqld]下面（一定不要写错地方）添加如下配置:
explicit_defaults_for_timestamp=true

```

```shell
#重启mysql服务使配置生效
service mysqld restart
#检查配置是否生效
mysql -uroot -proot
mysql> select @@global.explicit_defaults_for_timestamp;
+------------------------------------------+
| @@global.explicit_defaults_for_timestamp |
+------------------------------------------+
|                                        1 |
+------------------------------------------+
1 row in set (0.00 sec)

```

### Ⅰ. 通过修改 airflow.cfg 调整配置

#### 1 修改 webserver 地址

```shell
base_url = http://192.168.50.13:8085
web_server_port = 8085
```

#### 2 修改 executor

```shell
#SequentialExecutor是单进程顺序执行任务，默认执行器，通常只用于测试
#LocalExecutor是多进程本地执行任务使用的
#CeleryExecutor是分布式调度使用（当然也可以单机），生产环境常用
#DaskExecutor则用于动态任务调度，常用于数据分析
executor = CeleryExecutor
```

#### 

### Ⅱ. 初始化源数据库及启动组件

```shell
#初始化元数据库信息（其实也就是新建airflow依赖的表）
pip install celery
pip install apache-airflow['kubernetes']
airflow initdb 
#或者使用airflow resetdb
```

```shell
#准备操作
#关闭linux防火墙
systemctl stop firewalld.service
systemctl disable firewalld.service
#同时需要关闭windows防火墙

#启动组件：
airflow webserver -D
airflow scheduler -D
airflow worker -D
airflow flower -D

```

问题：airflow TypeError: __init__() got an unexpected keyword argument 'app'



问题：airflow worker启动报错：airflow No module named 'sqlalchemy.ext.declarative.clsregistry'

解决办法：

依赖版本问题导致，使用如下命令：

```shell
pip install SQLAlchemy==1.3.23 
pip install Flask-SQLAlchemy==2.4.4
```

### Ⅲ.Web 页面查看

```shell
#地址
192.168.50.13：8085/admin/
#测试
可以选择airflow_db数据库简单查询进行测试
select * from log;

```

[![][img-4]

5. 可能出现的 ERROR
--------------

### **错误 1：**

```shell
#启动webserver组件时可能会报如下错误：
Error: 'python:airflow.www.gunicorn_config' doesn‘t exist

```

安装指定版本的 gunicorn 即可：

(1) Airflow1.10 版本对应 gunicorn 的 19.4.0 版本：

```shell
sudo pip install gunicorn==19.4.0

```

### 错误 2：

```shell
$ AIRFLOW_HOME=/var/lib/airflow airflow initdb
[2019-09-07 20:51:32,416] {__init__.py:51} INFO - Using executor SequentialExecutor
Traceback (most recent call last):
  File "/bin/airflow", line 22, in <module>
    from airflow.bin.cli import CLIFactory
  File "/usr/lib/python2.7/site-packages/airflow/bin/cli.py", line 68, in <module>
    from airflow.www_rbac.app import cached_app as cached_app_rbac
  File "/usr/lib/python2.7/site-packages/airflow/www_rbac/app.py", line 26, in <module>
    from flask_appbuilder import AppBuilder, SQLA
  File "/usr/lib/python2.7/site-packages/flask_appbuilder/__init__.py", line 5, in <module>
    from .base import AppBuilder
  File "/usr/lib/python2.7/site-packages/flask_appbuilder/base.py", line 5, in <module>
    from .api.manager import OpenApiManager
  File "/usr/lib/python2.7/site-packages/flask_appbuilder/api/__init__.py", line 11, in <module>
    from marshmallow_sqlalchemy.fields import Related, RelatedList
  File "/usr/lib/python2.7/site-packages/marshmallow_sqlalchemy/__init__.py", line 1, in <module>
    from .schema import TableSchemaOpts, ModelSchemaOpts, TableSchema, ModelSchema
  File "/usr/lib/python2.7/site-packages/marshmallow_sqlalchemy/schema.py", line 101
    class TableSchema(ma.Schema, metaclass=TableSchemaMeta):
                                      ^
SyntaxError: invalid syntax

```

```shell
pip uninstall marshmallow-sqlalchemy
pip install marshmallow-sqlalchemy==0.17.1

```

### 错误 3：

```shell
Traceback (most recent call last):
  File "/usr/bin/airflow", line 18, in <module>
    from airflow.bin.cli import CLIFactory
  File "/usr/lib/python2.7/dist-packages/airflow/bin/cli.py", line 65, in <module>
    auth=api.api_auth.client_auth)
AttributeError: 'module' object has no attribute 'client_auth'

```

```shell
auth_backend=airflow.contrib.auth.backends.password_auth
写在webserver下，不要写在client下面

```





[img-0]:data:image/jpeg;base64,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

