---
layout: single
title: Local Zeppelin 설치하기 (OSX)
date: 2018-12-02 09:00:00.000000000 +09:00
categories:
- development
tags: [zeppelin]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
brew가 실행가능하도록 설정되어 있다는 전제하에
```shell
brew install apache-zeppelin
```
위와 같이 간편하게 zeppelin을 설치한다.


# alias 설정
실행 편의를 위해, 다음과 같이 alias를 선언한다.

```
which zeppelin.sh
```
로 zeppelin 설치 위치를 확인하고,

```
alias zeppelin-start="/usr/local/bin/zeppelin.sh start"
alias zeppelin-stop="/usr/local/bin/zeppelin.sh stop"
```
과 같이 본인이 사용하는 shell에 등록한다.

# Zeppelin 실행
zeppelin을 실행하려면, java runtime이 설치되어 있어야 한다.

```shell
zeppelin-start
```
![zeppelin-start.png]({{site.basurl}}/images/201812/zeppelin-start.png)

# Zeppelin 종료
```shell
zeppelin-stop
```

# 환경설정
설치 디렉토리 하위로 탐색해 보면, conf 디렉토리를 발견할 수 있을 것이다.

예) /usr/local/Cellar/apache-zeppelin/0.8.0/libexec/conf

기본 설치를 완료하면, 위 디렉토리 내 설정파일들은 모두 .template의 확장자로 되어있다. (일종의 주석처리)
이 .template 부분만 이름에서 지워주면, 원래 파일에 기록되어 있는 설정 내용들이 적용된다.
바꿔말하면, 원하는 설정을 변경 후 파일이름으로부터 .template을 없애주고 서비스를 재시작하면 변경한 설정이 반영된다.

## 실행 포트 변경
conf/zeppelin-site.xml.template내 아래 설정을 찾아서, 
원하는 포트로 변경 후, 저장하고 zeppelin-site.xml.template를 zeppelin-site.xml로 변경 저장한다.

```xml
<property>
  <name>zeppelin.server.port</name>
  <value>8080</value>
  <description>Server port.</description>
</property>
```

## 계정 권한 설정 (anonymous -> 계정 지정)
기본 설정은 익명계정으로 모든 노트북이 공유되도록 되어 있다.
만약, 계정별로 관리하거나 보호하고자 한다면, 계정과 관련된 설정을 별도 진행해야 한다.
계정 및 보안에 대한 설정은 conf/shiro.ini.template에 기록이 되어 있다.

```
[users]
# List of users with their password allowed to access Zeppelin.
# To use a different strategy (LDAP / Database / ...) check the shiro doc at http://shiro.apache.org/configuration.html#Configuration-INISections
# To enable admin user, uncomment the following line and set an appropriate password.
#admin = password1, admin
user1 = password2, role1, role2
user2 = password3, role3
user3 = password4, role2
```
위의 내용을 참조하여 계정 및 계정의 role을 부여하고 shiro.ini.template을 shiro.ini로 변경하고 서비스를 재시작하면 적용된다.



