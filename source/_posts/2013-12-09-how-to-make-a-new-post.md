---
layout: post
title: "How to make a new post"
date: 2013-12-09 21:28:37 +0900
comments: true
categories: [octopress]
author: 최효성, 서비큐라
---

Octopress 블로그에 글 작성하는 방법을 정리해 봅니다.

<!-- more -->

* Github 저장소를 clone 합니다.

{% codeblock lang:bash %}
$ git clone git@github.com:remotty/remotty.github.io.git
{% endcodeblock %}

* source 브랜치로 checkout 합니다.(디폴트 브랜치가 source 임)

{% codeblock lang:bash %}
$ git checkout source
{% endcodeblock %}

* 아래의 명령을 실행하여 Github Pages로 배포를 위한 준비작업을 합니다.

{% codeblock lang:bash %}
$ bundle install
$ rake setup_github_pages[git@github.com:remotty/remotty.github.io.git]
{% endcodeblock %}

* 위의 작업을 git clone 시 한번만 수행합니다.

* 새로운 글을 작성하기 위해서는 아래와 같이 명령을 실행합니다.

{% codeblock lang:bash %}
$ rake new_post[title-of-post]
{% endcodeblock %}

* 글을 작성하고 확인해 봅니다.

{% codeblock lang:bash %}
$ rake preview
{% endcodeblock %}

* 글을 작성하고 저장한 후 아래의 명령을 실행하면 바로 Github 저장소로 배포됩니다.

{% codeblock lang:bash %}
$ rake deploy 또는 rake push
{% endcodeblock %}

* source 브랜치의 변경내용을 커밋하고 git push 합니다.(이 과정을 반드시 수행해야만 합니다.)

{% codeblock lang:bash %}
$ git add .
$ git commit -m "새글 추가함"
$ git push origin source
{% endcodeblock %}

2014년 1월 30일 수정 보완함. 최효성
