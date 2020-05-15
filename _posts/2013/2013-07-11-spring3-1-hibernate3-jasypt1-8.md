---
layout: single
title: Spring3.1 + Hibernate3 + jasypt1.8 연동테스트
date: 2013-07-11 07:06:53.000000000 +09:00
categories:
- development
tags: [hibernate, spring, jasypt]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
기존에 Spring 3.1과 Hibernate 3을 사용중이었는데,
DB에 저장된 데이터를 암호화해야 할 일이 생겼다.

Hibernate를 사용한 목적에 맞도록, 암호화도 DBMS에 transparent한 솔루션으로 적용하고 싶다.
조사해보니, jasypt라는 라이브러리를 사용하면 이를 비교적 적은 비용으로 구현이 가능한 것 같아 설치 테스트해 보았다.

jasypt.는 (Java Simplified Encryption)이라는 뜻이며, 공식 사이트는 www.jasypt.org 이다.
2013/7/11 현재 최신 버전은 1.9.0이지만, 중앙 maven repository에 올라와 있는 버전으로는 1.8이고,
Hibernate 관련 라이브러리가 1.8에는 동봉되어 있으나, 1.9.0에는 별도 관리되고 있는 것 같다.
(결국, maven 테스트의 편리성 때문에 그냥 1.8로 테스트를 진행했다.)

<strong>1. Maven Dependency 추가</strong>
pom.xml에 다음의 내용을 추가하였다.

```xml
<!-- Jasypt -->
<dependency>
    <groupId>org.jasypt</groupId>
    <artifactId>jasypt</artifactId>
    <version>1.8</version>
</dependency>
```
<strong>2. Spring bean 등록 (application-context.xml에 등록)</strong>

```xml
<bean id="strongEncryptor" class="org.jasypt.encryption.pbe.PooledPBEStringEncryptor">
    <property name="algorithm">
        <value>PBEWithMD5AndTripleDES</value>
    </property>
    <property name="password">
        <value>cUst0mP@sswOrd</value>
	<!-- Put whatever you want, it must be unique and strong -->
    </property>
    <property name="poolSize">
<value>4</value>
	<!-- to be optimal, put the number of cores of your processor-->
    </property>
</bean>

<bean id="hibernateStringEncryptor" class="org.jasypt.hibernate.encryptor.HibernatePBEStringEncryptor">
    <property name="registeredName">
        <value>strongHibernateStringEncryptor</value>
    </property>
    <property name="encryptor">
        <ref bean="strongEncryptor"/>
    </property>
</bean>
```

<strong>3. Type 정의</strong>
www.jasypt.org에는 hibernate.cfg.xml에 환경 설정을 등록하는 방법과, Spring내에서 annotation으로 등록하는 방법을 가이드하고 있는데,
어차피 Spring에서 annotation을 사용하고 있으므로, 그냥 model 클래스에 선언하여 사용해 보았다.

해당 model(entity) 클래스에 다음과 같이 적어준다.

```java
@TypeDef (name="encryptedString", typeClass=EncryptedStringType.class,
 parameters={@Parameter(name="encryptorRegisteredName", value="strongHibernateStringEncryptor")})
```

<strong>4. 코드내 적용</strong>
이제 암호화를 원하는 필드에 위의 타입을 annotation으로 적용하자.

```java
@Type(type="encryptedString")
public String getName() {
    return name;
}
```

별도의 decryption 옵션을 지정하지 않아도, 이제 model 객체는 자동으로 encrypt/decrypt를 수행하며,
해당 model을 사용하는 DAO들은 별도의 수정작업이 없이도 해당 model의 일부 필드를 암호화할 수 있게 되었다.

<strong>5.  기타 : JCE 설정</strong>
jasypt를 설치/테스트하는 도중, JVM내 JCE가 설치되어 있지 않은 것 같다는 에러 메시지가 떴었다.
오라클 공식 홈페이지에서 JCE 관련 파일을 받아서 설치하자.

Java SE Downloads 메뉴로부터 스크롤하여, Additional Resources부분을 살펴보면,
Java Cryptography Extension(JCE) Unlimited Strength jurisdiction Policy Files를 발견할 수 있을 것이다.

이 파일을 다운로드하여, 파일내 들어있는 jar파일들을 $JAVA_HOME/jre/lib/security에 풀어넣자. (기존에 있던 동일 이름의 파일들은
다른 곳에 복사해두자.)
이제 다시 실행시켜보면, 기존에 발생하던 JCE 라이브러리가 없다는 에러가 발생하지 않을 것이다.

<strong>6. 기타 : Hibernate 검색 관련</strong>
잠시만 생각해보면 당연하겠지만, 기존에 Hibernate의 criteria등을 써서 검색에 사용하던 필드가 암호화 대상 필드가 되었다면?
검색이 되지 않는다. 기본적으로 random salt를 사용하기 때문에 같은 값이라 하더라도, 다른 암호화 값으로 변화할 것이다.
동일한 'test'라는 문자열을 암호화했으나, salt가 달라서  다음과 같이 다르게 암호화 문자열이 생성된다.

```
Jq4AjAaYAjgM7oTJHySLZw==
bjMPhCbaL3fxgg+3KquOoA==
```

Random이 아닌 Fixed Salt Generator를 사용한다면? 같은 암호 문자열은 얻을 수 있을지 모르지만, 덜 안전한 데이터 관리방안이 될 것이다.

게다가, == 연산 이외의 >,< 등의 연산이나 like 와 같은 연산은 사용할 수 없게 될테니 주의하자. (= 설계부터 잘 하자.)
