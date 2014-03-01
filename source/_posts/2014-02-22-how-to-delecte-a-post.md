---
layout: post
title: "How to delecte a post"
date: 2014-02-22 16:25:14 +0900
comments: true
categories:
author : 임경호
---


"How to make a post"라는 포스팅은 있음에도 포스팅을 삭제하는 방법이 없어서
제가 한번 정리해보겠습니다.
<!--more-->
만들어진 remotty.github.io의 디렉토리에서

"$ ls source/_posts"를 통해 지우고자 하는 파일 명을 확인 후

"$ git rm source/_posts/지우고자 하는 파일 명" 을 통해 파일을 지운다.

"$ git status"을 통해 상태를 확인하고,
"$ git commit -m "메세지 내용""으로 로깅을 하고

"$ git push"를 통해 소스를 완전히 지운다.

"$ rake push"를 통해 렌드링을 통해 메인 페이지에 포스팅을 지운다.

"$ open http://blog.remotty.com"을 통해 지워졌는지 확인 후 안됬을 경우
"$ rake deploy" 하고, "$ rake gen_deploy"를 완료 후 다시
메인 홈페이지를 확인해봅니다.

간단합니다.