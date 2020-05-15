---
layout: single
title: YUM local repository 구축 + httpd 연동
date: 2013-11-06 02:22:15.000000000 +09:00
categories:
- system
tags: [yum, repository]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
여러 가지 이유로, local YUM repository를 구축할 이유가 있을 것이다.

본 포스팅에서는 local에 YUM repository를 구축하고, private 네트워크 상에서 http로 yum repository에
액세스할 수 있도록 설정하는 방법을 적는다.

## 원본 rpm 파일 확보
이미 존재하는 repository로부터 rpm 파일들을 ftp, scp 등으로 복사하거나,
설치 이미지로부터 rpm 파일들을 추출하자.
이미지로부터 복사하려면,
www.centos.org에 접속하여 원본 설치용 DVD iso 파일들을 다운로드 한다.

CentOS-6.4-x86_64_bin-DVD1.iso
CentOS-6.4-x86_64_bin-DVD2.iso

파일을 다운로드 하였다.
다운로드한 iso 파일을

```
mount -o loop /mnt/centos CentOS-6.4-x86_64_bin-DVD1.iso
```

와 같이 DVD1, 2에 대해 각각 mount 후,
Package 디렉토리에 존재하는 rpm 파일들을 /srv/repo에 모두 복사한다.

## createrepo 설치
yum repository를 설치하려는 시스템상에서 yum 명령어를 쓸 수 있는 상태라면 (online)

```
yum install createrepo
```
을 실행하는 것이 가장 쉬운 방법이다.
그러나, offline이라서, yum을 통해 패키지를 설치하지 못하는 상황이라면 좀 돌아가야 한다.
createrepo 패키지를 설치해야 하는데,
이는 앞서 복사한 createrepo rpm 파일을 설치하면 된다.
초기 환경 구성중이라면, createrepo rpm 설치시 사전 필요한 의존 라이브러리들이 아직 준비되지 않았을 것이다.

```
rpm -ivh createrepo-0.9.8-4.el6.noarch.rpm
```
를 수행하여 createrepo를 설치해보자.
내 경우는, 두 개의 라이브러리가 없다고 각각 리포트 되었다.
(createrepo 설치 시도시, python-deltarpm이 없다고 에러 뜸, python-deltarpm 설치 시도시, deltarpm이 없다고 에러 뜸)

결국, 해당 라이브러리들을 역순으로 설치해주면 된다.
```
rpm -ivh  deltarpm-3.5-0.5.20090913git.el6.i686.rpm
rpm -ivh  python-deltarpm-3.5-0.5.20090913git.el6.i686.rpm
rpm -ivh createrepo-0.9.8-4.el6.noarch.rpm
```

한편, 이미 createrepo가 설치된 다른 머신에서

```
rpm -q --requires createrepo
```
명령을 수행해보면, 위의 라이브러들이 필요하다는 정보를 확인할 수 있다.

## createrepo 실행

```
createrepo /srv/repo
```
rpm 들을 /srv/repo에 복사했다면, 위의 명령어를 수행한다.
그러면, /srv/repo/repodata/repomd.xml까지 생성이 된다.

## local yum 설정 (파일 기반)
/etc/yum.repos.d 디렉토리에 임의의 repo 설정 파일을 생성한다.
내 경우는 luran.rep를 생성하고, 다음과 같이 기록하였다.

```
[luran]
name = luran repository
baseurl = file:///srv/repo
gpgcheck = 1
enabled = 1
```

여기까지 설정했다면, 일단 로컬 파일 기반으로 local yum repository를 사용할 수 있는 준비가 된 것이다.

## 확인
```
yum repolist
```
를 실행하여, 새로 추가한 yum repository가 에러 메시지 없이 뜨는지 확인한다.
현재 상태에서는 기본으로 설정된 다른 repository까지 함께 사용하게 되므로,
기본 설정으로 잡혀있는 다른 repository를 disable 시킨다.

/etc/yum.repos.d/CentOS-Base.repo 파일을 열고,
enabled =1 을 모두, enabled = 0으로 변경하고 저장한다.
(없으면, enabled=0을 추가 기록)

