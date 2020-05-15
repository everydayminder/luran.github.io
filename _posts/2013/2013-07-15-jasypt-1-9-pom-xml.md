---
layout: single
title: jasypt 1.9용 pom.xml 설정
date: 2013-07-15 08:35:17.000000000 +09:00
categories:
- development
tags: [jasypt]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
사실 잘 찾아보니(google), jasypt의 pom.xml 의존성 파일도
이미 mvn repository내 존재했다.

```xml
 <!-- Jasypt -->
 <dependency>
	<groupId>org.jasypt</groupId>
	<artifactId>jasypt</artifactId>
	<version>1.9.0</version>
 </dependency>
 <dependency>
	<groupId>org.jasypt</groupId>
	<artifactId>jasypt-hibernate3</artifactId>
	<version>1.9.0</version>
 </dependency>
 <dependency>
	<groupId>org.jasypt</groupId>
	<artifactId>jasypt-spring3</artifactId>
	<version>1.9.0</version>
 </dependency>
```

