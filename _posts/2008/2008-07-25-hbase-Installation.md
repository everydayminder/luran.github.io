---
layout: single
title: HBase 설치/설정하기
date: 2008-07-25 00:31:17.000000000 +09:00
categories:
- system
tags: [hadoop]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Michael 씨가 자세하게 설명했던 HDFS 설정과는 달리,
HBase 설정은 wiki.apache.org/hadoop/HBase에 나와있는 설명이 전부이다.
물론, 웹에서 많은 사람들의 노력의 흔적은 곳곳에 있긴 하지만, 역시 어렵다.
기본적인 수준의 설명이나, 링크의 소개, 해봤더니 잘 되더라는 소감이 대다수이니까.


1. 내 시스템 구성
 1) 앞서 설정한 HDFS(master, slave) -> 2대
 2) HBase -> 1대


2. 미리 준비할 것
 1) hbase1과 master, slave 간 ssh가 미리 설정되어 있어야 함.
   (비밀번호 없이 SSH 인증하기 참조)
 2) hbase1, master, slave는 서로 reachable 해야 함.
  (hbase1는 master랑만 통신해도 될 줄 알았는데.. 아니었음) 


3. 환경 설정
 1) .bashrc에 HBASE_HOME을 설정
 2) /conf/hbase-env.sh에 JAVA_HOME 설정
 3) /conf/hbase-defaul.xml을 /conf/hbase-site.xml로 복사
 4) /conf/hbase-site.xml중, hbase,master 항목과 hbase.rootdir 항목 변경
   - hbase.master -> example.org:60000을 hbase1:60000 으로 설정 
   - hbase.rootdir -> hdfs://example.org:9000/hbase를 master:54310/hbase로
    설정
    (hbase가 뜨는 곳은 hbase1이고, hbase가 사용할 hdfs는 master로 시작됨)
 5) /conf/regionservers는 ${HADOOP_HOME}/conf/slaves와 동일하게 작성함
 6) Hbase가 HDFS를 제대로 쓰도록 하기 위해, hadoop-site.xml이 필요하므로,
   ${HBASE_HOME}/conf에 ${HADOOP_HOME}/conf/hadoop-site.xml을
   복사함.


4. 실행
 1) 띄우기 : ${HBASE_HOME}/bin/start-hbase.sh
   - 성공적으로 뜨면, 로그에 에러가 안 남음
   - jps 결과 : HMaster, HRegionServer가 추가됨
 2) 종료하기 : ${HBASE_HOME}/bin/stop-hbase.sh
   - jps 실행시, HMaster, HRegionServer가 사라짐


* 문제점
 - 내가 실험한 결과, start-hbase.sh 가 정상 작동하지 않는다. HMaster는
  뜨지만, HRegionServer가 정상적으로 뜨지 않아서 쓸 수 없는 상태가 됨.
 - path가 너무 길거나 복잡하면 에러가 생기는 듯 한데, shell을 고쳐서 직접
  실행시키면 뜨기는 한다. 당분간은 이렇게라도 실행시켜봐야할 듯.
 - 정상적으로 동작하면, jps에도 뜨지만, start-base/stop-base 수행시간도 짧다.
  오작동시, 시간이 길어지며 최악의 경우, kill도 감수해야 한다. (vulnerablity)

