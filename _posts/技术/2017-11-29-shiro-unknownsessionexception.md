--背景
最近在整合了Spring+Shiro+Redis实现tomcat集群session共享的问题之后，发布以后运行以后发现老是会出现：org.apache.shiro.session.UnknownSessionException: There is no session with id [xxxx]的问题，具体问题如下截图：

--出现这个问题的原因

　　只所以出现这个问题是因为在shiro的DefaultWebSessionManager类中，默认Cookie名称是JSESSIONID，这样的话与servlet容器名冲突, 如jetty, tomcat等默认JSESSIONID, 当跳出shiro servlet时如error-page容器会为JSESSIONID重新分配值导致登录会话丢失!

　　明白了出现这个问题的原因，就好办了，我们只需要自己指定一个与项目运行容器不冲突的sessionID就好了，具体如下，在spring-shiro.xml中进行如下配置：

```
 <!--sessionManager -->
 <bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
    <property name="sessionDAO" ref="redisSessionDAO" />
    <property name="sessionIdCookie" ref="simpleCookie"/>
 </bean>

 <bean id="simpleCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
    <constructor-arg name="name" value="shiro.sesssion"/>
    <property name="path" value="/"/>
 </bean>
```
