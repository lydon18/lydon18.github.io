# linux nginx install

1. 下载安装包, 选择stable稳定版

``` https://www.nginx.com/resources/wiki/start/topics/tutorials/install/
https://www.nginx.com/resources/wiki/start/topics/tutorials/install/
```

2. 将安装包传输到linux服务器,使用 `tar -zxvf` 解压

3. 进入解压后的目录 `cd nginx-1.20.1/`

4. 执行 `./configure --prefix=/usr/local/nginx`  (--prefix=/usr/local/nginx 是指定安装到那个目录)

   出现错误

   ```
   ./configure: error: the HTTP rewrite module requires the PCRE library.
   You can either disable the module by using --without-http_rewrite_module
   option, or install the PCRE library into the system, or build the PCRE library
   statically from the source with nginx by using --with-pcre=<path> option.
   ```

   ```
   安装pcre-devel解决问题
   yum -y install pcre-devel
   ```

   还有可能出现：

   ```
   ./configure: error: the HTTP cache module requires md5 functions
   from OpenSSL library.   You can either disable the module by using
   --without-http-cache option, or install the OpenSSL library into the system,
   or build the OpenSSL library statically from the source with nginx by using
   --with-http_ssl_module --with-openssl=<path> options.
   ```

   解决办法：

   ```
   yum -y install openssl openssl-devel
   ```

   5. make 编译 （make的过程是把各种语言写的源码文件，变成可执行文件和各种库文件）

   6. make install 安装 （make install是把这些编译出来的可执行文件和库文件复制到合适的地方）

      **启动操作**

​         `  /usr/local/nginx/sbin/nginx `  

​	     ` /usr/local/nginx/sbin/nginx -t`  (查看配置信息) 

​			如果配置正确会提示:

```
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

​			修改端口号 :  ` vi /usr/local/nginx/conf/nginx.conf`    listen默认是80

​		**平滑重启**

​			kill -HUP 住进称号或进程号文件路径 (`kill -HUP /usr/local/nginx/logs/nginx.pid`)  

​		 或者:

​		`/usr/local/nginx/sbin/nginx -s reload`

