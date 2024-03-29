---
layout: post
title: linux下更改mysql数据库目录位置
category: 技术
keywords: java
---

## MySQL默认的数据文件存储目录为/var/lib/mysql。假如要把目录移到/home/data下需要进行下面几步：


### 1、home目录下建立data目录
```
cd /home
mkdir data
```

### 2、把MySQL服务进程停掉： 
```
　mysqladmin -u root -p shutdown
   ```


### 3、把/var/lib/mysql整个目录移到/home/data
```
mv /var/lib/mysql　/home/data/
```
这样就把MySQL的数据文件移动到了/home/data/mysql下
   


### 4、找到my.cnf配置文件
　　如果/etc/目录下没有my.cnf配置文件，请到/usr/share/mysql/下找到*.cnf文件，拷贝其中一个到/etc/并改名为my.cnf)中。命令如下：
```
cp /share/mysql/my-medium.cnf　/etc/my.cnf
```


### 5、编辑MySQL的配置文件/etc/my.cnf
　　为保证MySQL能够正常工作，需要指明mysql.sock文件的产生位置。 修改socket=/var/lib/mysql/mysql.sock一行中等号右边的值为：/home/mysql/mysql.sock 。操作如下：

```
vi　 my.cnf　　　 (用vi工具编辑my.cnf文件，找到下列数据修改之)    
# The MySQL server	 	   
[mysqld]	 	 	    
port　　　= 3306	     
#socket　 = /var/lib/mysql/mysql.sock（原内容，为了更稳妥用“#”注释此行）	   
socket　 = /home/data/mysql/mysql.sock　　　（加上此行）	 	 	 	 	
```

### 6、修改MySQL启动脚本/etc/init.d/mysql
　　最后，需要修改MySQL启动脚本/etc/init.d/mysql，把其中datadir=/var/lib/mysql一行中，等号右边的路径改成你现在的实际存放路径：home/data/mysql。

```
[root@test1 etc]# vi　/etc/init.d/mysql          
#datadir=/var/lib/mysql　　　　（注释此行）    				  
datadir=/home/data/mysql　　 （加上此行）    				             
```

### 7、重新启动MySQL服务

```
/etc/init.d/mysql　start
```
　　或用reboot命令重启Linux，如果工作正常移动就成功了，否则对照前面的7步再检查一下。

假设数据库原来的存放目录为/usr/local/mysql/var ，新的目录为/var/mysql

```
1.	/usr/local/mysql/share/mysql/mysql.server stop             
2.	mkdir /var/mysql     
    chown -R mysql /var/mysql    
    chgrp -R mysql /var/mysql       
3.	vi /etc/my.cnf     
    add: datadir=/var/mysql   
    log-bin=/var/mysql/mysql-bin             
 4.	/usr/local/mysql/share/mysql/mysql.server start
```