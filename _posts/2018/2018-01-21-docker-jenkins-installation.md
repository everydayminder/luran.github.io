---
layout: single
title: Docker용 Jenkins 설치/ 기본 환경설정
date: 2018-01-21 19:49:29.000000000 +09:00
categories:
- system
tags: [docker, jenkins, windows, kitematic]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Docker - Kitematic으로부터 Jenkins 이미지를 클릭하여 Jenkins를 쉽게 설치할 수 있다.
<img class="alignnone wp-image-826 size-large" src="{{ site.baseurl }}/images/201801/kitematic_screenshot-1024x876.png" alt="" width="770" height="659" />

일단, 설치 후 실행 시키면, 다음과 같은 창이 뜬다.
<img class="alignnone wp-image-829 size-large" src="{{ site.baseurl }}/images/201801/jenkins_1-1024x926.png" alt="" width="770" height="696" />

이후, General, Hostname/Ports, Volumes, Network, Advanced 등의 탭으로부터 정보를 확인하거나, 원하는 값으로 설정을 변경하여 사용할 수 있다.
Hostname/Ports 탭은 아래와 같이 나오는데,
<img class="alignnone wp-image-830 size-full" src="{{ site.baseurl }}/images/201801/jenkins_2.png" alt="" width="844" height="427" />

로컬서버의 32769 포트로 접속하면, Jenkins에 접속가능하다는 뜻이다.
이 주소로 접속해 보면, 다음과 같은 초기화면이 뜬다.

<img class="alignnone wp-image-831 size-full" src="{{ site.baseurl }}/images/201801/jenkins_3.png" alt="" width="911" height="508" />
화면에 뜬 바와 같이, unlock 정보를 확인해보자.

```
/var/jenkins_home/secrets/initialAdminPassword
```
내용을 확인해보자.
앞서 확인한 Kitematic의 관리 패널에서 EXEC를 클릭한다.

<img class="alignnone size-full wp-image-832" src="{{ site.baseurl }}/images/201801/kitematic_control.png" alt="" width="219" height="59" />

콘솔 창이 뜨면,
```
cat /var/jenkins_home/secrets/initialAdminPassword
```
를 실행하고, 결과값을 복사하여 Jenkins를 unlock 하는 창에 복사해 넣자.
unlock이 성공하면 다음과 같이 창이 뜰 것이다.

<img class="alignnone wp-image-833 size-full" src="{{ site.baseurl }}/images/201801/jenkins_unlock_success.png" alt="" width="860" height="505" />

우선 Install suggested plugins를 클릭하여, 권장 플러그인들을 설치하자.
<img class="alignnone wp-image-834 size-full" src="{{ site.baseurl }}/images/201801/jenkins_plugin_install_progress.png" alt="" width="865" height="491" />

계정 정보까지 모두 설정 완료하면, 기본 설치는 완료된다.
<img class="alignnone wp-image-836 size-full" src="{{ site.baseurl }}/images/201801/jenkins_install_complete.png" alt="" width="954" height="423" />

