---
layout: single
title: Installer방식으로 Jira (you host)를 CentOS에 설치하기
date: 2014-03-23 22:56:06.000000000 +09:00
categories:
- system
tags: [jira]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
매우 유명한 issue tracker인 Jira를 어쨌든 한 번 써보고 싶었다.
atlassian에서 운영하는 버전으로 말고, 직접 내가 설치하는 버전으로 구매했다.

You host 버전을 구매하며, 10 users 미만이면 $10가 든다.
그 옆에 Jira Agile은 $20가 든다고 한다.

그래서, 이왕이면 스크럼도 되고 칸반도 된다고 써있는 Jira Agile로 간다!
조금 더 읽어보니, Jira Agile은 Jira의 plugin이고, 구 Greenhopper라고 써 있다.

각각 $10로 총 $20 들었다.

1. 설치 환경
  - OS : Centos6
  - JDK : OpenJDK7
  - MySQL

2. JDK7 설치

```
yum install java-1.7.0-openjdk java-1.7.0-openjdk-devel
```
을 설치한다.

.bash_profile내, JDK의 path를 잡아주었다.
```
JAVA_HOME=/usr/lib/jvm/java-openjdk
PATH=$PATH:$JAVA_HOME/bin
export PATH
```

설치/설정 후, shell상에서 java -version, javac -version
을 수행하여 java가 잘 설치되었는지 확인한다.

3. DB 설치/설정
HSQLDB 대시 MySQL을 향후 설정할 것이므로,
MySQL을 설치하고, 기초 설정을 하자.

```
yum install mysql-server
```

를 수행하여 mysql 서버를 설치하자.
설치가 완료되면,


```
service mysqld start
```
를 실행시킨다.
친절하게 안내문이 나오는데,

```
/usr/bin/mysqladmin -u root password 'new-password'
```
를 수행하여, root에 새 비밀번호를 할당해 주라고 한다.
혹은 위의 방법 대신,

```
/usr/bin/mysql_secure_installation
```

을 실행하라고 안내문도 뜬다.
mysql_secure_installation을 실행시켜 보자.
이것저것 interactive하게 묻는다.

```
* Set root password? [Y/n]
* Remove anonymous users? [Y/n]
* Disallow root login remotely? [Y/n]
* Remove test database and access to it? [Y/n]
* Reload privilege tables now? [Y/n]
```

3. Jira 다운로드/설치
atlassian에서 jira를 다운로드한다. bin/ tar.gz/ war 형식
등이 있으니 원하는 것으로 다운로드 하면 된다. (그러나, 결국 어떤 형식으로 받아도 나중에 확인해보면 web application일 뿐이다.)

<b></b>get http://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-6.2.1-x64.bin
로부터 jira를 임의의 디렉토리에 다운로드 한다. (어차피 세부 디렉토리 설정 등은 추후 인스톨 과정에서 설정할 것이다.)
만약, bin 파일이 아닌 다른 형식의 파일을 다운로드했다면, 직접 디렉토리도 생성하는 등의 관리를 해줘야 할 수도 있다.

admin 권한이 없는 계정으로부터 jira를 설치하려고 하면, 시스템 권한이 없다고 에러를 보게 될 것이다.
따라서, 계정에 시스템 권한을 주고 설치를 하거나, 나중에 후조치를 하면 될 것이다.

설치의 편의상 우선 root로 진행하였다.

```
chmod +x atlassian-jira-6.2.1-x64.bin
./atlassian-jira-6.2.1-x64.bin
```

다음의 메시지가 나오면서 설치가 진행된다.
내 경우는, Advanced Option으로 설치를 진행시켰고,
디렉토리는 각각 아래와 같이 명시했다.

```
Unpacking JRE ...
Starting Installer ...
Mar 22, 2014 6:29:55 PM java.util.prefs.FileSystemPreferences$1 run
INFO: Created user preferences directory.
This will install JIRA 6.2.1 on your computer.
OK [o, Enter], Cancel [c]

Choose the appropriate installation or upgrade option.
Please choose one of the following:
Express Install (use default settings) [1], Custom Install (recommended for advanced users) [2, Enter], Upgrade an existing JIRA installation [3]

Where should JIRA 6.2.1 be installed?
[/opt/atlassian/jira]

Default location for JIRA data
[/var/atlassian/application-data/jira]
/var/atlassian/jira/data
Configure which ports JIRA will use.
JIRA requires two TCP ports that are not being used by any other
applications on this machine. The HTTP port is where you will access JIRA
through your browser. The Control port is used to Startup and Shutdown JIRA.
Use default ports (HTTP: 8080, Control: 8005) - Recommended [1, Enter], Set custom value for HTTP and Control ports [2]
8090
Use default ports (HTTP: 8080, Control: 8005) - Recommended [1, Enter], Set custom value for HTTP and Control ports [2]

JIRA can be run in the background.
You may choose to run JIRA as a service, which means it will start
automatically whenever the computer restarts.
Install JIRA as Service?
Yes [y, Enter], No [n]
```

4. 계정/권한 변경
jira 계정을 만들고 owner를 변경해 준다.

```
useradd jira
passwd jira
chown -R jira:jira /opt/atlassian
chown -R jira:jira /var/atlassian
```

5. 실행
jira를 설치한 곳의 bin 디렉토리에 가면, tomcat 관련 파일들이 보이고 익숙해 보이는 shell 들이 눈에 띈다.
startup.sh를 실행시키고 브라우저에서 접속 여부를 확인하자.

첫 실행시 다소 시간은 걸리겠지만, 잠시 후 다음과 같은 화면이 뜨면 일단 설치는 된 것이다.
이후 세부 설정을 하자. (다음 포스트)

<img src="{{ site.baseurl }}/images/201403/welcome.png" alt="welcome" width="300" height="283" class="alignnone size-medium wp-image-542" /></a>

