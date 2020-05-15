---
layout: single
title: 64bit CentOS 6에 lame, mplayer 설치하기
date: 2012-02-23 15:06:26.000000000 +09:00
categories:
tags:
- centOS
- lame
- Mplayer
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
<b>[lame 설치]</b>
1. http://odiecolon.lastdot.org/el5/noarch/ 사이트로부터 최신 odiecolon-repositories를 다운 받는다. (wget)
2. 다운받은 odiecolon-repositories rpm을 설치한다.

```
rpm -Uvh odiecolon-repositores*rpm
```

3. lame 패키지를 설치한다.
```
yum install lame
```

<b>[mplayer 설치]</b>

인터넷 글 중, 내 설정에 딱 맞게 한 번에 맞아 떨어진 것은


<a href="https://www.centos.org/modules/newbb/viewtopic.php?topic_id=34461&amp;forum=56">https://www.centos.org/modules/newbb/viewtopic.php?topic_id=34461&amp;forum=56</a>&nbsp;

에서 찾을 수 있었다.

rpmforge/ repoforge 등을 설정하지 말고,

다음과 같이 하라는 것이 요지이다.


/etc/yum.respo.d에 atrpms.repo를 만들고 다음과 같이 적어 넣는다.

```
[atrpms]
name=Fedora Core $releasever - $basearch - ATrpms
baseurl=http://dl.atrpms.net/el$releasever-$basearch/atrpms/stable
gpgkey=http://ATrpms.net/RPM-GPG-KEY.atrpms
gpgcheck=1
enabled=1
```

이제 mplayer를 설치하자
```
yum install mplayer
```
