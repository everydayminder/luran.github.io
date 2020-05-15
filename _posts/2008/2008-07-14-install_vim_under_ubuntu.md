---
layout: single
title: Ubuntu에서 VIM이 재설치가 안될때
date: 2008-07-14 04:58:40.000000000 +09:00
categories: [tips]
tags: [vim]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Ubuntu 7.10에서 기본적으로 설치되어 있는 Vim은 Tiny 버전이다.
기타 필요한 VIM의 기능을 쓸 수가 없으므로, 재설치는 필수이다.

```
sudo apt-get install vim
```

그러나, 이 재설치가 제대로 안되는 경우가 있다.
이럴 때, Ubuntu 소스의 repository를 제대로 업데이트 해줘야
sudo apt-get update도 된다.


다음의 내용을 보완하자.


/etc/apt/sources.list 파일에 아래의 내용을 추가(혹은 기존 내용은 모두 삭제 or 코멘트 처리)하면 된다.


## Daum Mirror Repository in Korea
```
deb http://ftp.daum.net/ubuntu gutsy main multiverse restricted universe
deb-src http://ftp.daum.net/ubuntu gutsy main multiverse restricted universe
deb http://ftp.daum.net/ubuntu gutsy-backports main multiverse restricted universe
deb-src http://ftp.daum.net/ubuntu gutsy-backports main multiverse restricted universe
deb http://ftp.daum.net/ubuntu gutsy-proposed main multiverse restricted universe
deb-src http://ftp.daum.net/ubuntu gutsy-proposed main multiverse restricted universe
deb http://ftp.daum.net/ubuntu gutsy-security main multiverse restricted universe
deb-src http://ftp.daum.net/ubuntu gutsy-security main multiverse restricted universe

deb http://ftp.daum.net/ubuntu gutsy-updates main multiverse restricted universe
deb-src http://ftp.daum.net/ubuntu gutsy-updates main multiverse restricted universe
```

이 내용을 적용한 후,

```
sudo apt-get update
sudo apt-get install vim 
```
하면 정상으로 설치가 된다.
