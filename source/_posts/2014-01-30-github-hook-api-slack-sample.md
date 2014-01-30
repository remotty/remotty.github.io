---
title: "깃허브(Github) 후크 API와 Slack에 알림 전달하기"
date: 2014-01-30 17:30:00 +0900
author: nacyot
profile: 후크 프로그래머(29)
---

후크(hook)는 특정 이벤트나 작업이 진행될 때 자동적으로 다른 스크립트를 실행시켜줍니다. 깃(Git)에서는 기본적으로 후크를 지원하고 있습니다. 저장소 폴더의 `.git/hooks`에서 샘플 스크립트와 사용할 수 있는 후크 이벤트들을 확인해볼 수 있습니다.

깃허브([Github][github])에서도 이러한 후크 기능을 지원하고 있으며 깃허브와 연동된 부분에 대한 이벤트를 추가적으로 지원하고 있습니다. 대부분의 경우 서비스 후크 기능을 통해서 다른 서비스와의 통합을 쉽게 할 수록 지원하고 있으며, 재미있게도 이렇게 다른 서비스와 통합하는 부분도 공개가 되어있어 관심이 있으시면 [실제 코드][github-service]를 확인해볼 수도 있습니다. 특히 현재는 웹후크(Webhook) 기능을 추가되어 깃허브에서 서비스 후크를 지원하지 않는 서비스와도 중간에서 매개할 수 있는 서버나 통합기능이 있다면 얼마든지 활용가능합니다.

<!--more-->

후크란 일반적인 API와는 반대 방향으로 작동합니다. 예를 들어 보통 API를 호출하면 어떤 정보를 되돌려줍니다만, 후크는 등록이 되어있으면 어떤 이벤트가 발생할 때 거꾸로 깃허브에서 내가 등록한 Webhook URL로 정보를 보내줍니다. 여기서는 깃허브 Hook API를 조작하는 방법에 대해서 간략히 살펴보고 하나의 예제로 깃헙에서 보내주는 웹후크 알림을 처리할 수 있는 간단한 [Padrino][padrino] 서버를 만들어 [슬랙(Slack)][slack]라는 협엄&채팅 서비스로 알림을 보내는 과정을 다뤄보겠습니다.

[github]: http://github.com
[github-service]:https://github.com/github/github-services
[padrino]: http://www.padrinorb.com/
[slack]: https://slack.com/

## 깃허브(Githbu) 후크 API ##

* [Hooks | GitHub API][github-api-hooks]

[github-api-hooks]: http://developer.github.com/v3/repos/hooks/

API의 자세한 사항은 깃허브 API 문서에서 확인할 수 있습니다. 여기서는 깃허브에서 지원하는 이벤트 종류와 웹후크를 추가했을 때 어떤 이벤트들이 추가되는지 살펴보겠습니다.

### 지원하는 후크 이벤트 종류 ###

* push : 저장소에 푸쉬가 들어왔을 때 발생. 기본 이벤트
* issues: 이슈가 열리거나 닫혔을 때 발생.
* issue_comment: 이슈에 코멘트가 달렸을 때 발생.
* commit_comment: 커밋에 코멘트가 달렸을 때 발생.
* create: 저장소나 브랜치, 태그가 추가되었을 때 발생.
* delete: 브랜치나, 태그가 삭제되었을 때 발생.
* pull_request: 풀리퀘스트가 열리거나 닫혔을 때, 동기화 되었을 때 발생.
* pull_request_review_comment: 풀리퀘스트 리뷰 안의 커밋에 커멘트가 달렸을 때.
* gollum: 위키가 업데이트되었을 때 발생.
* watch: 사용자가 저장소를 와치했을 때 발생.
* release: 릴리즈가 추가되었을 때 발생.
* fork: 저장소가 포크되었을 때 발생.
* member: Organization의 저장소가 아닐 때 멤버가 추가되면 발생.
* public: 저장소가 비공개에서 공개로 전환되었을 때 발생.
* team_add: 저장소에 팀이 추가되었거나 변경되었을 때 발생.
* status: API를 통해 커밋의 상태가 변경되었을 때 발생.
* deployment: API를 통해 저장소의 새로운 배포가 생성되었을 때 발생.
* deployment_status: API를 통해서 저장소의 특정 배포의 상태가 변경되었을 때 발생.

