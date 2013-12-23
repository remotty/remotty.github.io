---
layout: post
title: "github을 통한 협업"
date: 2013-12-23 22:17:44 +0900
comments: true
categories: [remote]
author: 서비큐라
---

github을 통합 협업시 필요한 셋팅입니다.

<!-- more -->

* 원본 repository를 본인 계정으로 fork하고 작업합니다.

* user.name & user.email 설정

  * github 정보와 동일하게 user.name과 user.email을 설정합니다.
{% codeblock lang:bash %}
$ git config user.name “subicura"
$ git config user.email “subicura@subicura.com”
{% endcodeblock %}

* upstream 설정

  * 원본 repository와 소스를 동기화 하기 위해 upstream을 설정합니다.
  * https://help.github.com/articles/syncing-a-fork

* pull request 관련 설정

  * git의 branch를 이용해서 테스트해볼 수 있습니다.
  * https://gist.github.com/piscisaureus/3342247

