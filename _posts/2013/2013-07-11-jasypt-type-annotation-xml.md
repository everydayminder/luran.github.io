---
layout: single
title: 'jasypt의 type 선언 : annotation에서 xml로 변경해보기'
date: 2013-07-11 08:30:21.000000000 +09:00
categories:
- development 
tags: [hibernate, java, jasypt]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
annotation 방식의 특성상, 정밀한 설정에는 용이하나 jasypt를 적용하면서 사용할 model 클래스에 매번 적어줘야 한다면,
역시 "불편"할 것이다.
그럴 때는, 역시 xml의 선언이 훨씬 편리할 것이다.

1. annotation 방식
```java
@TypeDef (name="encryptedString", typeClass=EncryptedStringType.class,
 parameters={@Parameter(name="encryptorRegisteredName", value="strongHibernateStringEncryptor")})
```

2. XML 방식
XML로 객체를 만들지 않고, annotation을 사용하여 만든 상황에서 hbm.xml을 만들어 사용하는 식으로 연결을 해보았다.
hibernate-configuration과 hibernate-mapping은 다른 dtd를 사용하기에 별도의 파일로 만들었다.

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
 "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="com.luran.jasypt_test">
 <typedef name="encryptedString" class="org.jasypt.hibernate.type.EncryptedStringType">
 <param name="algorithm">PBEWithMD5AndTripleDES</param>
 <param name="password">jasypt</param>
 <param name="keyObtentionIterations">1000</param>
 </typedef>
</hibernate-mapping>
```

현재 jasypt.org에 안내된 가이드는 1.9 기준이기 때문에, 해당 클래스가
```
org.jasypt.hibernate4.type.EncryptedStringType
```
로 되어있다.  본 테스트에서 사용한 버전은 1.8이기에 위와 같이 변경해줘야 제대로 인식이 된다. (namespace가 변경된 것 같음)

이제 기존에 작성한 hibernate.cfg.xml에서 이 mapping.hbm.xml을 읽어들이도록 변경해주자.
hibernate-configuration.dtd에 보면, mapping attribute가 있다. 이 attribute에 방금 작성한 파일을 지정한다.

```xml
<hibernate-configuration>
 <session-factory>
 ...
 <mapping resource="META-INF/mapping.hbm.xml"/>
 ...
 </session-factory>
</hibernate-configuration>
```

앞서 작성한 annotation 방식 typedef를 주석처리하고, 이제 다시 실행시켜 보자.
동일하게 동작하는 것을 확인할 수 있다.

