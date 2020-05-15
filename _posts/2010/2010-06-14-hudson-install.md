---
layout: single
title: Hudson - 설치하기
date: 2010-06-14 09:01:47.000000000 +09:00
categories:
- development
tags: [ci, hudson]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
이제는 일일 빌드보다 Continuous Integration(지속적인 통합)이 트렌드이다. 이와 관련하여, 수많은 종류의 tool이 존재하나, 그 중 Hudson이라는 무료 CI 툴을 설치하고, 주요 설정 방법, 사용방법에 대해 정리하고자 한다.

### 1. 준비물
+ JDK
+ Tomcat (설치 방법에 따라 다름)
+ Hudson

### 2. 설치 준비하기
 (1) JDK
+ <a title="[http://http;///java.sun.com]로 이동합니다." target="_blank" href="http://http;///java.sun.com">http://java.sun.com</a>
+ JAVA_HOME을 jdk 설치 디렉토리로 지정함

 (2) Tomcat
+ <a title="[http://tomcat.apache.org]로 이동합니다." target="_blank" href="http://tomcat.apache.org">http://tomcat.apache.org</a> 로부터 tomcat을 다운로드하여 설치함
+ CATALINA_HOME을 tomcat의 설치 디렉토리로 지정함

 (3) Hudson
+ <a title="[http://hudson-ci.org]로 이동합니다." target="_blank" href="http://hudson-ci.org">http://hudson-ci.org</a>
+ hudson.zip을 받았다면, hudson.war로 이름 변경

### 3. 설치하기
 (1) Tomcat에 연동하여 설치하기
+ tomcat 설치 디렉토리의 webapps에 hudson.war를 복사한다.
+ tomcat을 시작시킨다.
+ tomcat이 실행되면서 hudson.war의 설치가 진행된다.
+ http://localhost:8080/hudson 을 입력한다. 끝.

 (2) Tomcat과 독립적으로 설치하기
+ 항상 웹 서버를 띄워놓고 작업하는 것만이 좋은 해결책은 아님.
+ 별도의 애플리케이션으로 독립적으로 띄워보자.
+ hudson.bat 또는 hudson.sh을 다음과 같이 작성한다.

```shell
java -DHUDSON_HOME=C:/dev/hudson -jar hudson.war -Xms370m -Xmx370m --httpPort=8001 --ajp13Port=8019 > error.log.txt
```
+ 단, 본인은 hudson.war의 위치가 C:/dev/hudson이다.
+ 콘솔로 hudson이 동작한다.
+ http://localhost:8001 을 입력한다. 끝.


<img src="{{ site.baseurl }}/images/201006/cfile5.uf.165247164C1731971079CC.png" class="aligncenter" width="700" height="401" alt="" />

