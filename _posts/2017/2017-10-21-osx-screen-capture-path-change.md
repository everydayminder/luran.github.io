---
layout: single
title: OSX에서 스크린캡처 경로 변경하려면
date: 2017-10-21 08:09:03.000000000 +09:00
categories:
- system
tags: [tips, osx]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
터미널에서,

```
defaults write com.apple.screencapture location 변경경로
killall SystemUIServer
```
