---
layout: single
title: Eclipse내 JUnit static import를 위한 설정
date: 2013-07-10 10:45:23.000000000 +09:00
categories:
- development
tags: [junit, eclipse]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
jUnit4 이상을 사용하면서, hamcrest의 도움 없이는 온전한(가독성이 좋은) test case들을 작성하기 어렵다.
그런데, 이 jar들을 매번 import하자니 어렵고, 겨우 import해 놓으면 ctrl + shift + o를 누르는 순간 증발하기 일쑤다.
많은 개발자들도 이러한 점을 힘들어하여, 각 블로그에 여기저기 많이 남겨놓으셨다.
나도 이참에 기록을 남긴다.

1. static import할 라이브러리 등록하기
- Windows > Preferences > Java > Editor > Content Assist > Favorites 메뉴로 간다.
- New Type을 클릭하고 다음과 같은 라이브러리를 등록한다.

```java
org.hamcrest.CoreMatchers.*
org.hamcrest.Matchers.*
org.junit.Assert.*
```

2. *로 대체하기 기능 설정
- Windows > Preferences > Java > Code Style > Organize Imports 메뉴로 간다.
- Number of static imports needed for .* (몇 번 이상 겹치면 *로 처리해줄까?) 의 숫자를 변경한다. (예 : 99 -> 1)

