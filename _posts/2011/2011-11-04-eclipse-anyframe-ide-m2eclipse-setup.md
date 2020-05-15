---
layout: single
title: Eclipse + Anyframe IDE + m2eclipse 설치하기
date: 2011-11-04 12:39:49.000000000 +09:00
categories:
- development
tags: [anyframe, eclipse, m2eclipse]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Eclipse Indigo를 설치하고, maven 기반 프로젝트를 해보려고 한다.
Eclipse Indigo에서 m2eclipse 지원이 좋아졌다고 하여 그대로 Anyframe과 연동해보려 하였으나, 아직 버전이 호환되지 않는 것 같다.
Indigo를 설치하고 m2e를 설치하고 나니, Anyframe IDE를 설치할 때 에러가 발생하였다.
그래서, 그냥 Anyframe 사이트에 안내된 대로 m2eclipse를 설치하기로 한다.

<b>1. m2eclipse 사이트 등록</b>
Available Software에 m2eclipse 사이트와 m2eclipse extras를 지정한다.
* m2eclipse 사이트 :http://m2eclipse.sonatype.org/sites/m2e
* m2eclipse extras 사이트 :http://m2eclipse.sonatype.org/sites/m2e-extras

Anyframe 사이트도 지정한다.
* Anyframe 사이트:http://dev.anyframejava.org/update

<b>2. Anyframe IDE 설치</b>
방금 등록한 anyframe 사이트를 선택하면, 아래와 같이 목록이 뜰 것이다.
Anyframe IDE를 선택한다.

<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile2-uf-117a624d4eb3d9bf291bae.png" class="aligncenter" width="700" height="573" alt="" />
m2eclipse와 m2eclipse extras를 설치하기 위해, "Contact all update sites during install to find required software"를 체크한다. 설치를 마치면, Eclipse를 restart하라고 창이 뜬다. (restart 하자.)

<b>3. Anyframe IDE 정상 설치 여부 확인</b>
Help > About Eclipse를 클릭하면, 다음 창이 뜬다.
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile8-uf-146ca2484eb3da6a30259e.png" class="aligncenter" width="561" height="335" alt="" />
Installation Details를 선택한다.

<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile7-uf-19050a494eb3da98320a2b.png" class="aligncenter" width="700" height="739" alt="" />

Anyframe IDE가 설치된 것을 확인할 수 있다.

<b>4. Anyframe IDE 환경 설정</b>
Windows > Preferences에서 Anyframe IDE 탭에서 maven 관련 정보를 입력한다.
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile22-uf-207982454eb3db7537d237.png" class="aligncenter" width="634" height="553" alt="" />

<b>5. Server 정보 입력</b>
Windows > Preferences > Server를 설정한다.
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile22-uf-162ef1504eb3dc0f09c7c7.png" class="aligncenter" width="635" height="555" alt="" />
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile30-uf-142ef1504eb3dc0f0ad2a9.png" class="aligncenter" width="521" height="550" alt="" />
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile8-uf-152ef1504eb3dc0f0be547.png" class="aligncenter" width="524" height="551" alt="" />

이것으로 Anyframe 사용을 위한 기본 환경 준비를 마쳤다.
File > New > Other를 선택하면, Anyframe프로젝트를 만들 수 있다.
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile10-uf-201c404b4eb3dc9d107d7f.png" class="aligncenter" width="524" height="499" alt="" />

다음에는 Anyframe 프로젝트를 만들어 보자.

