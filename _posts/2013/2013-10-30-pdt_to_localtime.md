---
layout: single
title: PDT 시간을 local time으로?
date: 2013-10-30 06:02:38.000000000 +09:00
categories:
- python
tags: []
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
거의 5-6년만에 python으로 한 번 변환하는 로직을 짜보게 되었다.
google님 감사합니다.
PDT 시각을 현재 시각으로 변환해 보고자 한다.

약 16시간의 차이가 있기 때문에, 내 시각에서 PDT를 구하려면 -16시간을, 역으로는 +16시간을 연산해주면
되는 아주 간단한 요구사항이다.

본 포스트에서는, now - 16을 하는대신, (이것은 구글에서 pdt now 라는 검색어로 검색해봐도 금방 나오는 값이다.)
PDT 시간을 입력하면 현지 시각으로 변환하는 로직을 작성해 보고자 한다.


```python
from datetime import datetime, timedelta

now = datetime.now()
hours_16 = timedelta(hours = 16)


user_input = raw_input('Enter PDT in yyyymmddHHMMSS format : ')
pdt_time = datetime.strptime(user_input, &quot;%Y%m%d%H%M%S&quot;)
converted_time = pdt_time + hours_16

print converted_time

```

