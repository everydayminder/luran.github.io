---
layout: single
title: OptionParser를 활용한 commandline parsing 하기
date: 2008-10-02 06:38:21.000000000 +09:00
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
Optik (aka optarse) 를 사용하면, python 애플리케이션 개발시 커맨드라인 옵션/
파라미터를 파싱하기 쉬워진다.

예를 들어서, 
"실행파일명 --config 파일명" 으로 구성되는 형태의 옵션을 만들고 싶다면,

다음과 같이 간단하게 구성할 수 있다.
```python
from optik import OptionParser

def main():
    usage = "usage: %prog [options] arg"
    parser = OptionParser(usage)
    parser.add_option("-c", "--config", action="store", type="string", dest="filename" )
    (options, args) = parser.parse_args()
    
    if options.filename:
        print "cfg : %s" % options.filename
```

add_option() 내에 사용된 옵션에 대해 첨언하자면,
ㅁ -c : 한개의 대쉬 문자 뒤에 지정되는 문자는 1 캐릭터여야 한다
ㅁ --config : 풀로 표기하는 옵션은 두개의 대쉬 문자뒤에 표기한다.
   (두 개의 위치는 바뀌어도 상관없다. 표기만 제대로 한다면)
ㅁ action="store" : c/config  옵션 뒤에 오는  argument를 활용하기 위해 지정함.
ㅁ type="string" : 문자열을 받도록 해줌
ㅁ dest="filename" : 나중에 c/config 등등 다양한 형태로 지정된 옵션을 통합하여,

  호출할 때 사용됨. 즉, c/config 옵션은 "filename"이라는 옵션으로 통합하여 
  인지한다.
  action = "store" 로 저장한 파일이름은 options.filename의 속성값으로 활용한다.

기본적으로 -h/ --help 옵션은 deploy된다. 즉 테스트파일에 --help 옵션을 줘서
실행하면,
```
 Usage: OptionTester.py [options] arg

Options:
  -h, --help            show this help message and exit
  -c FILENAME, --config=FILENAME
 ```

와 같이 결과가 나온다.
여기에서 참고할 만한 내용은, 대쉬의 개수에 따라 동작이 다르게 될 수도 있다는 점.

동일한 결과를 내려면.
```
-c FILENAME
--config=FILENAME
--config FILENAME
```
의 형태로 활용할 수 있다.
보다 자세한 설명 및 활용 방법은 
<a href="http://optik.sourceforge.net/">http://optik.sourceforge.net/</a> 에서 확인할 수 있다.
