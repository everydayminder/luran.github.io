---
layout: single
title: war 배포시 root context로 하려면?
date: 2012-01-01 15:27:33.000000000 +09:00
categories:
- development
tags: [context, deploy, root, tomcat, war]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
1. Web Application 배포
$CATALINA_HOME/webapps에 web application 디렉토리를 통쨰로 복사하면 배포가 된다.
또한, war 파일을 복사해도 배포가 된다. (설정이 잘 되어 있다면) tomcat이 war 파일을 압축 풀고,
배포하는 것을 확인할 수 있다.

2. Context 설정
만약, webapps/test 라는 디렉토리가 있다면, context명은 test이다.
배포를 하기 위해, web application 형태의 test 디렉토리를 통째로 복사했을 수도 있고,
test.war 파일을 통해 배포했을 수도 있다.

이 때, context명을 $CATALINA_HOME/conf/server.xml에, 또는 $CATALINA_HOME/conf/context.xml에
지정할 수도 있다. (context 내용으로 docBase는 어느 디렉토리로 하고, path는 어디로 하겠다고 옵션을 준다.)
그러나, 이렇게 tomcat의 설정을 직접 변경하면, 설치하고자 하는 tomcat에 종속적이 된다. 
(새로운 곳에 설치하려면, tomcat 설정을 잘 잡아줘야 된다. 그래서, 실수가 발생한다.)

3. 배포시 Context를 함께 배포하자
웹 애플리케이션을 만들 때, webapps/META-INF/context.xml을 작성하자.
그러면, 내가 만든 context.xml이 tomcat에 배포될 때, 함께 설치된다.
내가 만든 context.xml이 $CATALINA_HOME/conf/${hostname}/${context-name}.xml로 복사될 것이다.

만약, META-INF/context.xml에 path="/test"라고 표기했다면,
$CATALINA_HOME/conf/.../test.xml이 생성되었을 것이고,
이로 인해, 사용자는 /test 라는 context를 호출할 수 있게 될 것이다.

4. root context로 배포하려면?
그런데, 만약 root context로 배포하려면 위의 설정만으로는 부족하다.
즉, META-INF/context.xml을 작성하고, path를 "/"로 표기한다 하더라도 root context로 인식되지 않는다.
$CATALINA_HOME/webapps에 가보면, ROOT 디렉토리를 발견할 수 있다.
이것처럼 배포하자.

즉, 내가 배포할 web application의 디렉토리 이름이 ROOT가 되도록 하자.
war로 배포하려면, ROOT.war (대문자를 지켜주자)로 배포하면 될 것이다.

결국, $CATALINA_HOME/webapps/META-INF/context.xml내,
docBase="ROOT"로, path="/"로 적어놓고 배포하면 된다.

정리하면,
- META-INF/context.xml을 활용할 것
- 루트 context로 배포하려면, ROOT.war로 배포할 것


