---
layout: single
title: mySQL drbd primary 설정적용
date: 2008-12-22 01:56:03.000000000 +09:00
categories:
- system
tags: [drbd, mysql]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
<strong>1. 10.0.0.1에 접속
</strong>

<strong>2. cat /proc/drbd</strong>
&nbsp;&nbsp; -&gt; primary인지 secondary인지 정보가 출력됨

<strong>3. drbdadm primary mysql&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 
</strong>&nbsp;&nbsp; -&gt; primary로 전환


<strong>4. mount /dev/drbd0 /drbd/
</strong>

<strong>5. service mysql start</strong>

