---
layout: single
title: CentOS 6 (64)에 BitNami로 RedMine설치
date: 2012-03-21 15:29:31.000000000 +09:00
categories:
- system
tags: [bitnami, centos, redmine]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
BitNami로 RedMine을 설치하면, Apache, MySQL, Subversion이 함께 설치된다.
이전에 해당 프로그램들이 개별적으로 설치되어 있다면, RedMine을 직접 설치해야 하나,
그렇지 않다면, 편하게 BitNami를 이용하여 설치해볼만도 하다.

### 1. 필요 라이브러리 설치
gcc, gcc++, zlib, zlib-dev를 미리 설치해두자.

```shell
yum install gcc gcc++ zlib zlib-dev
```
나의 경우, zlib~를 설치하지 않고 진행했다가, 나중에 gem을 실행시킬 때, 런타임에러가 발생하였다.
만일, gem을 실행시킬 때 zlib관련 so 라이브러리가 없다는 에러가 발생한다면,
zlib와 zlib-dev가 설치되어 있는지 확인하고, ruby를 다시 설치하자.

### 2. ruby 설치
다음과 같이 설치하였다.
```shell
wget ftp://ftp.ruby-lang.org/pub/ruby/1.8/ruby-1.8.7-p174.tar.gz
```
압축 풀고, 해당 디렉토리로 이동하여

```shell
./configure
make
make install
```

### 3. rubygems 설치
다음과 같이 설치하였다.
```shell
wget http://rubyforge.org/frs/download.php/73883/rubygems-1.4.2.zip
```

압축 풀고, 해당 디렉토리로 이동하여
```shell
ruby setup.rb
gem install rubygems-update
```
### 4. rails와 rack 설치
내 경우는 rails만 설치해도, rack도 함께 설치되었다.
```shell
gem install rails -v=2.3.5
```

위와 같이 실행하면, 다음과 같이 설치되는 것을 볼 수 있다.

> Fetching: rake-0.9.2.2.gem (100%)
> Fetching: activesupport-2.3.5.gem (100%)
> Fetching: activerecord-2.3.5.gem (100%)
> Fetching: rack-1.0.1.gem (100%)
> Fetching: actionpack-2.3.5.gem (100%)
> Fetching: actionmailer-2.3.5.gem (100%)
> Fetching: activeresource-2.3.5.gem (100%)
> Fetching: rails-2.3.5.gem (100%)
> Successfully installed rake-0.9.2.2
> Successfully installed activesupport-2.3.5
> Successfully installed activerecord-2.3.5
> Successfully installed rack-1.0.1
> Successfully installed actionpack-2.3.5
> Successfully installed actionmailer-2.3.5
> Successfully installed activeresource-2.3.5
> Successfully installed rails-2.3.5
> 8 gems installed

> Installing ri documentation for rake-0.9.2.2...
> Installing ri documentation for activesupport-2.3.5...
> Installing ri documentation for activerecord-2.3.5...
> Installing ri documentation for rack-1.0.1...
> Installing ri documentation for actionpack-2.3.5...
> Installing ri documentation for actionmailer-2.3.5...
> Installing ri documentation for activeresource-2.3.5...
> Installing ri documentation for rails-2.3.5...
> Installing RDoc documentation for rake-0.9.2.2...
> Installing RDoc documentation for activesupport-2.3.5...
> Installing RDoc documentation for activerecord-2.3.5...
> Installing RDoc documentation for rack-1.0.1...
> Installing RDoc documentation for actionpack-2.3.5...
> Installing RDoc documentation for actionmailer-2.3.5...
> Installing RDoc documentation for activeresource-2.3.5...
> Installing RDoc documentation for rails-2.3.5...>

### 5. RedMine 설치
```shell
wget http://bitnami.org/files/stacks/redmine/1.3.2-0/bitnami-redmine-1.3.2-0-linux-x64-installer.bin
chmod +x *.bin
./bitnami-redmine-1.3.2-0-linux-x64-installer.bin
```
위와 같이 실행하면, 몇 가지 옵션을 Y/N로 확인한다.
답하고 나면, 알아서 설치 완료.

### 6. 설치 확인
설치된 디렉토리를 보면, apache2, git, mysql, perl, php, ruby, sqlite, subversion, mysql 등이 설치되어 있는 것을 확인할 수 있다. 실행은 ctlscript.sh로 가능하며, 실행 파라미터는 start/stop/restart/status이다.

```shell
ctlscript.sh start
```

를 실행하여 띄우고, 방화벽 설정도 마친 후에 웹 브라우저로 접속해보자.
BitNami라고 사이트가 뜨면 정상이다. 혹은, http://아이피/redmine 로 접속하면, 곧바로 RedMine으로 접속 가능할 것이다.
