---
layout: single
title: "[hibernate] cannot simultaneously fetch multiple bags"
date: 2011-11-16 00:23:58.000000000 +09:00
categories:
- development
tags: [hibernate]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
hibernate를 쓰는 도중, 이 에러가 나오는 경우가 있다.
model 객체 내에서 @OneToMany 표기하고 java.util.List를 사용할 때 일어날 수 있다.
이 경우, fetch 옵션을 LAZY(default) &nbsp;가 아닌 다른 값으로 설정했는지 확인해 보자.
(내 경우, EAGER fetch 옵션을 LAZY로 바꾸어 해결)

자세한 설명 혹은 그 밖의 해결책은,
아래 블로그를 참조하면 더 좋은 정보를 얻을 수 있다.

<a href="http://jroller.com/eyallupu/entry/hibernate_exception_simultaneously_fetch_multiple" target="_blank" title="[http://jroller.com/eyallupu/entry/hibernate_exception_simultaneously_fetch_multiple ]로 이동합니다.">http://jroller.com/eyallupu/entry/hibernate_exception_simultaneously_fetch_multiple </a>