이벤트가 발생했을 때 깃허브에서 보내주는 내용은 [Event Types][events]에서 자세히 확인할 수 있으며 배포에 관한 부분은 [Deployments API][deploy]를 확인하시기 바랍니다.

[events]: http://developer.github.com/v3/activity/events/types/#deploymentevent
[deploy]: http://developer.github.com/v3/repos/deployments/#list-deployment-statuses

### 저장소에 웹후크 등록하고 상태 확인하기 ###

깃허브에서 저장소에 웹후크나 서비스 후크를 등록하기 위해서는 저장소의 관리 권한이 있어야합니다. 먼저 저장소의 오른쪽에 보이는 Settings 메뉴에 들어갑니다.

![Github settings][settings]

[settings]: /images/2014-01-30-github-hook-api-slack-sample/settings.png

들어와서 왼쪽에 보시면 Service Hooks라는 메뉴를 찾을 수 있습니다. Service Hooks 누르면 Github에서 바로 통합 가능한 서비스 리스트들을 전부 확인할 수 있습니다. 채팅 서비스인 [Hipchat][hipchat]을 비롯해, 빌드 서비스인 [CircleCI][circleci], [Travis][travis], 코드 매트릭스 관리 서비스인 [Code Climate][code_climate], 테스트 커버리지 리포트 서비스인 [Coveralls][coveralls]등 다양한 서비스를 지원하고 있습니다. 여기서는 맨 위에 있는 Webhook URLs를 사용하겠습니다.

[hipchat]: http://hipchat.com/
[circleci]: https://circleci.com/
[travis]: https://travis-ci.org/
[code_climate]: https://codeclimate.com/?v=original
[coveralls]: https://coveralls.io/

![Webhook][webhook]

[webhook]: /images/2014-01-30-github-hook-api-slack-sample/webhook.png

Webhook URLs를 클릭하시면 이벤트가 발생했을 때 정보를 받을 URL을 지정할 수 있습니다. 아직 후크 메시지를 받아 처리할 수 있는 서버가 없으므로 여기서는 서버를 등록하면 어떤 식으로 메시지가 오는 지, 어떤 이벤트들을 등록되어있는지 보여드리도록 하겠습니다. Webhook URLs에 `notifier.nacyot.com`라는 Slack에 깃허브 저장소의 변경사항을 전달해주는 어플리케이션을 등록했다고 가정해보죠. 

깃허브에서 저장소에 등록된 후크 정보를 확인하는 API URL은 다음과 같습니다.

```
https://api.github.com/repos/<계정이름>/<저장소이름>/hooks
```

이제 `curl`을 통해서 Github에 Hook가 어떻게 등록되어있는지 요청을 보내보도록하겠습니다. `-u` 플래그 뒤로는 인증을 할 계정이름을 넣어줍니다.

```
Commend > curl -u nacyot https://api.github.com/repos/nacyot/slack_notifier/hooks
Enter host password for user 'nacyot':
[
  {
    "url": "https://api.github.com/repos/nacyot/bbapi/hooks/1829382",
    "test_url": "https://api.github.com/repos/nacyot/bbapi/hooks/1829382/test",
    "id": 1829382,
    "name": "web",
    "active": true,
    "events": [
      "push",
    ],
    "config": {
      "url": "notifier.nacyot.com",
      "content_type": "form",
      "insecure_ssl": "1"
    },
    "last_response": {
      "code": 504,
      "status": "timeout",
      "message": "Service Timeout"
    },
    "updated_at": "2014-01-28T01:29:02Z",
    "created_at": "2014-01-26T02:27:38Z"
  }
]
```

