---
layout: post
title: "AngularJS+Rails 프로젝트 구성"
date: 2014-02-01 15:20:32 +0900
comments: true
categories: [rails, angularjs]
author: subicura
---

최근 프로젝트에서 Rails기반에 AngularJS를 사용한 경험을 공유합니다.

간단하게 frontend/backend의 개념과 angularjs를 소개하고 프로젝트를 구성하는 방법을 설명합니다.

<!--more-->

# frontend / backend

지금까지의 웹개발은 서버 프레임워크에서 frontend와 backend를 모두 처리하였습니다. controller에서 요청을 받으면 database와 통신하여 데이터를 가져오고 view를 통해 응답하는 방식입니다.

하지만 최근 iOS와 Android등 모바일앱이 늘어나면서 서버는 API만 처리하고 각 앱에서는 서버로 부터 받은 데이터를 가지고 화면에 표현하는 패턴이 일반화 되었습니다. 복잡한 서버 프레임워크 대신 경량의 서버 프레임워크가 선호되고 frontend와 backend가 철저히 분리되어 개발할 수 있게 된 것입니다.

웹에서도 webapp이라는 이름으로 그러한 흐름이 나타나고 있으며 Angular.js/Backbone.js/Knockout.js 등 다양한 framework이 등장하였습니다.

# AngularJS

AngularJS는 최근 엄청나게 인기를 끌고 있는 **frontend webapp framework**입니다.
Google이 만들었다는 점에서 믿고 쓸수 있고 짧은 코드로 빠르고 강력한 웹앱을 만들 수 있습니다.

- html/css/js등 static 파일로 구성되어 있고 java/php/.net등 서버쪽 작업은 전혀 필요가 없습니다. 단순히 파일을 그대로 제공만 합니다.
- 모든 서버자원은 ajax를 이용하여 API서버를 호출하는 것으로 처리합니다.
- yeoman/grunt/bower를 이용하여 빠르게 프로젝트를 구축하고 개발하고 배포할 수 있습니다.

# Rails와 AngularJS 조합하기

우선..
**AngularJS 사용 == Rails에서 View를 사용하지 않음**
이라는 생각으로 접근하면 이해가 빠를것 같습니다.

Rails에서 제공하는 강력한 View Helper의 기능을 사용하지 못한다고? 네 ㅠㅠ 맞습니다. Rails는 오로지 API를 제공하는 역할만 수행하고 View는 철저히 AngularJS에서 처리합니다.

## 디렉토리 구조

기존 rails 디렉토리 구성은 다음과 같습니다

```
ㄴ RAILS_ROOT
  ㄴ app
  ㄴ bin
  ㄴ config
  ㄴ db
  ㄴ lib
  ㄴ log
  ㄴ public
  ㄴ spec
  ㄴ tmp
``` 

여기에 AngularJS 소스를 넣기 위해 `angular`디렉토리와 `public`디렉토리를 사용합니다.

```
ㄴ RAILS_ROOT
  ㄴ angular (angular와 관련된 소스는 이곳에 저장됩니다.)
  ㄴ app
  ㄴ bin
  ㄴ config
  ㄴ db
  ㄴ lib
  ㄴ log
  ㄴ public (angular의 최종 dist 파일들이 이곳에 위치합니다.)
  ㄴ spec
  ㄴ tmp
``` 

- angular 디렉토리 : angular와 관련된 `소스`파일이 위치합니다. yeoman/grunt/bower 파일과 셋팅이 있고 실제 작업은 여기서 이루어집니다.
- public 디렉토리 : angular작업이 완료되면 소스를 압축(minify)하고 최적화하여 최종 사용자에게 보여질 `배포`파일을 생성합니다. 이렇게 구성하면 일반적인 rails 배포 방식으로 webapp을 같이 배포할 수 있습니다.


사실 angular디렉토리는 rails 프로젝트에 포함되어 있지 않아도 상관없습니다. 따로 프로젝트를 만들고 dist파일을 public디렉토리에 복사해도 되지만 보통 frontend와 backend작업을 동시에 할 경우 같이 관리하는 것이 좀더 편한 것 같습니다.

## RAILS 설정

