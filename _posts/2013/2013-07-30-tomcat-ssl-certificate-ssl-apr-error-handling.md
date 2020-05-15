---
layout: single
title: Tomcat SSL인증서 설정시 APR 관련 에러 발생시
date: 2013-07-30 07:13:57.000000000 +09:00
categories:
- system
tags: [tomcat, certificate, ssl, apr]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Tomcat에 SSL인증서를 설정하다 다음과 같은 에러가 날 경우에 대한 조치 방법이다.

<blockquote>
org.apache.coyote.AbstractProtocolHandler init
심각: Failed to initialize end point associated with ProtocolHandler ["http-apr-8444"]
java.lang.Exception: Connector attribute SSLCertificateFile must be defined when using SSL with APR
at org.apache.tomcat.util.net.AprEndpoint.bind(AprEndpoint.java:468)
at org.apache.tomcat.util.net.AbstractEndpoint.init(AbstractEndpoint.java:482)
at org.apache.coyote.AbstractProtocolHandler.init(AbstractProtocolHandler.java:354)
at org.apache.catalina.connector.Connector.initInternal(Connector.java:910)
at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:101)
at org.apache.catalina.core.StandardService.initInternal(StandardService.java:559)
at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:101)
at org.apache.catalina.core.StandardServer.initInternal(StandardServer.java:781)
at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:101)
at org.apache.catalina.startup.Catalina.load(Catalina.java:572)
at org.apache.catalina.startup.Catalina.load(Catalina.java:595)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
at java.lang.reflect.Method.invoke(Method.java:597)
at org.apache.catalina.startup.Bootstrap.load(Bootstrap.java:262)
at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:430)
2013. 7. 30 오전 11:20:02 org.apache.catalina.core.StandardService initInternal
심각: Failed to initialize connector [Connector[HTTP/1.1-8444]]
org.apache.catalina.LifecycleException: Protocol handler initialization failed
at org.apache.catalina.connector.Connector.initInternal(Connector.java:912)
at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:101)
at org.apache.catalina.core.StandardService.initInternal(StandardService.java:559)
at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:101)
at org.apache.catalina.core.StandardServer.initInternal(StandardServer.java:781)
at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:101)
at org.apache.catalina.startup.Catalina.load(Catalina.java:572)
at org.apache.catalina.startup.Catalina.load(Catalina.java:595)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
at java.lang.reflect.Method.invoke(Method.java:597)
at org.apache.catalina.startup.Bootstrap.load(Bootstrap.java:262)
at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:430)
Caused by: java.lang.Exception: Connector attribute SSLCertificateFile must be defined when using SSL with APR
at org.apache.tomcat.util.net.AprEndpoint.bind(AprEndpoint.java:468)
at org.apache.tomcat.util.net.AbstractEndpoint.init(AbstractEndpoint.java:482)
at org.apache.coyote.AbstractProtocolHandler.init(AbstractProtocolHandler.java:354)
at org.apache.catalina.connector.Connector.initInternal(Connector.java:910)
... 13 more
</blockquote>

조치 방법은 간단하다.
$CATALINA_HOME/conf/server.xml 중 다음의 설정을 찾아 주석처리하자.

```
 <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on"> 
```

