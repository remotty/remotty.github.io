---
layout: post
title: "model_spec"
date: 2013-12-18 09:17:57 +0900
comments: true
categories: RSpec TDD
author: 최효성
---

Rails 어플리케이션에서 모델은 해당 어플리케이션의 주요 핵심 로직으로 구성되어 있습니다. 
따라서, TDD를 이용한 Test 기반 개발을 할 때도 모델에 대한 Test를 중점적으로 시행하는 것은 당연한 일일 것입니다. 

이를 위해서는, TDD에 관한 여러 가지 책이 있지만, 저의 경우는 Aaron Sumner의 "Everday Rails Testing with RSpec(https://leanpub.com/everydayrailsrspec)"가 쉽게 이해가 되더군요.

이 책의 내용 중 모델 챕터를 보면 쉽게 따라해 볼 수 있습니다. 

TDD를 위한 젬 중에 대표적인 것은 `RSpec` 입니다. 이 젬을 중심으로 TDD를 위한 환경구축을 위해서 Aaron이 추천하는 젬 구성은 아래와 같습니다. 

``` ruby
group :development, :test do
  gem "rspec-rails", "~> 2.14.0.rc1"
  gem "factory_girl_rails", "~> 4.2.1"
end

group :test do
  gem "faker", "~> 1.1.2"
  gem "capybara", "~> 2.1.0"
  gem "database_cleaner", "~> 1.0.1"
  gem "launchy", "~> 2.3.0"
end
``` 

각 모델에 대해서는 아래의 3가지 정도를 test해 보면 됩니다. 

- 해당 모델의 `create` 메소드에 유효한 데이터를 넘겨 주면 에러 없이 모델 객체가 생성됨.
- 해당 모델에 유효하지 않는 데이터를 넘겨 주면 유효성 검증에서 실패함.
- 해당 모델의 클래스 메소드나 인스턴스 메소드가 원하는 대로 결과를 만들어 냄.