rails는 view를 사용하지 않으므로 [rails-api](https://github.com/rails-api/rails-api)를 이용합니다.

```
$ rails-api new sample
```


## AngularJS 설정

[yeoman](http://yeoman.io/)을 이용합니다.

ruby/compass/nodejs/generator-angular등을 미리 설치해야합니다.
설치하는 방법은.. 여기서는 pass!

```
$ cd sample
$ mkdir angular
$ cd angular
$ yo angular

     _-----_
    |       |
    |--(o)--|   .--------------------------.
   `---------´  |    Welcome to Yeoman,    |
    ( _´U`_ )   |   ladies and gentlemen!  |
    /___A___\   '__________________________'
     |  ~  |
   __'.___.'__
 ´   `  |° ´ Y `

Out of the box I include Bootstrap and some AngularJS recommended modules.

[?] Would you like to use Sass (with Compass)? Yes
[?] Would you like to include Twitter Bootstrap? Yes
[?] Would you like to use the Sass version of Twitter Bootstrap? Yes
[?] Which modules would you like to include? (Press <space> to select)
❯⬢ angular-resource.js
 ⬢ angular-cookies.js
 ⬢ angular-sanitize.js
 ⬢ angular-route.js
 
 ... 생략 ...
```

추가적으로 npm/bower install을 하면 기본 구성 완료!

```
$ npm install
....
$ bower install
....
```

서버 실행하기

```
$ grunt serve
...
```


## 도메인 이슈
- rails server : http://localhost:3000
- grunt server : http://localhost:9000

rails서버는 `3000` port로 동작하고 grunt서버는 `9000` port로 동작합니다. 따라서 angular에서 rails api를 호출하기 위해서는 `http://localhost:3000/api/v1/posts`와 같이 호출해야 합니다.

개발환경에서는 그렇게 하면 되는데.. 운영환경은 어떻게 해야 할까요?? 운영에서는 `/api/v1/posts`와 같이 호출하면 되는데.. 환경에 따라 설정을 분리해야 할까요?

### Frontend Setup

여기서는 [grunt-connect-proxy](https://github.com/drewzboto/grunt-connect-proxy) plugin을 이용합니다.

grunt 서버에서 `/api/v1/posts`를 호출할 경우 자동으로 `localhost:300/api/v1/posts`로 연동해줍니다.

먼저 plugin을 설치하고..

```
$ npm install grunt-connect-proxy --save-dev
```

`Gruntfile.js`에 관련설정을 합니다.

```
...

var proxySnippet = require('grunt-connect-proxy/lib/utils').proxyRequest;

...

    connect: {
      options: {
        port: 9000,
        // Change this to '0.0.0.0' to access the server from outside.
        hostname: 'localhost',
        livereload: 35729
      },
      proxies: [
        {
          context: '/api/v1',
          host: 'localhost',
          port: 3000,
          https: false,
          changeOrigin: false,
          xforward: false
        },
        {
          context: '/system',
          host: 'localhost',
          port: 3000,
          https: false,
          changeOrigin: false,
          xforward: false
        }
      ],
      livereload: {
        options: {
          open: true,
          base: [
            '.tmp',
            '<%= yeoman.app %>'
          ],
          middleware: function (connect, options) {
            var middlewares = [];
            middlewares.push(proxySnippet);
            options.base.forEach(function(base) {
              middlewares.push(connect.static(base));
            });
            return middlewares;
          }
        }
      },
      
...

  grunt.registerTask('serve', function (target) {
    if (target === 'dist') {
      return grunt.task.run(['build', 'connect:dist:keepalive']);
    }

    grunt.task.run([
      'clean:server',
      'concurrent:server',
      'autoprefixer',
      'configureProxies',
      'connect:livereload',
      'watch'
    ]);
  });
  
...
```

### Backend Setup

rails쪽은 CORS 설정을 합니다.

`Gemfile`에 cors gem을 추가합니다.

```
# Rack CORS Middleware
gem 'rack-cors', :require => 'rack/cors'
```

그리고 `application.rb`에 관련 설정을 추가합니다.

```
    config.middleware.use Rack::Cors do
      allow do
        origins "*"
        resource "*", :headers => :any, :methods => [:get, :post, :delete, :put, :patch, :options]
      end
    end
```


이제 proxy 설정이 완료되었습니다! 개발이나 운영이나 `/api/v1/xxx`을 호출하면 됩니다. +_+/


#### 배포 설정하기

현재 설정은 angular디렉토리 밑에 `dist`폴더를 만들고 배포하게 되었습니다. 이부분을 `../public`으로 배포하게 수정합니다.

역시 `Gruntfile`을 수정합니다.

```
...

    yeoman: {
      // configurable paths
      app: require('./bower.json').appPath || 'app',
      dist: '../public'
    },
    
...

    clean: {
      options: { force: true },
      dist: {
        files: [{
          dot: true,
          src: [
            '.tmp',
            '<%= yeoman.dist %>/*',
            '!<%= yeoman.dist %>/.git*',
            '!<%= yeoman.dist %>/system'
          ]
        }]
      },
      server: '.tmp'
    },
    
...

```

잘 배포되는지 테스트 해봅니다.

```
$ grunt build
...
```

[http://localhost:3000](http://localhost:3000) 으로 접속해서 angular 화면이 보이면 성공입니다!!


### 결론

이로써 Rails와 AngularJS를 조합하는데 성공했습니다.

사실 가장 기본적인 부분을 연동한 것이며 추후 인증 및 token설정과 관련하여 더 많은 작업이 필요합니다.. ㅠㅠ

그부분은 다음기회에 설명하기로 하고 이만 마칩니다 ㅎㅎ =_=/
