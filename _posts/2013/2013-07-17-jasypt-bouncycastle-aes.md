---
layout: single
title: jasypt + BouncyCastle AES 설정
date: 2013-07-17 05:08:59.000000000 +09:00
categories:
- development
tags: [jasypt, java, bouncycastle]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Sun의 JCE에서도 AES를 지원하지만, Password Based Encryption방식으로 보다 많은 종류의 암호화 알고리즘을 지원하는 BouncyCastle 라이브러리를
설치하고, jasypt에서도 BouncyCastle을 사용한 AES 암/복호화를 사용하도록 설정해 보았다.

친근한 이름(어린 아이들이 좋아할)과는 다르게, 보안라이브러리이다.

1. BouncyCastle 다운로드
BouncyCastle의 공식 사이트는 www.bouncycastle.org이다.
본인이 사용하는 jvm 버전에 맞는 것으로 다운로드한다.
내 경우, jdk 1.6을 사용하고 있어 bcprov-jdk16-146.jar를 받았다.
maven을 사용하고 있다면, pom.xml에 아래와 같이 추가하여 다운로드 할 수도 있다.

```xml
 <dependency>
 <groupId>org.bouncycastle</groupId>
 <artifactId>bcprov-jdk16</artifactId>
 <version>1.46</version>
 </dependency>
```

2. BouncyCastle 설치
jar 파일이므로 임의의 디렉토리에 복사 후, classpath에 추가하거나,
$JAVA_HOME/jre/lib/ext에 복사하여 자동으로 classpath가 인식하도록 한다.
maven을 사용한다면, 위의 dependency 설정으로도 충분할 것이다.
라이브러리 설치 자체는 이것으로 끝났으나, 추가 환경설정이 필요하다.

3. BouncyCastle을 사용하기 위한 환경설정
BouncyCastle을 JCE Provider로 인식시키기 위해 추가 설정이 필요하다.
$JAVA_HOME/jre/lib/security/java.security 파일에 BouncyCastle을 등록하자.

4. jasypt에 BouncyCastle AES 사용설정
(1) application-context.xml에 hibernate.cfg.xml 설정
```xml
 <bean id="sessionFactory" class="org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean">
 <property name="dataSource" ref="dataSource" />
 <property name="packagesToScan" value="com.luran" />
 <property name="configLocation" value="classpath:hibernate.cfg.xml" />
 </bean>
```

(2) hibernate.cfg.xml에 mapping.hbm.xml 지정 (type 선언 내용 포함)
```xml
 <hibernate-configuration>
 <session-factory>
 ...
 <mapping resource="META-INF/mapping.hbm.xml"/>
 ...
 </session-factory>
 </hibernate-configuration>

```

(3) mapping.hbm.xml내 타입 선언부 중, 알고리즘 변경 (PBEWITHSHA256AND128BITAES-CBC-BC)
```xml
 <hibernate-mapping package="com.luran.jasypt_test">
 <typedef name="encryptedString" class="org.jasypt.hibernate3.type.EncryptedStringType">
 <param name="algorithm">PBEWITHSHA256AND128BITAES-CBC-BC</param>

 <!-- <param name="providerName">BC</param> -->
 <param name="password">jasypt</param>
 <param name="keyObtentionIterations">1000</param>

 <!-- <param name="stringOutputType">hexadecimal</param> -->
 </typedef>
 </hibernate-mapping>
```
