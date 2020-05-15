---
layout: single
title: Tomcat Web Application 모니터링 툴 - javamelody 설정 방법
date: 2013-08-01 02:20:35.000000000 +09:00
categories:
- development
tags: [monitoring, javamelody, tomcat]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Tomcat Web Application을 모니터링하거나, profiiling하기 위한 괜찮은 툴을 소개한다.
Javamelody(https://code.google.com/p/javamelody)를 설정하는 방법은 무척 간단하다.

<strong>1. Jar 설정</strong>
WEB-INF/lib 내 javamedloy.zip내의 javamelody.jar와 jrobin-x.jar를 복사한다.
내 경우는 maven 을 사용하므로, 기존의 pom.xml에 다음과 같이 dependency를 추가하였다.

```xml
 <dependency>
 <groupId>net.bull.javamelody</groupId>
 <artifactId>javamelody-core</artifactId>
 <version>1.43.0</version>
 </dependency>
```

<strong>2. web.xml 설정</strong>
기존에 실행하던 웹 애플리케이션을 모니터링/분석해야 하므로, web.xml의 내용을 편집한다.
보통 web.xml에서는 선언된 순서로 우선순위를 지니므로, 다른 서블릿 선언보다 위쪽에 다음의
설정을 추가로 기록한다.

```xml
 <filter>
 <filter-name>monitoring</filter-name>
 <filter-class>net.bull.javamelody.MonitoringFilter</filter-class>
 </filter>
 <filter-mapping>
 <filter-name>monitoring</filter-name>
 <url-pattern>/*</url-pattern>
 </filter-mapping>
 <listener>
 <listener-class>net.bull.javamelody.SessionListener</listener-class>
 </listener>
```

<strong>3. 동작 확인</strong>
기본 설정은 이것으로 일단 끝!
이제 web application을 실행시키자.

```
 http://<host>/<context>/monitoring
```
과 같이 실행시키면, 기존 사이트에 대한 모니터링 페이지가 정상 적으로 보일 것이다.
대략 다음과 같은 화면이 뜬다.

<a title="JavaMelody 모니터링 화면 참고" href="http://http://javamelody.googlecode.com/svn/trunk/javamelody-core/src/site/resources/screenshots/graphs.png">http://javamelody.googlecode.com/svn/trunk/javamelody-core/src/site/resources/screenshots/graphs.png</a>

<strong>4. 보안설정(ID/Password 기반)</strong>
role 기반으로 간략하게 보안 설정을 해보자. web.xml에 다음의 내용을 추가한다.

```xml
 <login-config>
 <auth-method>BASIC</auth-method>
 <realm-name>Monitoring</realm-name>
 </login-config>

<security-role>
 <role-name>monitoring</role-name>
 </security-role>

<security-constraint>
 <web-resource-collection>
 <web-resource-name>Monitoring</web-resource-name>
 <url-pattern>/monitoring</url-pattern>
 </web-resource-collection>
 <auth-constraint>
 <role-name>monitoring</role-name>
 </auth-constraint>
 </security-constraint>
```

만약, 이대로 tomcat을 restart하고, /monitoring으로 접근한다면, HTTP BASIC Authentication으로 인증 요청을 할 것이다.

<strong>5. tomcat-user 추가</strong>
위의 인증창에 접근할 수 있는 사용자를 추가하자.
tomcat-users.xml을 찾아 다음과 같이 사용자 정보를 추가한다.

```xml
 <tomcat-users>
 ..
 <role rolename="monitoring"/>
 <user username="monitoring" password="monitoring" roles="monitoring" />
 ..
 </tomcat-users>
```

이제 Tomcat을 재시작하고, BASIC authentication popup window가 뜨면, 위의 정보로 인증하고, 처음에 본 모니터링 페이지가 정상적으로 뜨는지 확인한다.
