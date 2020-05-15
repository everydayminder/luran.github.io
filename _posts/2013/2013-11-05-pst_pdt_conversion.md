---
layout: single
title: pst, pdt time 변환 업데이트
date: 2013-11-05 05:19:23.000000000 +09:00
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
역시 요구사항은 변화한다.

pst, pdt를 옵션에 따라 변환해 볼 수 있도록 다시 한 번 변경해보았다.


```python
#!/usr/bin/python


import optparse
from datetime import datetime, timedelta

def main():
        p = optparse.OptionParser()
        p.add_option('--type', '-t', default=&amp;quot;pdt&amp;quot;)
        p.add_option('--time', '-i')
        options, arguments = p.parse_args()

        # check time type whether it is pst or pdt
        if options.type in ['pst', 'PST'] :
                hours_gap = timedelta(hours = 17)
        else :
                hours_gap = timedelta(hours = 16)

        # get time value from user
        if options.time == None :
                time = raw_input('Enter %s time in yyyymmddHHMMSS : ' % options.type)
        else :
                time = options.time

        # get converted time
        pxt_time = datetime.strptime(time, &amp;quot;%Y%m%d%H%M%S&amp;quot;)
        converted_time = pxt_time + hours_gap

        print 'converted time(%s) : %s' % (options.type, converted_time)

if __name__ == '__main__':
        main()
```
