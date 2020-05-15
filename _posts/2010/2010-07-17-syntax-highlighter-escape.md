---
layout: single
title: Syntax Highlighter로 인용할 때 괄호 오류 보정하려면
date: 2010-07-17 10:15:53.000000000 +09:00
categories:
tags:
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
Syntax Highlighter로 괄호를 인용할 때,

예를 들어
<pre class="brush:xml">&lt;property name="abc" value="123"/&gt;
</pre>

와 같이 인용하고자 한다면, 막상 인용해보면 위와 같이 표시되는 대신
<pre class="brush:xml">
</pre>

과 같이 표시되고 만다. 

게다가 nested expression이라면, 본인의 의도와는 다른 문서 구조로 표현되어
원치 않는 output을 만나게 될 것이다.

이럴 경우, 다음의 사이트를 사용하여 손쉽게 escape 시키자.
Syntax Highlight 시킬 부분에 변환 코드를 복사해 넣으면 될 것이다.

<a title="[http://accessify.com/tools-and-wizards/developer-tools/quick-escape/default.php]로 이동합니다." target="_blank" href="http://accessify.com/tools-and-wizards/developer-tools/quick-escape/default.php">http://accessify.com/tools-and-wizards/developer-tools/quick-escape/default.php</a>

