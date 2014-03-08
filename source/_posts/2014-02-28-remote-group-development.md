---
layout: post
title: "원격으로 집단 개발하기"
date: 2014-02-28 16:19:31 +0900
comments: true
categories: tmux, madeye
author: 김한기
---

떨어져 있는 사람 혹은 사람들과 같이 개발하고싶을때 어떤 방법을 사용하면 유용할지 소개해드립니다.

<!--more-->
<a name="tableOfContent"></a>
###[목차](#tableOfContent)

 - [머리말](#prologue)
 - [MadEye](#madeye)
 - [tmux](#tmux)
 - [맺는말](#epilogue)

<a name="prologue"></a>
###[머리말](#prologue)

혼자가면 빨리 가지만, 같이가면 멀리간다고 하였습니다. 음 별로 관계없는 말이긴 한데, 집단으로 개발(?)하는 방법에 대해서 알아보겠습니다. 그런데... 사실 방법들이 너무 쉬워서 간단히 Getting Started 느낌일꺼 같네요.

<a name="madeye"></a>
###[MadEye](#madeye)

[MadEye](https://madeye.io/)가 좋은 점은 행아웃과 연동이 된다는 것입니다. 즉 크롬과 같은 웹 브라우저를 통해서 모든게 가능합니다. 코딩, 대화, 거기다 host하시는분의 terminal까지 볼 수 있습니다.

설치는 매우 쉽습니다.

    $ curl https://madeye.io/install | sh

또는 [Node.js](http://nodejs.org/)가 가능하다면,

    $ npm install -g madeye

아마 대부분의 리눅스 혹은 OS X에서 무난하게 설치가 될텐데요, 사용도 아주 간단합니다.

설치가 되었을껀데요, 현시점 최신 버전은

    $ madeye --version
    0.5.1

이고, 이런게 가능하네요.

    $ madeye --help

      Usage: madeye [options]

      Options:

        -h, --help           output usage information
        -V, --version        output the version number
        -c --clean           Start a new project, instead of reusing an existing one.
        -d --debug           Show debug output (may be noisy)
        --trace              Show trace-level debug output (will be very noisy)
        --tunnel [port]      create a tunnel from a public MadEye server to this local port
        --ignorefile [file]  .gitignore style file of patterns to not share with madeye (default .madeyeignore)
        --update             Update MadEye to the latest version and exit.
        -t --terminal        Share your terminal output with MadEye (read-only)

      Run madeye in a directory to push its files and subdirectories to madeye.io.
      Give the returned url to your friends, and you can edit the project
      simultaneously.  Type ^C to close the session and disable the online project.

실행법은 이미 아셨겠지만, 설명해보자면... 일단 집단코딩을 하고싶은 프로젝트의 최상위 디렉터리로 이동을 합니다. 그런다음 `$ madeye`를 입력해주면 끝입니다. 너무 간단하죠...?!

실행 하셨다면 아래 와 같은 모습을 보실 수 있을겁니다.

    $ madeye
    View your project with MadEye at https://madeye.io/edit/dvSjfeYZnEwLxiCjt
    Use MadEye within a Google Hangout at https://madeye.io/api/hangout/dvSjfeYZnEwLxiCjt

위 주소 아무거나를 자주 사용하시는 웹 브라우저를 이용해서 접속해보세요. 보이시나요...!?

madeye를 실행했한 디렉터리 하위의 파일 및 디렉터리들이 전부 보이고, 오른쪽엔 수정 및 저장이 가능한 에디터가 보입니다. 이제 이 주소를 같이 코딩하고 싶은 분들께 알려주시면 됩니다. 그럼 다같이 코딩을 할 수가 있어요.

이번엔 그냥 실행하는게 아니라 -t 옵션을 줘서 실행을 해보겠습니다.

    $ madeye -t
    View your project with MadEye at https://madeye.io/edit/hjS7SRnpqk3XMurB8
    Use MadEye within a Google Hangout at https://madeye.io/api/hangout/hjS7SRnpqk3XMurB8

    ################################################################################
    ## MadEye Terminal    ##########################################################
    ################################################################################

    Anything output from this terminal will be shared within in your MadEye session.
    The shared terminal is read-only. Only you can type commands at this shell
    To exit this shell and end your MadEye session type exit

    Setting prompt to reflect that you are in a MadEye session
    export PS1="$PS1(madeye) "

    export MADEYE_ACTIVE=1
    $ export PS1="$PS1(madeye) "
    $ (madeye)
    $ (madeye) export MADEYE_ACTIVE=1
    $ (madeye)

위와 같이 `-t`옵션을 줘서 생성한 URL은 에디터 아래에 터미널이 보이고, `$ (madeye)`에서 어떠한 내용을 입력했을때 참여하는사람들 모두에게 보여집니다. 행아웃과 같이 사용하면, 얼굴도 볼 수있고, 대화도 할 수있고, 꽤 좋은 에디터로 같이 코딩도 할 수 있고, 거기가 터미널까지...

여기까지 원격에서 집단 개발을 도와주는 도구인 `MadEye`에 대해서 알아봤습니다!

<a name="tmux"></a>
###[tmux](#tmux)

[tmux](http://tmux.sourceforge.net)는 사실 MadEye처럼 원격에서 집단 개발을 위해서 만들어진 도구는 아닙니다. 여기서 설명하려는건 원격에서 집단으로 개발할 때 사용하면 좋을 tmux의 한 기능을 소개하겠습니다.

일단 tmux가 어떤 도구인지에 대한 설명은 잘 설명된 문서를 통해 설명하겠습니다.

 - [TTY 멀티플랙서 tmux](http://blog.outsider.ne.kr/699)
 - [[cookbook] Tmux 입문자님들을 위해 #1 : 설치, 기본사용방법, session](http://nodeqa.com/nodejs_ref/99)

tmux가 뭔지 아셨나요? 이제 tumx를 이용하여 원격에서 집단으로 개발할때 유용한 기능을 소개해드리겠습니다.

일단 tmux를 이용하여 집단 개발을 하려면 개발하려는 대상 프로젝트의 개발환경이 구성되어있는 서버에 ssh, telnet 같은 도구를 이용하여 참여자들이 접속 할 수 있어야 합니다.

기본 개념은 이렇습니다. 최초에 어떤 한 사람이 tmux session을 생성합니다. 그리고 나서 다른 모든 사람들이 해당 session에 붙는 형태가 되는거죠.

먼저  tmux session을 만들어 볼까요?

    $ tmux

끝.

저러면 tmux session이 생겨요. 이제 tmux session이 생겼으니 다른 사람들은 저 session으로 붙으면 됩니다. 어떻게 붙느냐...!?

이렇게 붙으면 됩니다.

    $ tmux attach

끝.

session을 생성하는것, 그리고 붙는 방법 둘다 정말 간단하죠?

조금 더 자세히... 어떻게 된건지 알아보겠습니다.

최초에 session을 생성할때 `$ tmux`를 입력하게되면 서버 소켓 파일 `/tmp/tmux-1000/default`를 생성합니다.

※ tmux man page에 보면 아래와 같이 설명되어있습니다.

    tmux stores the server socket in a directory under /tmp (or TMPDIR if set); the default socket is named default.


`/tmp`디렉터리에 `tmux-1000`이라는 디렉터리가 있는데, 이건 저의 `uid`가 `1000`이기때문에 `tmux-1000` 디렉터리 밑에 `default`가 생성된겁니다.

    $ id
    uid=1000(han) gid=1000(han) groups=1000(han)
 
`uid`가 `1234`인 계정이 `$ tmux`를 한다면 서버 소켓 파일 `default`는 `/tmp/tmux-1234`밑에 생길것입니다...

이하 설명부터는 편의상 모든 사용자는 같은 계정에 로그인한다고 가정합니다.

그리고 나서 다른사람이 똑같이 `$ tmux attach`했을때엔 최초에 누군가 session을 만들당시 생성된 `/tmp/tmux-1000/default`를 이용하여 같은 세션에 붙을 수 있는 겁니다.

여기까지만 보면 하나의 세션만 공유가 가능한 것 처럼 보이지만 그렇지 않습니다.

이제 소개해드릴 옵션은 `-L`옵션과 `-S`옵션 입니다.

먼저 `-L`옵션 부터 살펴보면

    -L socket-name
                   tmux stores the server socket in a directory under /tmp (or TMPDIR if set); the
                   default socket is named default.  This option allows a different socket name to be
                   specified, allowing several independent tmux servers to be run.  Unlike -S a full
                   path is not necessary: the sockets are all created in the same directory.

tmux session을 그냥 생성하게 되면, 즉 `$ tmux`이렇게 입력하면 서버 소켓 파일은 `/tmp/tmux-1000/default`에 생기게 됩니다.

다시말해서 위에서 설명할때 tmux session을 *만든다* 혹은 *생성한다*라는 표현을 사용했는데, 정확히 하자면 *소켓파일을 생성하는 것*이라고 할 수 있죠.

그런데 `-L`옵션을 사용하면 서버 소켓 파일의 이름을 지정할 수 있습니다.

    $ tmux -L remotty

위와 같이 서버 소켓파일을 생성하게되면

    $ pwd
    /tmp/tmux-1000
    $ ls
    default remotty

remotty라는 서버 소켓파일이 생성 되었네요.

이렇게 되면 다른사람들이 붙을때도 `-L`옵션을 사용해서 붙으면 됩니다.

    $ tmux -L remotty attach

붙을땐 `attach`를 뒤에 붙이는 것 빼먹으시면 안되고요.

`-L`옵션도 좋지만 서버 소켓 파일 자체를 다른 곳에 생성 하고 싶을때가 있죠. 그럴때 사용하는 옵션이 `-S`옵션 입니다.

    -S socket-path
                   Specify a full alternative path to the server socket.  If -S is specified, the
                   default socket directory is not used and any -L flag is ignored.

`-S`옵션에 대한 설명은 위와 같습니다. 어떻게 사용해야 할 지 아시겠죠?

    $ tmux -S /tmp/mydir/haha/hoho/remotty

위 명령을 내리면 서버 소켓 파일은 `/tmp/mydir/haha/hoho`디렉터리에 `remotty`이름으로 생기게 됩니다.

`-S`옵션으로 서버 소켓 파일을 생성했다면 다른 사람들은 모르니까 해당 소켓파일의 위치를 알려주고 붙으라고 하면됩니다.

철수: 영희야 tmux 서버 소켓파일은 `/tmp/mydir/haha/hoho/remotty`에 있어.

영희: 아 그럼 내가 `-S`옵션을 사용해서 붙으면 되겠구나!

영희는 이렇게 붙으면 되요.

    $ tmux -S /tmp/mydir/haha/hoho/remotty attach

그런데 만약에 두사람이 같은 소켓파일을 생성하면 어떻게 될까요? 간단히 이렇게 생각해 볼 수 있습니다. 같은 서버에 접속한 두 사람이 `$ tmux`명령을 내립니다. 그럼 위에서 설명한 것과 같이 서버 소켓파일 `/tmp/tmux-1000/default`이 생성될텐데요.

이렇게되면 `/tmp/tmux-1000/default`에 두개의 session이 생기게 되는겁니다. 첫번째로 `$ tmux`를 한 사람은 0번 session에 두번째로 `$ tmux`를 한 사람은 1번 session에서 작업을 하게 되는 겁니다.

자신이 작업중인 세션의 번호는 하단에 초록색 띠가 있는데 가장 왼쪽에 대괄호로 감싸있는 숫자가 세션 번호 입니다.

이렇게 하나의 소켓파일에 여러개의 세션이 생겼고, 누군가 붙으려 아래 명령을 내렸습니다.

    $ tmux attach

어느 세션으로 붙게 될까요? 마지막에 생성된 세션에 붙게 됩니다. 즉 3개의 세션이 `/tmp/tmux-1000/default`에 생성되어있으면 2번 세션에 붙게 됩니다.(왜냐하면 세션번호는 0부터 시작하니까...)

그런데 마지막 세션이 아니라 1번 세션에 붙고 싶다면? `-t`옵션을 사용하면 됩니다

    $ tmux attach -t 1

서버 소켓 파일 `/home/smart/remotty/abc`의 2번 세션에 붙고 싶다면 `-S`와 `-t`를 같이 사용하면 됩니다.

    $ tmux -S /home/smart/remotty/abc -t 2 attach

이렇게 하면 shell 자체를 공동으로 사용할 수 있게 되는 겁니다.

실제로 tmux의 이 기능을 이용하여 [Facebook Django](https://www.facebook.com/groups/django/) 그룹에서는 코딩도장을 운영한다고 합니다. 대화는 행아웃으로 하고, 코딩은 각자의 terminal로 하는거죠!

<a name="epilogue"></a>
###[맺는말](#epilogue)

저희는 **Remote Work**를 지향하는 조직인 만큼 이러한 원격으로 집단 개발을 가능하게 해주는 도구는 참으로 유용합니다. 이 글을 보시는 분들께서도 여기서 소개해 드린 두가지 도구를 이용하여 멀리있는 친구와도 즐거운 개발 하시길 바랍니다.

