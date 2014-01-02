---
layout: post
title: "hub로 커맨드라인에서 Github pull-request 보내기"
date: 2013-12-29 15:31:32 +0900
comments: true
categories: hub github git 
author: nacyot
---

오픈소스를 비롯해 git를 사용해 소스 코드의 버전관리를 하는 경우엔 원격 git 저장소로 Github를 많이 사용합니다. Github는 단순히 git 저장소 역할을 하는 것뿐만 아니라 웹 인터페이스를 통해서 저장소를 관리할 수 있게 도와주며, 소스코드를 공유하고 협업하기 위한 다양한 기능을 제공합니다.

<!--more-->

예를 들어서 다른 사람이 만든 저장소를 자신의 계정에 fork해서 별도로 관리할 수 있고, 이렇게 fork해서 수정한 저장소의 브랜치를 pull-request를 통해서 원래의 저장소에 통합하도록 요청할 수도 있습니다. 이외에도 이슈 관리와 위키를 비롯한 매우 다양한 기능들이 지원됩니다. 이러한 Github의 장점들은 단순히 git을 활용한 좋은 버전관리 시스템이라는 것을 넘어서 프로젝트 관리를 위한 도구로서 Github 서비스를 차별화 시켜줍니다.

하지만 대부분의 기능들은 기본적으로 웹인터페이스로만 사용할 수 있다는 단점이 있습니다. Git의 가장 기본적인 클라이언트 프로그램은 git 명령어로 커맨드라인에서 사용할 수 있습니다. 하지만 Github는 일차적으로 웹서비스로서 부가적인 기능들을 웹을 통해서 제공합니다. GUI나 화면에 익숙한 분들에게는 이러한 면은 또다른 장점이 될 수도 있겠지만, 커맨드 라인에서 직접 git 명령어를 입력하고, 저장소의 상태를 확인하는 사람들에게는 워크 플로우가 웹과 커맨드라인으로 나눠진다는 게 영 장점이지만은 않습니다.

