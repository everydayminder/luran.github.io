---
layout: single
title: tar.gz로부터 Jira(you host)를 CentOS에 설치하기
date: 2014-03-29 15:53:33.000000000 +09:00
categories:
- system
tags: [jira]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Jira를 installer (bin)로부터 설치하는 대신,tar.gz로부터 설치하더라도 사실 별 차이는 없다.
다음과 같이 진행하였다.

1. admin 권한을 가진 계정으로 진행 (예: jira)
2. jira (tar.gz)를 atlassian으로부터 다운로드한다.
3. 원하는 디렉토리에 해당 파일을 압축 해제한다.
   예) /opt/atlassian-jira 

   압축을 해제하고 디렉토리 구조를 bin 파일을 설치한 것과 비교해 보면,
   사실 동일하다. tomcat도 함께 패키징 되어 있다.

   atlassian 사이트내 가이드에 따르면, 설치 방법이 installer를 사용하거나,
   지금 방법과 같이 tar.gz로부터 설치하는 것을 권장한다고 한다.

   war 파일을 통한 설정은 그다지 권하지 않는다고 한다.

4. jira의 데이터가 저장될 디렉토리를 생성하고, 환경변수에
   JIRA_HOME으로 설정하자. 이 디렉토리도 jira 계정과 동일하게 owner 설정이 되어 있어야 한다.

5. jira를 설치한 디렉토리의 bin을 열어보면
   start-jira.sh로 시작시키고, stop-jira.sh로 종료시키면 된다.
