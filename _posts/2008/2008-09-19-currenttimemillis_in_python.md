---
layout: single
title: 어떤 구문의 소요시간을 milliseconds로 확인하려면
date: 2008-09-19 02:11:45.000000000 +09:00
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
java에서의 System.currentTimeMillis()에 해당 하는 것이 Python에서는 
어떤 것이 있을까 궁금했다.

현재, 확인한 바로는
time 패키지의 time.clock()이 유용할 듯 하다.
이것의 특징은 애플리케이션이 실행된 시점으로부터 계산되는 count라는 점이다.
즉, 상대적인 stop watch라는 점.

<table style="border-collapse:collapse;" width="460" bgcolor="#eaf4cf" cellpadding="1" cellspacing="1">
<tbody>
<tr>
<td style="border:1px solid rgb(218,218,218);" width="100%">begin = time.clock()
... do something ...
end = time.clock()
elapsed = end - begin
</td>
</tr>
</tbody>
</table>

과 같이, 소요된 시간을 구할 수 있다. 기본 단위가 seconds로 리턴이 되기 때문에
milliseconds는 0.xxx로 표현되는 값으로부터 얻을 수 있다.

반면, datetime.now()을 사용하게 되면, HH:MM:SS:ss의 형태로 값을 얻게 되는데,
difference를 구하더라도 마찬가지의 형태로 연산 결과를 산출한다.

<table style="border-collapse:collapse;" width="460" bgcolor="#eaf4cf" cellpadding="1" cellspacing="1">
<tbody>
<tr>
<td style="border:1px solid rgb(218,218,218);" width="100%">begin = datetime.now()
.. do something ..
end = datetime.now()
elapsed = end - begin

elapsed.seconds
elapsed.microseconds
</td>
</tr>
</tbody>
</table>

결과를 second와 microseconds 속성으로 얻을 수도 있는데, microseconds라고 호출한다고 해서, seconds 이상의 값을 microseconds (milliseconds) 단위로 자동 환산해주지는 않는다. 따라서, milliseconds 범위내에서의 시간 소요를 측정할 경우는 유용하나 1초를 초과하는 범위의 측정에서는 적절치 않을 수도 있다.