물론 Github에서 제공하는 API를 직접 이용하는 방법도 있긴있겠습니다만, 매우 번거로운 작업입니다. Github에서는 이러한 문제를 해결하기 위해서 저장소 생성, 포크 및 풀리퀘스트 등 주요한 기능을 커맨드라인 인터페이스로 제공해주는 Hub라는 git 명령어의 확장 인터페이스를 제공하고 있습니다. [Hub](https://github.com/github/hub)라는 이름은 git + hub = Github 라는 공식에서 나온 이름도 참 앙증맞습니다.

이 글은 Hub를 설치하고 실제 커맨드라인에서 풀리퀘스트를 보내는 과정을 다룹니다. 기본적으로 Github의 풀리퀘스트 기능 정도는 익숙하다는 걸 전제로(최소한 개념 정도는 이해하고 있다는 전제로) 이야기합니다.

Hub 설치
========

먼저 Hub를 사용하기 위해서는 공식 홈페이지를 참조해 프로그램을 설치해줄 필요가 있습니다. 우선 hub는 git과 ruby에 의존적인 프로그램이므로 시스템에 이러한 프로그램들이 있는지 확인해야합니다.

```sh
$ git -v
git version 1.8.3.2
# git 1.7.3 이상 필요!

$ ruby -v
ruby 2.1.0p0 (2013-12-25 revision 44422) [x86_64-linux]
# ruby 1.8.6 이상 필요!
```

위의 프로그램들이 설치돼있다면 이제 Hub를 설치할 차례입니다. 맥에서는  Homebrew[^brew]를 이용해 hub를 쉽게 설치할 수 있습니다.

[brew]:http://brew.sh/

```sh
brew install hub
```

리눅스 계열에서는 소스 코드를 다운로드 받아 직접 설치할 수 있습니다. 아래 과정을 따라 hub를 설치할 수 있습니다.

```sh
$ git clone git://github.com/github/hub.git
$ cd hub
$ rake install
```

설치 시 ruby의 `rake` 명령어가 지원되어야합니다. `rake` 명령어가 없다면 `gem install rake`로 먼저 rake를 설치해주시기 바랍니다. 또한 `rake install` 명령어 실행시 기본적으로 메인 시스템 상에 프로그램을 설치하므로 `sudo` 등을 붙여 관리자 권한으로 설치해야할 수도 있습니다.

설치가 정상적으로 끝났다면 아래와 같이 `hub` 명령어를 사용할 수 있습니다.

```sh
$ hub --version
git version 1.8.3.2
hub version 1.11.1
```

hub는 `hub` 명령어를 통해서 독자적으로 사용할 수도 있지만 `git` 명령어와 통합해서 사용할 수도 있습니다. `hub alias` 명령어를 실행하면 `git` 명령어와 통합하는 방법을 알려줍니다.

```
# Wrap git automatically by adding the following to ~/.zshrc:
eval "$(hub alias -s)"
```

예를 들어 zsh 사용하고 있다면 `~/.zshrc` 파일에, bash를 사용하고 있다면 `~/.bash_profile` 파일에 `eval "$(hub alias -s)"`을 추가해주면 쉘 초기화 시에 git와 hub 명령어를 통합시켜줍니다. git와 hub 명령어는 기능적으로는 겹치지 않으며, hub가 git를 보완하는 역할을 하고 있으므로 이렇게 사용하더라도 별다른 문제가 되지 않습니다.

이제 설치가 끝났으니 쉘을 재실행 시켜줍니다. git 명령어를 통해서 제대로 alias 되었는지 확인합니다.

```
$ git --version
git version 1.8.3.2
hub version 1.11.1
```

쉘에서 Hub 자동 완성 사용하기
=============================

Hub에서는 쉘에서 명령어 및 옵션 자동 완성을 위한 completion 파일을 제공하고 있습니다. 여기서 zsh을 기준으로 git 명령어의 자동완성을 확장하는 법을 설명합니다. 먼저 hub 자동 완성 파일을 다운로드 받아 적절한 위치(우분투의 경우 기본적으로  `usr/local/share/zsh/site-functions` 디렉토리. 정확한 위치는 각 운영체제 별 zsh 환경 설정 파일 위치에 따릅니다)에 복사해줍니다.

```
$ mkdir ~/src
$ cd ~/src
$ wget https://raw.github.com/github/hub/master/etc/hub.zsh_completion
$ sudo mv hub.zsh_completion /usr/share/zsh/site-functions/_hub
```

zsh을 다시 실행하면 아래와 같이 hub 명령어 사용시 자동완성이 적용되는 것을 알 수 있습니다.

```
$ hub pull<TAB>
pull          -- fetch from and merge with another rep....
pull-request  -- open a pull request on GitHub
```

`git` 명령어와 통합해서 사용중인 경우에는 .zshrc에 아래 라인을 추가해 git 명령어에서도 hub 명령어의 자동완성을 사용할 수 있습니다.

```
compdef git=hub
```

아래와 같이 자동 완성이 적용된 것을 확인할 수 있습니다.

```
$ git pull<TAB>
pull          -- fetch from and merge with another rep....
pull-request  -- open a pull request on GitHub
```

Hub 사용하기
============

Hub의 다양한 명령어에 대해서는 공식 저장소에 간략한 사용법들이 나와있습니다. 여기서는 Hub를 사용해 Github에 저장소를 만들어보고, Github의 핵심 기능중 하나인 풀리퀘스트를 실제로 보내보겠습니다. (단, 여기서는 같은 저장소의 브랜치간에 풀리퀘스트를 보냅니다.)

먼저 Git 저장소를 가진 디렉토리를 하나 생성해줍니다.

```
$ pwd
/home/nacyot/prog/github/nacyot/
$ mkdir pull-request && cd $_
$ git init
Initialized empty Git repository in/home/nacyot/prog/github/nacyot/pull-request/.git/
$ git create
Updating origin
created repository: nacyot/pull-request
```

일반적으로 Git 저장소를 초기화할 때는 `git init` 명령어를 사용합니다. 여기서는 추가적으로 `git create` 명령어를 사용했습니다. 이 명령어는 hub를 통해 확장된 명령어로 현재 git 저장소로 Github에 저장소를 생성해줍니다. `git remote` 원격 저장소 설정이 제대로 되었는지 확인해보겠습니다.

```
$ git remote -v
origin  git@github.com:nacyot/pull-request.git (fetch)
origin  git@github.com:nacyot/pull-request.git (push)
```

이를 통해서 Github [nacyot](http://github.com/nacyot) 계정에 [pull-request 저장소](http://github.com/nacyot/pull-request)가 추가되었다는 것을 알 수 있습니다.

우선 master 브랜치를 활성화시키기 위해 커밋을 하나 해보겠습니다.

```
$ touch README.md
$ git add .
$ git commit -m'Add README.md'
$ git push origin master

Counting objects: 3, done.
Writing objects: 100% (3/3), 221 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:nacyot/pull-request.git
* [new branch]      master -> master
```

이 명령어들은 일반적으로 git에서 사용하는 명령어들입니다. 이제 파일들이 정상적으로 추가되었는지를 확인하기 위해 이 저장소의 웹페이지를 띄워보겠습니다.

```
git browse
```

`git browse` 명령어도 hub 확장으로 현재 디렉토리에 위치한 git 저장소의 원격 저장소를 근거로 Github 페이지를 찾아 바로 웹브라우져를 열어줍니다. 이 명령어를 통해서 따로 웹브라우져를 실행시키지 않더라도 저장소의 Github 페이지를 바로 확인할 수 있습니다.

![repository](/images/2013-12-29-hub-and-pull-request/repository.png)

이제 pull-request 브랜치를 만들고 실제로 풀리퀘스트를 보내보도록 하겠습니다.

```
$ git checkout -b pull-request
$ git touch hello.rb
$ git add .
$ git commit -m'Add hello.rb'
$ git push origin pull-request
```

위의 명령어들 역시 git에서 일반적으로 사용하는 명령어들로 추가적인 설명은 생략하겠습니다. 간단히만 얘기하면 pull-request 브랜치를 만들고 `hello.rb` 파일을 추가하고 Github 저장소에도 추가했습니다. 이제 nacyot/pull-request 에는 master 브랜치와 pull-request 두 브랜치가 존재합니다.

여기서는 pull-request 브랜치에서 master 브랜치로 풀리퀘스트를 보내겠습니다.

```
$ git pull-request
```

`git pull-request` 명령어를 실행시키면 현재 디렉토리의 github 저장소와 브랜치를 기준으로 풀리퀘스트를 보내기 위한 메시지를 입력할 수 있도록 기본 에디터를 실행해줍니다.

```
First pull-request

# Requesting a pull to nacyot:master from nacyot:pull-request
#
# Write a message for this pull request. The first block
# of text is the title and the rest is description.
```

주석을 통해서 풀리퀘스트가 어디로 보내지는지 확인할 수 있습니다. 풀리퀘스트 메시지를 완성하고 저장한 후 에디터를 종료하면 풀리퀘스트가 보내집니다.

```
https://github.com/nacyot/pull-request/pull/1
```

풀리퀘스트를 보내는데 성공하면 위와 같이 풀리퀘스트의 url을 보여줍니다.

![pull-request](/images/2013-12-29-hub-and-pull-request/pull-request.png)

`git pull-request` 명령어는 아래와 같이 좀 더 명시적으로 사용할 수도 있습니다.

```
$ git pull-request -m 'First pull-request' -b nacyot:master -h nacyot:pull-request
https://github.com/nacyot/pull-request/pull/1
```

여기서 `-m` 플래그는 풀리퀘스트 메시지, `-b` 플래그는 풀리퀘스트 목적지, `-h` 플래그는 풀리퀘스트를 보내는 브랜치가 됩니다. `-b`와 `-h` 플래그는 `<계정명>:<브랜치명>` 형식으로 기술하며, 저장소는 작업 디렉토리 저장소를 근거로 자동으로 유추됩니다. 마찬가지로 풀리퀘스트에 성공하면 풀리퀘스트의 url을 보여줍니다.

결론
====

여기까지 git 명령어를 hub로 확장해서 커맨드라인에서 Github 작업을 좀 더 편하게 하는 방법을 소개했습니다. hub는 웹에서 해야하는 귀찮은 일들을 많이 덜어주는 프로그램입니다. Github에서 저장소를 만들고 풀리퀘스트를 보내려고 웹상에서 브랜치 지정해주고 하는 일은 간단한 작업이지만 꽤나 번거롭습니다. 특히 해당 '저장소'를 찾아서 들어가는 일도 반복되면 귀찮기만 한 일입니다. hub와 함께 좀 더 즐거운 Github 라이프가 되길 바랍니다 >_<