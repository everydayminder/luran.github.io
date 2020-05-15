---
layout: single
title: 커맨드라인에서 Maven/java 웹 프로젝트를 Tomcat에  배포하려면?
date: 2015-02-22 23:38:27.000000000 +09:00
categories:
- development
tags: [deploy, java, maven, cargo, tomcat]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
java 웹 애플리케이션을 Tomcat에 배포하려면 여러 가지 방법이 있다.
1. 웹 애플리케이션 디렉토리 전체를 webapps 밑에 직접 복사하거나,
2. 웹 애플리케이션 디렉토리를 압축한 WAR 파일을 webapps 밑에 직접 복사하거나,
3. Tomcat의 매니저 웹 GUI를 통해, WAR 파일을 배포할 수 있다.

그러나, 직접 특정 디렉토리에 복사하는 방법은 무엇보다 번거롭다.
Tomcat의 매니저 웹을 통해 배포하는 것은 쉽지만, 자동으로 배포를 하고자 한다면 매번
사용하기 어렵다.

다음과 같은 경우, Cargo라는 프로그램/ 플러그인을 사용하면 좋을 것 같다.
1. 커맨드라인으로부터 웹 애플리케이션을 배포하고 싶다.
2. WAS가 로컬/리모트로 접근 가능하다.
3. Ant/ Maven을 사용하고 있다.
4. WAS가 다른 것으로 대체될 가능성, 혹은 서로 다른 WAS가 내 웹 애플리케이션에 혼용될 수도 있다.
5. 그러나, 동일한 방법으로 웹 애플리케이션을 여전히 배포하고 싶다. (커맨드라인으로부터)

Cargo에 대한 정확하고, 자세한 정보는 무엇보다 공식 홈페이지(http://cargo.codehaus.org)를 참조하는 것이
바람직하다.

홈페이지의 What is CARGO?란에서는 다음과 같이 Cargo를 소개하고 있다.
Cargo is a thin wrapper that allows you to manipulate various type of application containers
(Java EE and others) in a standard way.

주목할 만한 것은 결국, various type of application containers in a standard way라고 보면 될 듯 하다.

*  Cargo가 지원하는 Containers
- Geronimo 1.x ~ 3.x
- Glassfish 2.x ~ 4.x
- JBoss 3.x ~ 7.4.x
- Jetty 4.x ~ 9.x
- Jo! 1.x
- JOnAS 4.x ~ 5.x
- JRun 4.x
- Orion/OC4J 9.x ~ 10.x
- Resin 2.x ~ 4.x
- Tomcat 4.x ~ 8.x
- TomEE 1.x
- WebLogic 8.x ~ 12.1.x
- WebSphere 8.5.x
- WildFly 8.x

* Standard way = Cargo의 사용법 + container 별 설정
앞서 언급한 다양한 WAS들은 결국 몇몇 property를 선언하는 환경 설정의 문제로 국한된다.
본 글에서는 Maven 기반 웹 애플리케이션을 Tomcat7에 배포하는 설정을 간단하게 적어보고자 한다.

1. Tomcat 설정
Tomcat에는 기본적으로 권한 및 룰이 등록되어 있지 않기 때문에 Cargo를 사용하여 웹 애플리케이션을 배포하려면
그에 필요한 내용을 선언해야 한다.

Tomcat의 설치 디렉토리를 $CATALINA_HOME이라 할 때, $CATALINA_HOME/conf/tomcat-users.xml을 편집하여,
다음의 내용을 추가한다.

```
<role rolename="manager-script" />
<user username="deploy" password="deploy" roles="manager-script" />
```

GUI 기반이 아닌, CLI 기반으로 제어하기 위해, tomcat에 manager-script 롤을 선언한다.
배포에 사용할 계정 및 패스워드를 선언하고 (위의 예에서는 deploy/deploy), 이 계정에 앞서 선언한 manager-script 롤을
부여한다.

2. Maven 설정
Cargo 홈페이지상의 Downloads 탭으로부터 다운로드를 받을 수도 있으나,
Maven 기반으로 설정할 경우는, 본문에 표기된 바와 같이

If you want to use Cargo from Maven 2 or Maven 3, you don't need to install anything at all as Maven will automatically download the required jars when you first use the plugin.

저절로 복사가 된다고 하였으나, 저절로 복사가 되도록 설정을 해줘야 한다.
http://repo1.maven.org/maven2/org/codehaus/cargo/cargo-maven2-plugin
에 접속하여 확인하면,

2014/7/28에 배포된 1.4.9가 이 글을 작성하는 시점 기준으로 가장 최신버전이다.
본 글은, 예전에 1.2.4로 테스트 환경을 구축한 것을 바탕으로 작성하였기에, 아래
설정에는 1.2.4로 기록하였다. (1.4.9로 적어도 동일하게 동작할 것이라 예상한다.)

pom.xml에 cargo-maven2-plugin 설정을 하자.
```
<plugin>
    <groupId>org.codehaus.cargo</groupId>
	<artifactId>cargo-maven2-plugin</artifactId>
	<version>1.2.4</version>
	<configuration>
		<container>
			<containerId>tomcat7x</containerId>
			<type>remote</type>
		</container>
		<configuration>
			<type>runtime</type>
			<properties>
				<cargo.remote.uri>http://tomcat_ip:tomcat_port/manager/text</cargo.remote.uri>
				<cargo.remote.username>username</cargo.remote.username>
				<cargo.remote.password>password</cargo.remote.password>
			</properties>
		</configuration>

		<deployer>
			<type>remote</type>
			<deployables>
				<deployable>
					<groupId>com.luran</groupId>
					<artifactId>jsweb</artifactId>
					<type>war</type>
					<properties>
						<context>/jsweb</context>
					</properties>
				</deployable>
			</deployables>
		</deployer>
	</configuration>
</plugin>
```

- cargo.remote.uri 에 Tomcat manager 주소를 http://ip:port/manager/text로 기록
- cargo.remote.username에 배포를 위한 계정 기록
- cargo.remote.password에 배포를 위한 계정의 비밀번호 기록
- deployable이하 내용에는 어떤 war로 배포할 것인지 (maven 형식) 기록
- properties~context에는, servlet context 명 기록 (본 예제에서는, http://ip:port/jsweb으로 배포함)

3. Maven을 통한, 웹 애플리케이션 관리
- deploy
```
mvn cargo:deploy
```

또는

```
mvn cargo:deployer-deploy
```

- undeploy
```
mvn cargo:undeploy
```

또는
```
mvn cargo:deployer-undeploy
```

- redeploy
```
mvn cargo:redeploy
```

또는
```
mvn cargo:deployer-redploy
```

4. 자동 배포 응용 (빌드 서버 또는 스크립트 상)

```
mvn clean install package cargo:redploy
```

이와 같은 식으로 작성한다면, 빌드까지 모두 한 후 Cargo를 통해 Tomcat으로 웹 애플리케이션을
자동 배포할 수 있다.