다음과 같은 응답이 되돌아 옵니다. 이는 현재 등록된 후크의 URL과 어떤 이벤트가 등록되어있는지를 비롯한 여러가지 정보를 담고 있습니다. 특히 주목할 부분은 `id`와 `events`그리고 `url` 부분입니다. 웹에서 지정한대로 정상적으로 등록이 된 걸 알 수 있습니다. 단, 웹에서 등록을 하면 기본 이벤트인 `push` 이벤트밖에 등록이 되지 않습니다. 그렇다면 이슈에 관련된 이벤트나 위키가 수정되었을 때 알림을 받고자 한다면 어떻게 해야할까요?

여기서는 마찬가지로 `curl`을 사용해 새로운 이벤트를 등록해보겠습니다. 여기서 등록할 이벤트는 `issue`, `issue_comment`, 그리고 위키 업데이트를 알려주는 `gollum` 이벤트입니다.

```
curl -u nacyot https://api.github.com/repos/nacyot/slack_notifier/hooks/1829382 -X PATCH -d '{"add_events": ["issue", "issue_comment", "gollum"] }'
```

위에서 후크 정보를 조회했던 `curl` 정보를 참고로 적당한 형태로 바꿔주시기 바랍니다. 특히 여기서는 `/hooks/` 뒤에 앞서서 조회했던 후크의 `id` 값을 집어넣어줘야합니다. 다시 처음 url로 후크 정보를 조회해보면 정상적으로 이벤트들이 추가된 것을 알 수 있습니다.

```
...
    "events": [
      "push",
      "issue",
      "issue_comment",
      "gollum"
    ],
...
```

이제 저장소에서 이슈와 관련 이벤트가 발생하거나 위키에 페이지가 추가되거나 업데이트될 때 알림이 오게 됩니다. 이번엔 실제로 알림이 오는 것을 확인해보겠습니다. 위키 페이지를 하나 생성해보겠습니다.

![Creating Wiki page][wiki]

[wiki]: /images/2014-01-30-github-hook-api-slack-sample/wiki.png

이번엔 `notifier.nacyot.com`에 요청이 들어오는지 확인해보겠습니다.

```
07:15:35 web.1  |   DEBUG -      POST (1.6938s) / - 200 OK
07:15:35 web.1  | 192.30.252.54 - - [30/Jan/2014 07:15:35] "POST / HTTP/1.1" 200 - 1.6956
```

정상적으로 들어오네요. 네, 앞서서 이야기했듯이 이 서버가 해주는 역할은 깃허브로부터 후크를 받고 이를 Slack이라는 채팅 서비스에 연결해주는 역할을 합니다.

![Slack Room][github_slack]

[github_slack]: /images/2014-01-30-github-hook-api-slack-sample/github_slack.png

채팅방에도 메시지가 잘 들어오는 것을 알 수 있습니다. (시간 차이는 시간 설정 때문에 9시간 차이가 나서 그렇습니다)

## Slack과 깃허브 저장소 이벤트 통합 ##

약간 순서가 거꾸로된 듯한 느낌이 들기도 합니다만, 여기서부터는 위에서 다룬 Slack에 메시지를 전달하는 서버에 대해서 다루도록 하겠습니다. 위에서 다룬 이벤트의 종류만 봐도 알 수 있습니다만, 의외로 Github에서는 다양한 이벤트들을 지원하고 있다는 것을 알 수 있습니다. 그리고 이러한 이벤트들이 깃허브 생태계를 구성하는 강력한 원동력이 되고 있습니다. 깃허브에서 서비스 후크로 바로 통합할 수 있는 서비스라면 두 서비스 간의 통합 기능을 이용하는 게 가장 편리합니다. 하지만 원하는 기능을 직접 구현하고 싶다거나 아직 서비스 후크가 갖춰지지 않은 서비스와 통합을 하려는 경우엔 직접 깃허브에서 보내는 메시지를 처리해줄 서버를 만들 필요가 있습니다.

