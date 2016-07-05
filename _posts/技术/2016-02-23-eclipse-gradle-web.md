---
layout: post
title: Eclipse下用 Gradle构建 Web Application
category: 技术
keywords: IDE
---

1.安装 Gradle插件<br>
在 Eclipse Marketplace中搜索 Gradle, 选择安装 Gradle Integration for Eclipse

2.新建一个 Dynamic Web Project<br>
在项目根目录下新建 build.gradle 文件, 并向其中写入如下内容: <br>

```
import org.gradle.plugins.ide.eclipse.model.Facet

apply plugin: 'java'
apply plugin: 'eclipse-wtp'
apply plugin: 'war'

webAppDirName = 'web'
sourceSets.main.java.srcDir 'src'
[compileJava]*.options*.encoding = 'UTF-8'
version = '1.0'
sourceCompatibility = 1.7

repositories {
	mavenLocal()
	mavenCentral()
}

ext {
	springVersion = '4.2.3.RELEASE'
	hibernateVersion = '5.0.5.Final'
	hibernateSearchVersion = '5.5.1.Final'
	freemarkerVersion = '2.3.23'
	httpclientVersion = '4.5'
	jacksonVersion = '2.6.1'
}

war{
    baseName ='drizzt_web'
    from("$projectDir/src/") {
        include "*.xml"
        into('WEB-INF/classes')
    }
    from("$projectDir/src/config/language") {
        into('WEB-INF/classes/config/language')
    }
    from("$projectDir/src/config/properties") {
        into('WEB-INF/classes/config/properties')
    }
}

dependencies {
	compile (
	"org.freemarker:freemarker:${freemarkerVersion}",

	'commons-logging:commons-logging:1.2',
	'commons-io:commons-io:2.4',
	'commons-fileupload:commons-fileupload:1.3.1',
	'commons-codec:commons-codec:1.10',
	'org.apache.commons:commons-collections4:4.0',
	'org.apache.commons:commons-lang3:3.4',

	"org.springframework:spring-context-support:${springVersion}",
	"org.springframework:spring-jdbc:${springVersion}",
	"org.springframework:spring-orm:${springVersion}",
	"org.springframework:spring-webmvc:${springVersion}",

	"org.hibernate:hibernate-core:${hibernateVersion}",
	"org.hibernate:hibernate-ehcache:${hibernateVersion}",
	"org.hibernate:hibernate-search:${hibernateSearchVersion}",

	'c3p0:c3p0:0.9.1.2',
	'mysql:mysql-connector-java:5.1.35',

	"com.fasterxml.jackson.core:jackson-core:${jacksonVersion}",
	"com.fasterxml.jackson.core:jackson-databind:${jacksonVersion}",
	"com.fasterxml.jackson.core:jackson-annotations:${jacksonVersion}",

	"org.apache.httpcomponents:httpclient:${httpclientVersion}",
	"org.apache.httpcomponents:httpmime:${httpclientVersion}",
	"org.apache.httpcomponents:httpclient-cache:${httpclientVersion}",

	'javax.activation:activation:1.1.1',
    'javax.transaction:jta:1.1',
	'javax.mail:mail:1.4.7',
    
    'org.slf4j:slf4j-api:1.7.10',
	'eu.bitwalker:UserAgentUtils:1.15',
	'antlr:antlr:2.7.7',
	'dom4j:dom4j:1.6.1',
	'net.coobird:thumbnailator:0.4.8',

	'org.apache.ant:ant:1.9.4',

	'org.quartz-scheduler:quartz:2.2.1',

	'cglib:cglib-nodep:3.1',
	'dom4j:dom4j:1.6.1'
	)
	compile('javax.servlet:javax.servlet-api:3.1.0')
}

eclipse {
	wtp {
		facet {
			facet name: 'jst.web', type: Facet.FacetType.fixed
			facet name: 'wst.jsdt.web', type: Facet.FacetType.fixed
			facet name: 'jst.java', type: Facet.FacetType.fixed
			facet name: 'jst.java', version: '1.7'
			facet name: 'jst.web', version: '3.1'
			facet name: 'wst.jsdt.web', version: '1.0'
		}
	}
}
```

3.接下来在项目上右击 -> configure -> convert to Gradle project<br>

4.再次在项目上右击 -> Gradle -> Refresh All<br>

5.如果没有 web.xml 文件的话, 再右击项目 -> Java EE Tools -> Generate Deployment Descriptor Stub<br>

6.最后看下效果如何, 在 web下新建 index.html,里面随便写点什么, 然后项目上右击 -> Run As -> Run on Server<br>
