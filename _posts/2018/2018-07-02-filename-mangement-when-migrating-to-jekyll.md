---
layout: single
title: Jekyll로 블로그 이전시 _drafts에서 _posts로 옮기기
categories:
- life
tags: [jekyll, wordpress]
---

# 배경

앞서, export -> import를 하면서 신경쓰게 된 부분은, 

- 이미지를 모두 assets에 통째로 넣고 관리할 것인가?
  - 분명 같은 파일명을 주게 될 일도 생길테니, images/YYYYMM으로 디렉토리를 만들고 파일을 넣자
- 기존에 export한 포스트명과 이미지 이름들이 모두 인코딩 되어 있다.
  - 나중에 수정하거나, 변경하려면 찾아내기 힘들 것 같다.
  - 그대로 두면, 유지보수 하기 힘들 것 같다.

물론, Git에 올리면 읽을 수도 있겠지만 개발환경에서는 분간해 내기 어렵다.

결국, _drafts에 있는 포스트들을 _posts로 옮기는 작업을 하되, 이름을 변경해가면서 옮겨줘야 한다. (불편)



# 활용

vi에서 본문 편집 -> mv _drafts/원본파일(한글).md _posts/영문파일.md

이런 식으로 작업하려니 번거롭기 그지 없다.



이 때, NERDTreeFind를 사용해 보자. (사전 설치한 플러그인)

## NERDTreeFind 실행 

### 파일 이동

extension mode로부터, m을 누르면 다음과 같이 하단에 interactive menu가 뜬다.

```
NERDTree Menu. Use j/k/enter and the shortcuts indicated
==========================================================
> (a)dd a childnode
  (m)ove the current node
  (d)elete the current node
  (c)opy the current node
  (l)ist the current node
```

여기에서 m을 한 번 더, 누르면 말 그대로 move를 하겠다는 것이고, 그에 따른 또다른 문구가 출력된다.

```
Rename the current node
==========================================================
Enter the new path for the node:
/Users/luran/devwork/blog/_drafts/2013-07-11-spring3-1-hibernate3-jasypt1-8-%ec%97%b0%eb%8f%99%ed%85%8c%ec%8a%a4%ed%8a%b8.md
```

보다시피, 내 경우는 예전에 워드프레스에서 작성했던 포스트들이 그냥 한글이름으로 export되면서, 향후 유지보수를 하려면 본문을 열어봐야 하는 불편함이 생겼다. 이에 _drafts 디렉토리에서 대략 편집한 후, _posts 디렉토리로 옮겨야 하는 수요가 발생한다. 이 때, NERDTreeFind에서 m - m을 눌러서, 파일 이름이나 디렉토리를 편집하고 엔터를 치면, 원하는 디렉토리로 글을 옮길 수 있다.