예를 들어서 제가 속해있는 Remotty 팀에서는 지금까지 힙챗(Hipchat)이라는 협업 채팅 툴을 사용해왔습니다만, 최근에 공개된 Slack이라는 서비스로 갈아탈 준비를 하고 있습니다.[^1] 하지만 깃허브과 통합을 하는데 약간의 애로사항이 있어서 이전을 포기했습니다. 얼마 전까지만 해도 Webhook을 통해서 Slack 서비스를 연동하면 커밋이 푸쉬되는 알림밖에는 전해주질 않았습니다. Hipchat 같은 경우는 깃허브에서 다루는 거의 모든 이벤트를 전달해줍니다. 특히 이슈와 관련된 부분도 필수적이고, 위키를 적극 사용하고 있었기에 이런 알림이 비활성화되는 것은 치명적인 단점으로 부각될 수밖에 없었습니다. 그리고 조금 더 올라가보면 바로 그런 통합이 가능했기 때문에 Hipchat을 사용하기로 했었으니까요.

이러한 문제에 대해서 슬랙 쪽 통합 방식이 최근에 변경되면서 현재는 이슈와 풀리퀘스트 부분의 알림을 보내주도록 추가가 되었습니다. 원래는 Slack의 hook_url을 직접 추가하는 방식으로 통합을 했습니다만, 최근에는 Github 인증을 하면 Slack에서 hook url을 추가해주고 이슈와 풀리퀘스트 관련 이슈들을 더해줍니다. 이러한 약간의 변화가 다시 슬랙으로 넘어가자는 의견에 힘을 실어주었습니다. 하지만 여전히 위키를 지원해주지 않는 문제가 남아있었습니다.

[^1]: 현재 완전히 이전한 상태입니다.

금방 지원해 줄 것 같기는 했습니다만, 당장 필요했던 관계로 직접 알림을 보내주는 서버를 만들기로 했습니다. 현재 루비의 Padrino[^2]로 만든 깃허브에서 위키 변경 사항 알림 [서버를 slack_notifier][slack_notifier]라는 이름으로 올려둔 상태입니다. 이 서버가 하는 일은 정말 딱 위키 알림을 Github에서 받고 Slack으로 전달해주는 일뿐입니다.

[slack_notifier]: https://github.com/nacyot/slack_notifier
[^2]: Padrino는 Sinatra를 확장한 경량 웹프레임워크입니다.

[로직][hook]도 정말 단순합니다. `hook_controller`에 다음과 같은 내용이 들어가있을 뿐입니다.

[hook]: https://github.com/nacyot/slack_notifier/blob/master/app/controllers/hook.rb

```
Slack::Post.configure(
                      subdomain: params[:subdomain],
                      token: params[:token],
                      username: "Github"
                      )
Slack::Post.post "#{login}#{action} <#{url}|#{title}>", "#" + params[:room]
```

내부적으로 [slack-post][slack-post]라는 젬을 사용해 깃허브에서 서버의 특정 페이지에 접근하면(이벤트를 알려주면), Slack에 메시지를 보내주는 방식입니다. 현재는 서버에 데이터를 저장하는 기능이 없어서 token과 메시지를 전달할 곳을 전부 Url 인자로 받아서 사용하고 있습니다.

자, 그럼 직접 사용해보도록 하죠.

### 도커(Docker) X slack_notifier ###

루비 서버를 올리는 게 어려운 일은 아닙니다만, 부가적인 처리 과정이나 설명해질 부분이 많아지므로 해당하는 부분에 대해서는 다루지 않도록 하겠습니다. 여기서는 이러한 과정을 생략하기 위해 이전 포스트에서 이야기했던 [도커(Docker)]를 출동시키겠습니다. 빠밤.

[docker]: http://docker.io

먼저 도커를 설치합니다. (여기서는 우분투를 가정합니다. 필요한 경우 가상머신이나 클라우드 서비스를 사용하시기 바랍니다. Remotty 팀에서도 SKT에서 지원받고 있는 VM을 활용해 개발 지원 서비스들을 운영중에 있습니다)

```
$ curl -s https://get.docker.io/ubuntu/ | sudo sh
...
$ docker --version
Client version: 0.7.6
Go version (client): go1.2
Git commit (client): bc3b2ec
Server version: 0.7.6
Git commit (server): bc3b2ec
Go version (server): go1.2
Last stable version: 0.7.6
```

