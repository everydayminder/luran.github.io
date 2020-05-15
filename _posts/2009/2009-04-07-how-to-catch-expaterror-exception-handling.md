---
layout: single
title: How to catch 'ExpatError exception' (handling)
date: 2009-04-07 00:56:01.000000000 +09:00
categories:
- development
tags: [python]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
ElementTree와 같은 패키지를 사용하여 XML를 파싱하는 경우,
XML 엘리먼트의 짝이 안맞는 등, 유효하지 않은 XML 구성이 탐지되면
ExpatError가 뜨는데,

<div class="txc-textbox" style="border-right:#fe8943 1px dashed;border-top:#fe8943 1px dashed;border-left:#fe8943 1px dashed;border-bottom:#fe8943 1px dashed;background-color:#fedec7;padding:10px;"><font color="#612a03">try:

&nbsp; # XML 연산

catch ExpatError, e:

&nbsp; # do something</font>
</div>


하면, 

NameError: global name 'ExpatError' is not defined라는 에러가 뜬다.


이를 해결하려면,

ExpatError를 catch하는 py 파일의 앞에, scope을 맞춰서




<div class="txc-textbox" style="border-right:#fe8943 1px dashed;border-top:#fe8943 1px dashed;border-left:#fe8943 1px dashed;border-bottom:#fe8943 1px dashed;background-color:#fedec7;padding:10px;"><font color="#8c3c04">from xml.parsers.expat&nbsp; import ExpatError

</font></div>

라고 넣어주자. 그러면, 문제 해결!!


''

While you are using XML packages such as ElementTree, you might want to handle

ExpatError which might be caused by invalid-format or missing tags and so on.


However, you will see another error message saying "NameError: global name 'ExpatError' 

is not defined".&nbsp; What you only have to solve this problem is just add a line on the top of

your code.


That is,


from xml.parsers.expat import ExpatError


It'll work, then.

