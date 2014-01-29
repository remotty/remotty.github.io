---
layout: post
title: "Angularjs Providers"
date: 2014-01-29 13:55:27 +0900
comments: true
categories: angularjs
author: 최효성
---

**AngularJS에서 Provider 간의 차이점**
-------------------------------

http://bit.ly/19ZQTHy (한글번역)
<!--more-->
provider vs factory vs service

### Provider란 무엇인가?

AngularJS docs 에는 아래와 같이 정의되어 있다.

> $get 메소드를 가지는 하나의 객체이다. injector는 바로 이 $get 메소드를 호출해서 새로운 서비스를 생성하게 되는 것이다. `provider`는 설정을 통해서 이와 같은 메소드를 추가할 수 있다.

AngularJS는 `$provide`를 이용해서 새로운 `provider`를 등록한다. `provider`는 기본적으로 새로운 인스턴스를 생성하지만 `provider`당 하나의 인스턴스만 만들게 된다(singleton). `$provide`는 6개의 메소드를 사용해서 커스텀 `provider`를 생성하게 되는데, 각각에 대해서 샘플 코드와 함께 설명할 것이다. 아래의 `provider`는 $provide` 상에서 사용할 수 있다.

- constant
- value
- service
- factory
- decorator
- provider

##Constant

`constant(상수)`는 모든 곳에 inject(주입)할 수 있다. 상수는 그 값을 변경할 수 없다.

    var app = angular.module('app', []);

    app.config(function ($provide) {
      $provide.constant('movieTitle', 'The Matrix');
    });

    app.controller('ctrl', function (movieTitle) {
      expect(movieTitle).toEqual('The Matrix');
    });

AngularJS는 상수를 만들기 위한 편리한 방법을 제공해 주어 코드를 단축할 수 있다.

    app.constant('movieTitle', 'The Matrix');

##Value
`value`는 `configuration(설정)`로 주입할 수 없지만 값을 변경할 수는 있다.

```javascript
var app = angular.module('app', []);

app.config(function ($provide) {
  $provide.value('movieTitle', 'The Matrix')
});

app.controller('ctrl', function (movieTitle) {
  expect(movieTitle).toEqual('The Matrix');
})
```

AngularJS는 `value`를 만들기 위한 편리한 방법을 제공해 주어 코드를 단축할 수 있다.

```javascript
app.value('movieTitle', 'The Matrix');
```

##Service

`service`란 주입이 가능한 `constructor(생성자)`이다. 원할 경우, 함수에 필요한 dependency를 명시할 수 있다. `service`는 하나의 singleton이기 때문에 AngularJS가 한번만 생성하게 될 것이다. `service`는 데이터를 공유하는 것 같은 컨트롤러간의 통신을 위한 좋은 방법이다.

```javascript
var app = angular.module('app' ,[]);

app.config(function ($provide) {
  $provide.service('movie', function () {
    this.title = 'The Matrix';
  });
});

app.controller('ctrl', function (movie) {
  expect(movie.title).toEqual('The Matrix');
});
```
AngularJS는 `service`를 만들기 위한 편리한 방법을 제공해 주어 코드를 단축할 수 있다.

```javascript
app.service('movie', function () {
  this.title = 'The Matrix';
});
```

##Factory

`factory`는 주입이 가능한 함수이다. `factory`는 singleton이고 dependency를 함수내에 명시할 수 있다는 점에서 `service`와 많은 부분에서 흡사하다. 차이점은 `factory`는 일반 함수를 주입해서 AngularJS가 호출하도록 하는 것이고 `service`는 `constructor`를 주입한다는 것이다. `constructor`는 새로운 객체를 만들기 때문에 `service`내에서는 `new` 메소드를 호출하지만, `factory`를 사용하면 함수가 어떤 형태의 것이라고 반환할 수 있도록 해 준다. 나중에 알게 되겠지만, `factory`는 `$get` 메소드만을 가지는 `provider`이다.

```javascript
var app = angular.module('app', []);

app.config(function ($provide) {
  $provide.factory('movie', function () {
    return {
      title: 'The Matrix';
    }
  });
});

app.controller('ctrl', function (movie) {
  expect(movie.title).toEqual('The Matrix');
});
```
AngularJS는 `factory`를 생성하는 편리한 방법을 제고해 주어 아래와 같이 단축할 수 있다.

```javascript
app.factory('movie', function () {
  return {
    title: 'The Matrix';
  }
});
```

##Decorator

`decorator`는 다른 `provider`를 변경하거나 encapulation할 수 있다. 그러나 하나의 예외가 있는데, `constant`는 decorate 할 수 없다는 것이다.

```javascript
var app = angular.module('app', []);

app.value('movieTitle', 'The Matrix');

app.config(function ($provide) {
  $provide.decorator('movieTitle', function ($delegate) {
    return $delegate + ' - starring Keanu Reeves';
  });
});

app.controller('myController', function (movieTitle) {
  expect(movieTitle).toEqual('The Matrix - starring Keanu Reeves');
});
```

##Provider

`provider`는 모든 `provider` 중에서 가장 유연한 메소드이다. 이 메소드를 이용하면 복잡한 생성 함수에 다양한 옵션을 가지도록 할 수 있다. `provider`는 실제로 구성을 변경할 수 있는 `factory`이다. `provider`는 하나의 객체나 `constructor`를 취하게 된다.

```javascript
var app = angular.module('app', []);

app.provider('movie', function () {
  var version;
  return {
    setVersion: function (value) {
      version = value;
    },
    $get: function () {
      return {
          title: 'The Matrix' + ' ' + version
      }
    }
  }
});

app.config(function (movieProvider) {
  movieProvider.setVersion('Reloaded');
});

app.controller('ctrl', function (movie) {
  expect(movie.title).toEqual('The Matrix Reloaded');
});
```
##요약

- 모든 `providers`들은 단 한번만 인스턴스화 된다. 즉, singleton 이라는 것이다.
- `constant`를 제외한 모든 `provider`는 decorate할 수 있다.
- `constant`은 어느 곳이나 주입할 수 있는 값을 가지만, 수정할 수 없다.
- `value`는 간단하게 주입할 수 있는 값이다.
- `service`는 주입할 수 있는 `constructor`이다.
- `factory`는 주입할 수 있는 함수이다.
- `decorator`는 `constant`를 제외한 모든 `provider`를 변경하거나 encapuslation할 수 있다.
- `provider`는 구성요소를 설정할 수 있는 `factory`이다.
