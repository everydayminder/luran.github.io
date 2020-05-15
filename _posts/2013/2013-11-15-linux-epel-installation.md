---
layout: single
title: Linux EPEL 설치 (metalink 에러 조치)
date: 2013-11-15 08:08:02.000000000 +09:00
categories:
- system
tags: [linux, epel]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Linux EPEL (Extra Packages for Enterprise Linux)을 설치하면서 겪은 것을 기록으로 남긴다.
L2TP VPN 솔루션 중, XL2TP를 설치하려고 하려고 관련 글들을 찾아보았다.


```
yum install xl2tpd
```

블로그로부터 위와 같이 실행하면 되는 것으로 확인하고, 실행하였으나 설치가 되지 않는다.
기본 설정 상태의 yum repository들에는 xl2tpd가 존재하지 않기 때문이다.

xl2tpd가 존재하는 repository가 어디에 있는지 조사해 보니, Linux EPEL에 있다고 한다.
또한, 이를 위해 EPEL을 설정해 주어야 한다는 것을 확인했다.

```
yum repolist
```

를 실행하면 기본적으로 epel이 아직 보이지 않는다.
다음과 같이 epel을 설정해 주자.

w<b></b>get http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm 후,

```
rpm -Uvh epel-release-6-8.noarch.rpm
```
이후, yum repolist를 실행했더니,
다음 에러가 발생하였다.

<pre>
 Error : Cannot retrieve metalink for repositroy: epel. Please verify its path and try again.
</pre>

여러가지 방법을 시도해 보고, 다음과 같이 조치했더니 동작하였다.

/etc/yum.repos.d/epel.repo 파일을 아래와 같이 편집하였다.

조치 내용은 다음과 같다.
metalink를 참조하는 mirrorlist를 주석처리하고, 그 위의 baseurl을 그대로 사용하도록 주석해제한다.
enabled=1로 설정한다.

```
 [epel]
 name=Extra Packages for Enterprise Linux 6 - $basearch
 baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
 #mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&amp;arch=$basearch
 failovermethod=priority
 enabled=1
 gpgcheck=1
 gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
```

그리고, 다시 yum repolist를 수행하니,
metalink 에러 없이, 정상적으로 repolist에 액세스하였고,
yum install xl2tp 도 수행할 수 있었다.
