---
layout: single
title: Ubuntu에서 휠마우스 사용하기
date: 2008-07-22 00:15:01.000000000 +09:00
categories:
- system
tags: [tips]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
sudo vi /etc/X11/xorg.conf 를 실행하여
해당 파일을 편집한다.

Section "InputDevice"
&nbsp; &nbsp; Identifier "Configured Mouse"
&nbsp; &nbsp; Driver "mouse"
&nbsp; &nbsp; Option "CorePointer"
&nbsp; &nbsp; Option "Device" "/dev/input/mice"
&nbsp; &nbsp; Option "Protocol" "<font color="#177fcd"><u><strong>ImPS/2</strong></u>"</font>
&nbsp; &nbsp; Option "Buttons" "5"
&nbsp; &nbsp; Option "ZAxisMapping" "4 5"
EndSection

마우스 디바이스 정의 부분 중, Protocol 부분을 위와 같이
바꿔주고 재시작하면, 휠 마우스의 휠이 동작한다.

