---
layout: single
title: CentOS에 HP MFP M1005 연결 설정
date: 2012-02-03 13:22:37.000000000 +09:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- system
tags: [print, m1005, hp]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
마이크로서버에 설치한 CentOS에 HP MFP M1005를 연결 성공하였다.
역시 google!

HP에서는 hplip(HP Linux Imaging and Printing)을 지원한다.
google에서 검색어로 centos hplip를 입력하였다.

다음과 같은 링크를 얻어냈다.
<a href="http://hplipopensource.com/hplip-web/install/manual/distros/centos.html"></a><a href="http://hplipopensource.com/hplip-web/install/manual/distros/centos.html" target="_blank" title="[http://hplipopensource.com/hplip-web/install/manual/distros/centos.html]로 이동합니다.">http://hplipopensource.com/hplip-web/install/manual/distros/centos.html</a>&nbsp;

옆의 메뉴에 "Supported Printers"를 선택하여, 입력했다.
프린터 종류까지 선택하고, 조회하면

<a href="http://hplipopensource.com/hplip-web/models/laserjet/hp_laserjet_m1005.html"></a><a href="http://hplipopensource.com/hplip-web/models/laserjet/hp_laserjet_m1005.html" target="_blank" title="[http://hplipopensource.com/hplip-web/models/laserjet/hp_laserjet_m1005.html]로 이동합니다.">http://hplipopensource.com/hplip-web/models/laserjet/hp_laserjet_m1005.html</a>&nbsp;

지원된다고 하니 아주 기쁘다. :)
프린터를 연결하자. (USB)

```
yum list | grep hplip
```

를 실행하여 hplip 라이브러리들을 확인하자.
그리고, 관련 hplip 라이브러리들을 설치한다. (yum install)

root 계정으로 바꿔서,

```
hp-setup
```

을 실행시키자.
그 다음부터는 끝까지 Next만 누르면 정말 끝이다.
맨 마지막에 테스트 페이지 인쇄까지 해보면 성공적으로 설치가 될 것이다.

