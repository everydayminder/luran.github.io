---
layout: single
title: hudson + maven project 설정하기
date: 2011-11-29 10:56:00.000000000 +09:00
categories:
- development
tags: [hibernate, hudosn, maven, java]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Hudson에 maven 프로젝트를 설정해 보고자 한다.
기존에 작성된 maven 프로젝트가 있다고 가정하자.

<b>1. Hudson에 연동할 기존 maven 프로젝트 준비 또는 신규 maven 프로젝트 준비</b>
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile29-uf-110c974b4ed4b77527b80a.png" class="aligncenter" width="676" height="355" alt="" />

즉, 이렇게 생겼다고 가정하자. pom.xml이 존재하는 maven 프로젝트 파일이다.
src 폴더에는 원본 소스파일과 함께 테스트 코드도 작성해 두었다. 

<b>2. Hudson, 새 프로젝트 만들기</b>
다음과 같이 새 프로젝트를 만든다. Maven 연계 프로젝트를 선택한다.
(물론, 이를 위해 Hudson - Maven 관련 플러그인이 설치되어 있어야 할 것이다.)
 
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile25-uf-205076464ed4b8e7460c3e.png" class="aligncenter" width="700" height="269" alt="" />

<b>3. 소스 위치 지정하기</b>
CVS, SVN, Git 등을 활용하여, 소스를 가져올 위치를 지정한다.

<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile30-uf-1432cc414ed4ba233540bd.png" class="aligncenter" width="700" height="307" alt="" />
위치를 지정하고, 필요하면 계정정보 설정까지 마친다.

주기적으로 build 필요성 여부를 검사하고, build 시키기 위해 trigger 옵션을 설정한다.
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile27-uf-17167c4f4ed4bb12011a02.png" class="aligncenter" width="700" height="150" alt="" />

일단, 여기까지 하고 save하자.
프로젝트가 정상적으로 생성되었다면, 다음과 같이 나타날 것이다.

<p style="margin:0;">일단, Build Now를 클릭하고, 정상적으로 설정되었는지 검사하자.<img src="{{ site.baseurl }}/images/201111/cfile2-uf-162edb384ed4bc3d2433b6.png" class="aligncenter" width="476" height="365" alt="" />

테스트 코드까지 정상적으로 실행이 되었다면, 다음과 같이 나타날 것이다.
<p style="margin:0;"><img src="{{ site.baseurl }}/images/201111/cfile22-uf-161c024f4ed5b65d2b7ed5.png" class="aligncenter" width="576" height="414" alt="" />

다음에는, hudson에서 사용하는 플러그인을 설정해 봐야겠다.

