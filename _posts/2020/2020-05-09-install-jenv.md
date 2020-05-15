---
layout: single
title: jenv 설치하기 
date: 2020-05-09 13:00:00.000000000 +09:00
categories:
- development
tags: [jenv, java]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
# 여러 버전 java 

사실 개발하면서 Java의 버전을 계속 바꿀 일은 별로 없는 것 같다.

프로젝트마다 바꿀 일도 없고, 회사 정책이 바뀌지 않는한 기존의 버전을 사용할 가능성이 높다.

또한, 기존에 java 8을 사용한 프로젝트라면 특별한 이유가 없는 한 Java8을 유지보수할 가능성이 높다.



Java의 버전 릴리즈 주기가 6개월로 짧아지면서, 여러 버전을 설치하고 사용하고자 할 때 jenv를 사용하면 좋을 것이다. (Python처럼) 3년 주기의 Java LTS 버전을 감안하면, production에서는 현재로서는 Java11이 유용한 선택지가 될 것이다.

![jenv.png]({{site.baseurl}}/images/202005/jenv.png)

https://en.wikipedia.org/wiki/Java_version_history



## jenv: Java용 Pyenv

Python 개발 환경에 익숙한 사람들은 아마 이미 pyenv에 익숙할 것이다.

그리고, java 환경에도 동일한 tool이 있다. 이름도 사용법도 유사하다. pyenv를 써온 사람들이라면 그대로 쓰면 된다. (약간의 명령어 차이와 사용법 차이는 있겠지만)



### Homebrew, Cask 설치

```shell
brew update && brew upgrade brew-cask && brew cleanup && brew cask cleanup
```

### Java 설치

```
brew cask info java
brew cask install java
```

2020/5 기준, java14가 설치된다.

### jenv 설치/ 환경 설정

```shell
brew install jenv

if which jenv > /dev/null; then eval "$(jenv init -)"; fi

jenv add /Library/Java/JavaVirtualMachines/openjdk-14.0.1.jdk/Contents/Home
```

설치/ 설정 후 jenv versions를 실행해 보면,

```
$ jenv versions
  system
  1.8
  1.8.0.192
* 14.0 (set by /Library/Java/JavaVirtualMachines/openjdk-14.0.1.jdk/Contents/Home/.java-version)
  14.0.1
  openjdk64-14.0.1
  oracle64-1.8.0.192
```

만약, LTS 버전인 Java 11을 설치하고자 한다면, 다음과 같이 해보자.

AdaptOpenJdk를 사용하기 위해

```
brew tap AdoptOpenJDK/openjdk
brew cask install adoptopenjdk11
```

설치 후, /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home 를 jenv에 추가해 준다.

```
$ jenv add /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home 

$ jenv versions
  system
* 1.8 (set by /Users/luran/.jenv/version)
  1.8.0.192
  11.0
  11.0.7
  14.0
  14.0.1
  openjdk64-11.0.7
  openjdk64-14.0.1
  oracle64-1.8.0.192
```



### jenv 실행

```
jenv 0.5.2
Usage: jenv <command> [<args>]

Some useful jenv commands are:
   commands    List all available jenv commands
   local       Set or show the local application-specific Java version
   global      Set or show the global Java version
   shell       Set or show the shell-specific Java version
   rehash      Rehash jenv shims (run this after installing executables)
   version     Show the current Java version and its origin
   versions    List all Java versions available to jenv
   which       Display the full path to an executable
   whence      List all Java versions that contain the given executable

See `jenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/hikage/jenv#readme
```

jenv만 실행하면, pyenv와 마찬가지로 가능 옵션을 알 수 있다.

* versions: 사용 가능한 옵션들 보기
* version: 현재 동작하는 옵션 보기
* local: 특정 디렉토리 이하 버전 적용
* global: 전체 버전 적용
* shell: 현재 shell에만 적용



```
jenv global 1.8
jenv local 11.0
jenv shell 14.0
```

등과 같이 설정하여 사용하면 된다.

특히 local로 설정하게 되면, .java-version에 버전 정보가 저장된다.

만약, global 설정과 local 설정을 조합하여 버전을 제어하려고 하는데, 너무 상위 디렉토리에서 local로 선언한다면 원하는 제어를 하기 어려워 질 수 있다. 

global/ local 제어가 뜻대로 제어가 되지 않는다면, local을 너무 상위 디렉토리에서 선언하지는 않았는지 확인해 볼 필요가 있다. .java-version 파일을 삭제하면, 기존에 선언한 버전 정보가 삭제된다.