다시, yum repolist를 실행하면, 기존에 보이던 yum repository는 이제 보이지 않고,
내가 신규 설치한 yum repository만 보일 것이다.

## local yum 동작 확인
```
yum repolist
yum list | grep vim
```

등과 같이 실행하여 에러 없이 동작하는 것을 확인한다.

## Apache httpd 설치/설정
이제 apache httpd web server를 설치한다.
local file 기반의 yum repository를 사용하는 것을 넘어, 이제는 동일 네트워크 상에 존재하는 (이 yum repository에 액세스 가능한)
서버들도 이 repository를 사용할 수 있도록 http로 서비스를 설정하자.

지금까지 설정한 local yum repository가 정상적으로 동작한다면,
yum 명령어로 httpd를 설치할 수 있을 것이다.

```
yum install httpd
```
그러나, 내 경우는 설치시 에러가 났다. sign이 되어 있지 않다는 에러가 떴는데,
이는 앞서 mount한 원본 CentOS-DVD 이미지에 있는 파일을 사용하면 된다.

해당 파일을 복사해 와서,

```
rpm --import RPM-GPG-KEY-CentOS-6
```
와 같이 실행하고,

```
yum install httpd
```
를 재실행하자.

이제, /repo라는 별칭으로 /srv/repo를 액세스 할 수 있도록
/etc/httpd/conf.d/repo.conf 파일을 생성하고,

```
Alias /repo "/srv/repo/"
```
를 추가한다.

```
service httpd start
```
를 실행한 후,
브라우저를 사용하여 또는 lynx를 사용하여

http://localhost/repo/repodata/repomd.xml
을 접근해보자.

해당 내용이 뜬다면 일단 성공했다.
만약, selinux가 enabled 된 상태라면,
웹 접속이 되지 않을 것이다.

getenforce 명령을 실행하여,
selinux 설정 상태를 확인하고, 변경한다.

```
setenforce 0
```

영구적용 하려면,
/etc/sysconfig/selinux (/etc/selinux/config)

```
SELINUX=enforcing
```
을
```
SELINUX=disabled
```

로 바꿔 저장해 놓는다.
만약, http_proxy 등이 잡혀 있다면, local yum proxy를 웹으로 접속시 접속 실패할 것이다.
따라서, unset http_proxy를 해주거나,

```
export no_proxy=ip
```
로 추가 선언해줘서,
local에서 테스트시, 정상적으로 될 수 있도록 해 둔다.

## repo 설정 변경
이제 yum.repos.d/luran.repo 파일의 baseurl을 파일타입으로부터 http 형으로 변경하자.
http://youripaddress/repo

## 동작 확인

```
yum clean all
yum repolist
yum list | grep vim
```

## Apache list 확인
/etc/httpd/conf/httpd.conf 파일을 열고,
아래의 내용을 추가로 편집하여 리스트 등의 기능이 정상 동작하도록 설정을 변경하고,

```
<Directory>
  Options Indexes FollowSymLinks Includes ExecCGI
  AllowOverride All
  Order deny,all
 Allow from all
</Directory>
```

다시

```
service httpd restart
```
하였다.

```
lynx http://localhost/repo
```

또는 웹브라우저에서
http://localhost/repo
를 실행하여, 해당 rpm들이 정상적으로 리스팅 되는지 확인한다.

## 타 서버에서 접속 허용을 위한 방화벽 설정
iptables 설정을 변경하여, 80 포트로 외부 접속을 허용하여 다른 서버에서도 해당 repository를 http 기반으로
사용할 수 있도록 변경한다.

## 타 서버에서의 repository 등록
앞서 로컬에서 repository 설정을 repo 파일을 추가하여 했던 것과 동일하게 진행하면 된다.
해당 서버용 repo파일을 /etc/yum.repos.d 내에 생성하고, baseurl을 지금 구축한 yum repository 의 주소로
http://youripaddress/repo
형태로 적어준다.

해당 시스템 역시 offline이라면, 다른 base repo의 설정은 disabled 상태로 바꿔서 저장해 준다.
