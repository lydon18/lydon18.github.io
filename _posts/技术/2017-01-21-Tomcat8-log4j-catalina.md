---
layout: post
title: tomcat8使用log4j分割日志的流程
category: 技术
keywords: tomcat8 log4j
---

## 准备工作
1.下载log4j-1.2.17.jar（http://www.apache.org/dist/logging/log4j/1.2.17/）      
2.下载 tomcat-juli.jar（http://www.apache.org/dist/tomcat/tomcat-8/v8.0.39/bin/extras/）      
3.下载 tomcat-juli-adapters.jar（http://www.apache.org/dist/tomcat/tomcat-8/v8.0.39/bin/extras/）	
**(若为其他版本须更名为tomcat-juli.jar和tomcat-juli-adapters.jar)**

## 替换jar包修改配置文件

1.把log4j-1.2.17.jar和tomcat-juli-adapters.jar放到$CATALINA_HOME/lib下；    		
2.用新下载的tomcat-juli.jar包替换掉$CATALINA_HOME/bin/tomcat-juli.jar包。（名称必须为tomcat-juli.jar）；				
3.删除$CATALINA_BASE/conf/logging.properties(或者更改名称)    					
4.修改 Tomcat 的 conf/context.xml 文件，

```
将<Context>改为<Context swallowOutput="true">	  
```
			   
5.新建log4j.properties 放到$CATALINA_HOME/lib目录下；

```
	log4j.rootLogger=INFO, CATALINA,LOCALHOST,MANAGER,HOST-MANAGER
	
	# Define all the appenders
	log4j.appender.CATALINA=org.apache.log4j.RollingFileAppender
	log4j.appender.CATALINA.File=${catalina.base}/logs/catalinas.out
	log4j.appender.CATALINA.MaxFileSize=10240KB
	log4j.appender.CATALINA.MaxBackupIndex=100
	log4j.appender.CATALINA.Append=true
	log4j.appender.CATALINA.Encoding=UTF-8
	# Roll-over the log once per day
	#log4j.appender.CATALINA.DatePattern='.'yyyy-MM-dd-HH-mm'.log'
	log4j.appender.CATALINA.layout = org.apache.log4j.PatternLayout
	log4j.appender.CATALINA.layout.ConversionPattern = %d [%t] %-5p %c- %m%n
	
	log4j.appender.LOCALHOST=org.apache.log4j.RollingFileAppender
	log4j.appender.LOCALHOST.File=${catalina.base}/logs/localhost.log
	log4j.appender.LOCALHOST.MaxFileSize=10240KB
	log4j.appender.LOCALHOST.MaxBackupIndex=100
	log4j.appender.LOCALHOST.Append=true
	log4j.appender.LOCALHOST.Encoding=UTF-8
	log4j.appender.LOCALHOST.layout = org.apache.log4j.PatternLayout
	log4j.appender.LOCALHOST.layout.ConversionPattern = %d [%t] %-5p %c- %m%n
	
	log4j.appender.MANAGER=org.apache.log4j.RollingFileAppender
	log4j.appender.MANAGER.File=${catalina.base}/logs/manager.log
	log4j.appender.MANAGER.MaxFileSize=10240KB
	log4j.appender.MANAGER.MaxBackupIndex=100
	log4j.appender.MANAGER.Append=true
	log4j.appender.MANAGER.Encoding=UTF-8
	log4j.appender.MANAGER.layout = org.apache.log4j.PatternLayout
	log4j.appender.MANAGER.layout.ConversionPattern = %d [%t] %-5p %c- %m%n
	
	log4j.appender.HOST-MANAGER=org.apache.log4j.RollingFileAppender
	log4j.appender.HOST-MANAGER.File=${catalina.base}/logs/host-manager.log
	log4j.appender.HOST-MANAGER.MaxFileSize=10240KB
	log4j.appender.HOST-MANAGER.MaxBackupIndex=100
	log4j.appender.HOST-MANAGER.Append=true
	log4j.appender.HOST-MANAGER.Encoding=UTF-8
	log4j.appender.HOST-MANAGER.layout = org.apache.log4j.PatternLayout
	log4j.appender.HOST-MANAGER.layout.ConversionPattern = %d [%t] %-5p %c- %m%n
	
	log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
	log4j.appender.CONSOLE.Encoding=UTF-8
	log4j.appender.CONSOLE.layout = org.apache.log4j.PatternLayout
	log4j.appender.CONSOLE.layout.ConversionPattern = %d [%t] %-5p %c- %m%n
	
	# Configure which loggers log to which appenders
	log4j.logger.org.apache.catalina.core.ContainerBase.[Catalina].[localhost]=INFO, LOCALHOST
	log4j.logger.org.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/manager]=\
	  INFO, MANAGER
	log4j.logger.org.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/host-manager]=\
	  INFO, HOST-MANAGER
```

6.重启tomcat