다음으로 어플리케이션을 클론하고 `docker build`를 수행합니다.

```
$ git clone https://github.com/nacyot/slack_notifier
$ docker build -t nacyot/slack_notifier:latest -q=true ./slack_notifier
...
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
nacyot/slack_notifier   latest              d35792687e8c        4 days ago          751.1 MB
ubuntu                  12.04               8dbd9e392a96        9 months ago        128 MB
ubuntu                  12.10               b750fe79269d        10 months ago       175.3 MB
```

`docker imaegs` 명령어로 정상적으로 빌드된 것을 확인할 수 있습니다. 이제 생성한 이미지로부터 실제 어플리케이션 컨테이너를 실행시킵니다.

```
$ docker run -d -p 4000:4000 nacyot/slack_notifier:latest
7c42ae39691c
$ docker ps
CONTAINER ID        IMAGE                         COMMAND                CREATED             STATUS              PORTS                    NAMES
7c42ae39691c        nacyot/slack_notifier:first   /bin/sh -c bundle ex   4 days ago          Up 2 days           0.0.0.0:4000->4000/tcp   slack_notifier  
```

`docker ps`를 통해서 컨테이너가 정상적으로 실행되고 있는 것을 확인할 수 있습니다. 이제 깃허브에 WebHook을 등록할 차례입니다. 그 전에 먼저 Slack 쪽에서 서비스 등록을 하고 토큰을 생성할 필요가 있습니다.

```
http://<SLACK_SUBDOMAI>.slack.com/services/new/incoming-webhook
```

위 주소로 접속하시면 incoming-webhook을 바로 등록할 수 있습니다. `Add integration` 버튼을 누르면 오른쪽에 토큰 정보가 출력됩니다. 토큰 정보를 가지고 아래 URL을 완성합니다.

```
http://<서버주소>:4000/hook?subdomain=<SLACK_SUBDOMAIN>&token=<SLACK_TOKEN>&room=<SLACK_ROOM>
```

SLACK_ROOM에는 알림을 전달할 채널 이름을 지정합니다. 이제 이 URL을 원하시는 Github 저장소의 WebHook에 등록만 해주면 모든 준비는 완료됩니다. 하지만 여기까지 설정하고 위키를 수정해도 알림은 가지 않습니다. 앞서서 Github API에 대해서 다룬 바 있습니다만, 기본 Hook로는 `push`밖에 등록이 되지 않기 때문입니다. 이 서버에서 인식할 수 있는 이벤트는 위키를 다루는 `gollum`밖에 없습니다.

```
curl -u <USER_NAME> https://api.github.com/repos/<USER_NAME>/<REPO_NAME>/hooks/<HOOK_ID> -X PATCH -d '{"add_events": ["gollum"] }'
```

앞서 다룬 것과 마찬가지 방법으로 `gollum` 이벤트를 추가해줍니다. 네 이걸로 모든 설정이 끝났습니다. 이제 해당하는 저장소의 위키를 수정해보면 서버에 알림이 가고 서버가 Slack으로 위키를 수정했다는 알림이 가게 됩니다.

![Slack Room][github_slack]

만약 정상적으로 메시지가 가지 않을 경우엔 Github Webhook 등록 페이지에서 Test Hook 버튼을 누르고 `docker logs <CONTAINER_ID>` 명령어를 통해서 요청이 정상 전달되는지부터 확인할 필요가 있습니다. 일단 이러한 방법을 통해서 무사히 Slack으로 이전을 마쳤습니다.

## 정리 ##

네, 정리하겠습니다.

이 글에서는 Github Hook API 서버를 다루고 중간에서 깃허브이 전달해주는 메시지를 처리하는 서비스를 소개해보았습니다. 여기서 든 예제는 팀의 필요에 기반해서 만들어진 정말 간단한 예제입니다만, 필요하다면 좀 더 복잡하고 고도화된 서버를 개발할 수도 있을 것입니다.

