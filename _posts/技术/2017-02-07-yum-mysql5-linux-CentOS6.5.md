---
layout: post
title: Linux centos5.6下yum mysql5.7安装
category: 技术
keywords: Linux centos5.6 mysql5.7 yum
---

## 准备工作
查看当前centos版本和内核，位数。
```
1.Linux版本（－－/etc/redhat-release配置文件用一行内容来声明 Red Hat 的名称和版本号。）
[root@localhost  ~]# cat /etc/redhat-release    
CentOS release 6.5 (Final)
2.系统位数
[root@localhost  ~]#  cat /proc/version
Linux version 2.6.32-573.22.1.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-16) (GCC) ) #1 SMP Wed Mar 23 03:35:39 UTC 2016
3.或者
[root@localhost  ~]# uname -a
Linux localhost  2.6.32-573.22.1.el6.x86_64 #1 SMP Wed Mar 23 03:35:39 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
```

## 修改yum仓库 
去这里找到你linux版本对应的包：http://dev.mysql.com/downloads/repo/yum/ 
找到对应的版本后可以直接点击右侧的”Download”按钮下载，然后手动上传到你的服务器上。 
比如包名为：mysql57-community-release-el5-7.noarch.rpm

## 切换源
```
rpm -Uvh mysql57-community-release-el5-7.noarch.rpm
```

## 开始安装 
###安装MySQL 服务器端： 
mysql-devel所需的库和包含文件
```
yum install mysql-devel
```

###安装mysql-server
mysql-server是mysql服务需要安装的
```
yum install mysql-server
```

###安装mysql客户端：
```
yum install mysql 
```

### 启动mysql服务
service mysqld start或者/etc/init.d/mysqld start 
停止： 
service mysqld stop 
重启： 
service mysqld restart*

若启动失败可能是配置文件my.cnf没修改
```
[mysqld]
basedir=/var/lib/mysql
datadir=/var/lib/mysql/data
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
explicit_defaults_for_timestamp = TRUE
table_open_cache=4
query_cache_limit=256K
query_cache_size=4M
max_allowed_packet=1M
sort_buffer_size=64K
read_buffer_size=256K
thread_stack=64K
innodb_buffer_pool_size = 56M
innodb_flush_neighbors=0
innodb_flush_log_at_trx_commit=2
character-set-server=utf8
collation-server=utf8_general_ci

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
若datadir=/var/lib/mysql/data中有之前的数据会启动失败，把原来mysql安装目录下的data文件夹拷出来；
拷出来的data文件夹里，除了mysql、test子文件夹外的子文件夹，全部都是数据库。启动后再把除了mysql test之外的文件夹拷贝进来，原来的数据文件还在。

###进入mysql，修改登录密码 
mysql 5.7给我们提供了临时密码
```
grep 'temporary password' /var/log/mysqld.log
```
这样得到临时密码，然后登录
```
mysql -uroot -p"临时密码" 
```
这样就成功登录了，下面我们可以修改登录密码了，比如修改为”admin” 
因为mysql 5.7提高了密码安全级别，会报错（ERROR 1819 (HY000): Your password does not satisfy the current policy requirements）
所以我们需要先
```
mysql> set global validate_password_policy=0;
```
再修改
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'admin';
```
http://blog.csdn.net/zhanngle/article/details/41042631
http://www.360doc.com/content/16/0324/13/16915_544855686.shtml

