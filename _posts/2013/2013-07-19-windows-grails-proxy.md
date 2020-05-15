---
layout: single
title: Windows에서 grails proxy 설정하기
date: 2013-07-19 08:29:44.000000000 +09:00
categories:
- development
tags: [grails, development]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Windows 환경의 grails에서 proxy설정과 관련된 명령어를 수행하면 제대로 proxy 설정이 되지 않는다.
원래 다음과 같이 설정하라고 가이드가 되어 있다.

```
grails add-proxy client --host=someproxyserver --port= 8080
grails set-proxy client
```

c:users사용자명.grailsProxySettings.groovy 파일의 내용을 열어보자.
제대로 설정되었다면, http.proxyHost와 http.proxyPort등의 값들이 정상적으로 반영이 되어 있어야 한다.

해결 방법은 둘 중의 하나!
일단 생성된 ProxySettings.groovy 파일을 편집하여 본인이 원하는 proxy 설정값으로 변경해 넣거나,
위의 명령어에 따옴표를 활용하면 된다.

```
grails add-proxy client &quot;--host=someproxyserver&quot; &quot;--port= 8080&quot;
grails set-proxy client
```
