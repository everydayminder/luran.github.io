---
layout: single
title: Eclipse/STS에서 maven 프로젝트 UTF-8, JDK 버전을 매번 해줘야 하나?
date: 2014-11-27 23:24:49.000000000 +09:00
categories:
- development 
tags: [eclipse, java, maven, sts]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Eclipse/STS에 사용중인 maven project로부터

1. 프로젝트를 새로 import할 때
2. Disable Maven Nature + Configure > Convert to Maven Project를 할 때

인코딩이 MS949로 자꾸 바뀌어서 수동으로 UTF-8로 바꿔줘야 했다면?
또 JDK 버전이 자꾸 1.5로 바뀌어서 수동으로 JDK 버전을 상위 버전으로 바꿔줘야 했다면?
다음의 옵션을 pom.xml에 추가해 보자.

pom.xml 파일 윗쪽에

```xml
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

와 같이 이 프로젝트는 UTF-8 인코딩이라고 선언해 주자.
pom.xml의 build 부분에 (없다면 추가로 써주면 된다)
아래와 같이 maven-compiler-plugin 선언을 하고, 사용하고자 하는 JDK 버전을 명시한다.

```xml
<build>
	<plugins>
		<plugin>
			<artifactId>maven-compiler-plugin</artifactId>
			<configuration>
				<source>1.7</source>
				<target>1.7</target>
			</configuration>
			<version>2.3.2</version>
			<executions>
				<execution>
					<id>default-testCompile</id>
					<phase>test-compile</phase>
					<goals>
						<goal>testCompile</goal>
					</goals>
				</execution>
				<execution>
					<id>default-compile</id>
					<phase>compile</phase>
					<goals>
						<goal>compile</goal>
					</goals>
				</execution>
			</executions>
		</plugin>
	</plugins>
</build>
```
이렇게 해서, SCM에 commit 하면, 새로 프로젝트를 import 할 경우와,
aven Feature를 껐다 켤 때에도 인코딩과 JDK 버전이 설정한 대로 유지된다.

