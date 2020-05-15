---
layout: single
title: hudson - emma와 연동하기 (2/2)
date: 2010-07-19 01:29:01.000000000 +09:00
categories:
- development
tags: [ci, coverage, emma, hudson, jenkins, java]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
지난 글에 설정한 바대로 ant task를 정상적으로 진행했다면,
ant task로 emma.report 태스크를 수행했을 때, coverage.html과 coverage.xml이 생성되었을 것이다.
참고로, 생성된 coverage.html을 살펴보자.

<img src="{{ site.baseurl }}/images/201007/cfile21-uf-14063e154c43d64d9d230f.png" class="aligncenter" width="700" height="340" alt="" />
해당 패키지의 구성중, 클래스/메소드/블럭/라인 기준으로 어느 정도가 test로 커버 되고 있는지를 보여준다.
패키지 이름을 클릭하면,

<img src="{{ site.baseurl }}/images/201007/cfile27-uf-154eb0164c4505c6c5bdec.png" class="aligncenter" width="700" height="307" alt="" />
패키지에 포함된 클래스들이 나타나고, 이 클래스들이 어느 정도 test로 커버되고 있는지 보여준다.
이 중, 아무 클래스나 또 클릭하게 되면,
<img src="{{ site.baseurl }}/images/201007/cfile26-uf-203a22174c43d6c0953f01.png" class="aligncenter" width="700" height="675" alt="" />
클래스내의 메소드들이 test로 어떻게 커버되고 있는지 현황을 자세하게 보여주게 된다.
이 결과물은, 별도의 ant task를 수동으로 실행시켜 얻은 결과물이므로,
이제 hudson의 task에 연동하여, 자동으로 build가 되면 emma test를 수행하자.
그러면, hudson이 매 빌드마다 결과물을 자동으로 생성하여 dashboard로부터 볼 수 있도록 설정할 것이다.

"Hudson 관리 > Manage Plugins"를 클릭한다.
Available 탭을 클릭하여 살펴보면, 

<img src="{{ site.baseurl }}/images/201007/cfile22-uf-133176174c43dc73c1c751.png" class="aligncenter" width="700" height="35" alt="" />
과 같이, Emma plugin 설치에 대한 체크박스가 있다.
이를 체크하고, 우측 하단 구석에 숨어있는 "Install" 버튼을 클릭하자.

성공적으로 Emma plugin을 설치했다면,
다음 화면을 보게 될 것이다.

<img src="{{ site.baseurl }}/images/201007/cfile7-uf-135ba7184c43dcf0c8a074.png" class="aligncenter" width="686" height="208" alt="" />
설명 문구에 뜬 내용처럼, hudson을 재시작시켜 emma plugin이 정상 동작하도록 하자.
hudson에 다시 접속해보면, 외형상 변화는 아무 것도 나타나지 않는다. 
(이제부터 hudson에 emma 설정을 해야 하니까)

프로젝트명을 클릭하고, Configure를 선택한다.
Post-build Actions 부분에 "Record Emma coverage report"가 생성된 것을 확인할 수 있다.

이 체크박스를 클릭하면, 세부 설정 화면이 생긴다.
<img src="{{ site.baseurl }}/images/201007/cfile4-uf-114a24204c43de257bbd22.png" class="aligncenter" width="700" height="150" alt="" />

예전에 ant task로 테스트 했을 경우 coverage.xml이 생성된 경로를 기억하는가?
XML report 경로를 넣는 곳에,
프로젝트이름/report/emma/coverage.xml 등과 같은 형식으로 기록해 준다.
Health reporting은 그냥 디폴트 값으로 사용하기로 한다. 즉, 비워두면 디폴트값이다.
아직 emma report를 생성하는 ant task가 동작하도록 설정하지 않았기 때문에,
build 되더라도 emma report가 자동으로 생성되지는 않을 것이다.

따라서, Post-build Actions 위쪽에 있는 Build > Invoke Ant 옵션을 고치자.
Targets에
all.emma.report 
라고 써주고, build.xml에 all.emma.report를 추가해 보자.

<pre class="brush:xml"><!-- target : all with emma.report -->
	<target name="all.emma.report" depends="clean,emma.report" 
		description="builds the project, excutes tests, and writes emma report"/>
</pre>

이제, 해당 프로젝트의 왼쪽 메뉴탭을 보면,
<img src="{{ site.baseurl }}/images/201007/cfile29-uf-1132b0184c43e56f063abc.png" class="aligncenter" width="166" height="252" alt="" />
와 같이, Coverage Report가 생성됨을 확인할 수 있다.
이 Coverage Report를 클릭해보자.

<img src="{{ site.baseurl }}/images/201007/cfile2-uf-141428164c43e5a01c4813.png" class="aligncenter" width="700" height="306" alt="" />
coverage trend를 표시하는 그래프가 graphics로 나타나는 것을 확인할 수 있다.
이 화면에서도 패키지명을 클릭해 보자.

<img src="{{ site.baseurl }}/images/201007/cfile10-uf-127aa6194c43e5f701ced4.png" class="aligncenter" width="700" height="211" alt="" />
임의의 클래스도 클릭해보자.

<img src="{{ site.baseurl }}/images/201007/cfile8-uf-135344154c43e60e227d12.png" class="aligncenter" width="700" height="510" alt="" />
사실, 내용은 앞서 살펴보았던 html과 다를 것이 없다. 
다만, 별도의 ant task를 사용자가 실행하지 않아도, 소스를 수정후에 SVN 서버에 commit 하는 것만으로도 충분하다는 것이다.

지금까지 한 작업은 다음의 과정을 자동화한다.
* 소스를 개발/수정 후 commit하면,
* 자동으로 소스를 컴파일하고, 
* 테스트 코드도 컴파일해서 테스트를 시행하고,
* 테스트 결과를 보여준다. 
* 또한 이 테스트의 적용 범위(coverage)가 어느 정도인지 검사해준다.

이로써, hudson에 ant task를 활용하여 emma를 연동해 보았다.

