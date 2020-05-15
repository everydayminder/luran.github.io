---
layout: single
title: git 실행시 libcurl.dll에서 curl_multi_timeout 에러 발생시
date: 2014-03-28 00:08:09.000000000 +09:00
categories:
- system
tags: [git]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
에러가 발생하는 원인/상황은 다양할텐데,
내 경우는 다음과 같이 조치하였다.

1. http://curl.haxx.se/libcurl/ 에 방문하여, libcurl.dll을 다운로드한다.
2. c:\Windows\System (32bits), c:\Windows\SystemWow64 (64bits)에
   위 단계에서 다운로드한 libcurl.dll을 복사한다.


  


  

