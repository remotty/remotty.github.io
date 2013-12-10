---
layout: post
title: "install theme"
date: 2013-12-10 23:00:55 +0900
comments: true
categories: [octopress]
author: 서비큐라
---

octopress에 테마를 추가하는 방법은 아주 간단합니다.

<!--more-->

일단 theme 리스트는 다음 링크에서 확인하시고..

https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes

http://www.evolument.com/blog/2013/03/02/top-10-plus-octopress-themes/

설치는 아래와 같이 하면 됩니다.

```
cd [octopress directory]
git clone [theme git주소] .themes/[theme 이름]
rake install['theme 이름']
rake generate
```

zsh 사용시 에러가 발생한다면

```
cd [octopress directory]
git clone [theme git주소] .themes/[theme 이름]
rake "install[theme 이름]"
rake generate
```

다음과 같이 합니다.
