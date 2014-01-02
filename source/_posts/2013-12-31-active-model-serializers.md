---
layout: post
title: "active_model_serializers 젬 사용하기"
date: 2013-12-31 19:51:34 +0900
comments: true
categories: gem
author: 최효성
---

`active_model_serializers`젬은 레일스 API 를 작성할 때 JSON 데이터를 만들기 위해 추천되는 젬입니다.

설치
===

Gemfile 에 추가하고 bundle install 합니다.

{% codeblock lang:bash %}
gem 'active_model_serializers'
{% endcodeblock %}
<!--more-->

Serializer 생성하기
===

이후부터 `scaffolding`이나 `model generator`를 사용하여 특정 모델을 생성하면 자동으로 `serializer`가 만들어 집니다.

이미 만들어진 모델에 대해서는 아래와 같이 직접 `serializer`를 생성할 수 있습니다. 여기서는 `Post` 모델에 대한 `serializer`를 생성하는 예를 들었습니다.

{% codeblock lang:bash %}
$ rails g serializer post
{% endcodeblock %}

이제 `app/serializers/post_serializer.rb`에서 `Post` 모델에 대한 `serializer`를 볼 수 있게 됩니다.

render :json
===

컨트롤러에서 `render :json`을 사용하면, 우선적으로 해당 객체에 대한 `serializer`를 찾아보고 있으면 해당 `serializer`를 사용하게 됩니다.

{% codeblock lang:ruby %}
class PostsController < ApplicationController
  def show
    @post = Post.find(params[:id])
    render json: @post
  end
end
{% endcodeblock %}

배열
===

배열에 대해서도 `render :json`을 사용할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :title, :body
end

class PostsController < ApplicationController
  def index
    @posts = Post.all
    render json: @posts
  end
end
{% endcodeblock %}

렌더링되는 결과는 아래와 같습니다.

{% codeblock lang:javascript %}
{
  "posts":
    [
      { "title": "Post 1", "body": "Hello!" },
      { "title": "Post 2", "body": "Goodbye!" }
    ]
}
{% endcodeblock %}

디폴트로 컨트롤러의 이름이 루트 엘리먼트의 이름이 됩니다. 즉, `PostsController`는 `posts`라는 루트노드명을 만들어 줍니다. 또한 아래와 같이 루트노드명을 변경할 수도 있습니다.

{% codeblock lang:ruby %}
render json: @posts, root: "some_posts"
{% endcodeblock %}

루트 엘리먼트를 없애는 방법 4가지
===


* 모든 클래스에 대해서 루트 엘리먼트를 사용하지 않는 방법


  initializer 파일을 새로 만들어 아래와 같이 추가해 줍니다.

  {% codeblock lang:ruby %}
  # Disable for all serializers (except ArraySerializer)
  ActiveModel::Serializer.root = false

  # Disable for ArraySerializer
  ActiveModel::ArraySerializer.root = false
  {% endcodeblock %}


* 컨트롤러에서 render 옵션으로 지정하는 방법

{% codeblock lang:ruby %}
render json: @posts, root: false
{% endcodeblock %}

* Serializer를 상속받는 방법

{% codeblock lang:ruby %}
class CustomArraySerializer < ActiveModel::ArraySerializer
  self.root = false
end

# controller:
render json: @posts, serializer: CustomArraySerializer
{% endcodeblock %}

* 컨트로러에 `default_serializer_options` 메소드를 정의하는 방법

{% codeblock lang:ruby %}
def default_serializer_options
  {
    root: false
  }
end
{% endcodeblock %}

Attributes와 Associations
===

serializer 클래스에서는 속성과 관계를 지정할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body
  has_many :comments
end
{% endcodeblock %}

Attributes
===

attributes로 명시한 속성들에 대해서 serializer는 `render :json` 호출시에 넘겨준 액티브레코드 객체에 대해서 해당 속성들을 찾아보게 됩니다. 이 때 serializer는, `ActiveRecord` 객체가 속성을 조회하기 위해서는 사용하는 `read_attribute_for_serialization` 메소드를 이용하게 됩니다.

특정 객체에 대한 속성을 조회해 보기 전에, serializer는 해당 속성과 같은 이름의 메소드가 정의되어 있는지를 알아 보고 있다면 모델 속성을 포함하기 전에 해당 메소드의 결과를 속성으로 포함하게 됩니다.

예를 들면,

{% codeblock lang:ruby %}
class PersonSerializer < ActiveModel::Serializer
  attributes :first_name, :last_name, :full_name

  def full_name
    "#{object.first_name} #{object.last_name}"
  end
end
{% endcodeblock %}

serializer 메소드 내에서 객체는 `object`로써 접근하게 됩니다.
따라서 속성명이 `object` 라는 이름을 가질 경우 그 이름이 감춰지게 되므로 이 때는 `object.object`로써 접근할 수 있습니다. 예를 들면,

{% codeblock lang:ruby %}
class VersionSerializer < ActiveModel::Serializer
  attribute :version_object, key: :object

  def version_object
    object.object
  end
end
{% endcodeblock %}

또한 `scope` 메소드를 사용할 수 있는데, 이것은 serializer에서 인증상태를 이용할 수 있게 해 줍니다. 디폴트로는 어플리케이션의 current user가 바로 이러한 인증상태에 해당하는 것이지만 다른 것으로 변경할 수도 있습니다.

serializer는 `filter`라는 메소드를 제공해 줍니다. 이것은 결과에 보여줄 attributes와 associations을 포함하는 배열을 반환해 줍니다. 일반적으로 이것은 `current_user`에 근거해서 결과를 다양하게 보여주기 위해서 사용합니다. 예를 들면 다음과 같습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body, :author

  def filter(keys)
    if scope.admin?
      keys
    else
      keys - [:author]
    end
  end
end
{% endcodeblock %}

별도의 keys 배열을 추가로 만들 필요없이, `keys.delete(:author)`를 이용하여 keys 인수를 변경하는 것이 안전할 것입니다. 주의할 것은 in-place 변경을 시도하더라도 변경된 배열을 여전히 반환할 필요가 있다는 것입니다.

액티브레코드 상의 이름과 다른 키를 사용하고 싶을 때는, 다른 이름의 키를 선언하고 메소드를 재정의하면 됩니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  # look up subject on the model, but use title in the JSON
  def title
    object.subject
  end

  attributes :id, :body, :title
  has_many :comments
end
{% endcodeblock %}

JSON 결과물에 메타 정보를 포함하고잘 할 경우에는, `:meta` 옵션을 사용하면 됩니다.

{% codeblock lang:ruby %}
render json: @posts, serializer: CustomArraySerializer, meta: {total: 10}
{% endcodeblock %}

그러면 아래와 같은 결과를 보여 줄 것입니다.

{% codeblock lang:javascript %}
{
  "meta": { "total": 10 },
  "posts": [
    { "title": "Post 1", "body": "Hello!" },
    { "title": "Post 2", "body": "Goodbye!" }
  ]
}
{% endcodeblock %}

또한 `:meta_key` 옵션을 사용하면 메타 키 이름을 변경할 수 있습니다.


{% codeblock lang:ruby %}
render json: @posts, serializer: CustomArraySerializer, meta: {total: 10}, meta_key: 'meta_object'
{% endcodeblock %}

`:meta_key` 옵션을 사용하면 아래와 같은 결과를 보여 줄 것입니다.

{% codeblock lang:javascript %}
{
  "meta_object": { "total": 10 },
  "posts": [
    { "title": "Post 1", "body": "Hello!" },
    { "title": "Post 2", "body": "Goodbye!" }
  ]
}
{% endcodeblock %}

이와 같이 메타 정보를 이용할 경우에는, serializer는 `{ root: false }` 옵션을 가질 수 없습니다. 결국 유효하지 않는 JSON 데이터를 반화하기 때문에 루트 키가 없는 경우에는 메타 정보가 무시될 것입니다.

attribute 직렬화 과정을 직접 로우레벌에서 조작하고자 할 경우에는, `attributes` 메소드를 덮어쓰기해서 해시를 반환해 주면 됩니다.

{% codeblock lang:ruby %}
class PersonSerializer < ActiveModel::Serializer
  attributes :first_name, :last_name

  def attributes
    hash = super
    if scope.admin?
      hash["ssn"] = object.ssn
      hash["secret"] = object.mothers_maiden_name
    end
    hash
  end
end
{% endcodeblock %}

Associations
===

association을 사용할 경우, serializer가 해당 association을 찾아보고 연관객체의 각 엘리먼트를 직렬화하게 됩니다. 예를 들어, `has_many :comments` 라고 지정하면 각 comment 객체에 대해서 CommentSerializer 객체를 만들어서 직렬화하게 되는 것입니다.

디폴트 상태에서는 오리지날 객체에 대해서 선언되어 있는 association을 찾게 됩니다. 그러나 해당 association 이름과 동일한 메소드를 정의하여 반환되는 객체들을 변경할 수 있습니다. 이것은 특정 scope(current_user와 같은)에 국한된 객체들을 반환할 때 사용하면 도움이 될 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body
  has_many :comments

  # only let the user see comments he created.
  def comments
    object.comments.where(created_by: scope)
  end
end
{% endcodeblock %}

이 경우에도 attributes와 같이 JSON 키를 변경할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body

  # look up comments, but use +my_comments+ as the key in JSON
  has_many :comments, root: :my_comments
end
{% endcodeblock %}

또한 attributes와 같이, `filter` 메소드를 정의하면, 결과로써 포함할 associations을 지정할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body
  has_many :comments

  def filter(keys)
    keys.delete :comments if object.comments_disabled?
    keys
  end
end
{% endcodeblock %}

또는

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body
  has_one :author
  has_many :comments

  def filter(keys)
    keys.delete :author unless scope.admin?
    keys.delete :comments if object.comments_disabled?
    keys
  end
end
{% endcodeblock %}

`:serializer` 옵션을 이용하여 커스텀 serializer 클래스를 지정할 수 있고 `:polymorphic` 옵션을 지정하여 해당 association이 polymorphic 이라는 것을 알려줄 수 있습니다.

serializer에서는 `belongs_to` association을 `has_one`을 이용하여 포함하게 된다는 것을 주의해야 합니다.

Embedding Associations
===

디폴트 상태에서는 associations가 serializer 객체에 포함(embeded)됩니다. 그래서 하나의 post 가 있다고 가정할 때 다음과 같은 결과를 볼 수 있게 될 것입니다.

{% codeblock lang:javascript %}
{
  "post": {
    "id": 1,
    "title": "New post",
    "body": "A body!",
    "comments": [
      { "id": 1, "body": "what a dumb post" }
    ]
  }
}
{% endcodeblock %}

이러한 결과물은 간단한 경우에는 편리하지만, 복잡한 association이 존재할 경우에는 해당 association 에 대해서 ID이 구성된 배열을 포함하는 것이 더 좋을 것입니다. 이것은 전체적인 퍼포먼스 측면에서도 그렇고 불필요한 중복을 피할 수 있어서 좋습니다.

이를 위해서 `embed`라는 클래스 메소드를 사용하면 됩니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  embed :ids

  attributes :id, :title, :body
  has_many :comments
end
{% endcodeblock %}

이제 association들이 ID들로 구성된 배열을 포함하게 될 것입니다.

{% codeblock lang:javascript %}
{
  "post": {
    "id": 1,
    "title": "New post",
    "body": "A body!",
    "comment_ids": [ 1, 2, 3 ]
  }
}
{% endcodeblock %}

다른 방법으로는 클래스내의 측정 association에 대해서만 ID 또는 객체 배열만을 포함할 수 있게 할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body

  has_many :comments, embed: :objects
  has_many :tags, embed: :ids
end
{% endcodeblock %}

따라서 JSON 데이터는 다음과 같이 보일 것입니다.

{% codeblock lang:javascript %}
{
  "post": {
    "id": 1,
    "title": "New post",
    "body": "A body!",
    "comments": [
      { "id": 1, "body": "what a dumb post" }
    ],
    "tag_ids": [ 1, 2, 3 ]
  }
}
{% endcodeblock %}

게다가, ID 만을 포함하는 것 외에도 메인 객체에 데이터를 추가로 포함할 수도 있습니다. 이렇게 하므로써 포함된 정보를 검색하기 위해서 트리구조를 스캔할 필요없이 전체 데이터 패키지를 보다 쉽게 처리할 수 있게 될 것입니다. 또한 객체사이에 (tags와 같이) 공유되는 associations들은 전체 로드시에 단 한번만 전달된다는 것입니다.

아래와 같이 데이터가 포함되도록 명시할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  embed :ids, include: true

  attributes :id, :title, :body
  has_many :comments
end
{% endcodeblock %}

이 때 comments 객체가 `has_many :tags` association이 선언되어 있다고 가정하면, 다음과 같은 JSON 데이터를 얻게 될 것입니다.

{% codeblock lang:javascript %}
{
  "post": {
    "id": 1,
    "title": "New post",
    "body": "A body!",
    "comment_ids": [ 1, 2 ]
  },
  "comments": [
    { "id": 1, "body": "what a dumb post", "tag_ids": [ 1, 2 ] },
    { "id": 2, "body": "i liked it", "tag_ids": [ 1, 3 ] },
  ],
  "tags": [
    { "id": 1, "name": "short" },
    { "id": 2, "name": "whiny" },
    { "id": 3, "name": "happy" }
  ]
}
{% endcodeblock %}

위에서와 같이 데이터를 추가로 로드할 경우에는 `{ root: false }` 옵션을 사용할 수 없습니다. 이 옵션을 지정할 경우에는 유효하지 않은 JSON 데이터를 만들게 되기 때문입니다. 따라서 이 옵션을 지정하게 되면 `include` 옵션이 작동하지 않게 됩니다.

또한 포함된 객체에 대해서는 참조하는 키외의 다른 루트를 지정할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  embed :ids, include: true

  attributes :id, :title, :body
  has_many :comments, key: :comment_ids, root: :comment_objects
end
{% endcodeblock %}

이것은 다음과 같은 JSON 데이터를 민들게 됩니다.

{% codeblock lang:javascript %}
{
  "post": {
    "id": 1,
    "title": "New post",
    "body": "A body!",
    "comment_ids": [ 1 ]
  },
  "comment_objects": [
    { "id": 1, "body": "what a dumb post" }
  ]
}
{% endcodeblock %}

또한 포함된 객체의 ID외의 다른 속성을 지정할 수 있습니다.

{% codeblock lang:ruby %}
class PostSerializer < ActiveModel::Serializer
  embed :ids, include: true

  attributes :id, :title, :body
  has_many :comments, embed_key: :external_id
end
{% endcodeblock %}

이것은 다음과 같은 JSON 데이터를 만들게 됩니다.

{% codeblock lang:javascript %}
{
  "post": {
    "id": 1,
    "title": "New post",
    "body": "A body!",
    "comment_ids": [ "COMM001" ]
  },
  "comments": [
    { "id": 1, "external_id": "COMM001", "body": "what a dumb post" }
  ]
}
{% endcodeblock %}

Note: `embed :ids` 기전은 주로 데이터를 대량으로 처리해서 로컬 저장소에 로드할 경우에 유용합니다. 이와 같은 경우에, 정보검색을 위해서 데이터를 반복적으로 스캔할 필요없이 종류별로 모든 데이터를 쉽게 볼 수 있다는 것은 매우 유용한 기능입니다.

대부분의 경우 간단히 시나리오 하에 데이터 작업을 하고 직접 Ajax 요청을 할 경우에는 아마도 디폴트 상태의 embed 기능을만을 사용하면 될 것입니다.

Scope 커스터마이징하기
===

특정 serializer 클래스에서 대해서, `current_user` 는 `render :json` 을 호출할 때 컨트롤러가 해당 serializer에 제공하는 인증 scope에 해당합니다. 디폴트로, 이것은 `current_user`가 되지만, 컨트롤러에서 ` serialization_scope`을 호출하여 이 scope을 변경할 수 있습니다.

{% codeblock lang:ruby %}
class ApplicationController < ActionController::Base
  serialization_scope :current_admin
end
{% endcodeblock %}

위의 예는 scope을 `current_user`에서 `current_admin`으로 변경하게 될 것입니다.

주목할 것은, 지금까지 볼 때, `serialization_scope`은 두번째 인수를 지정하여, 해당 scope을 적용할 액션들을 지정할 수 없습니다.

즉, 아래와 같이 액션들을 지정할 수 없다는 것입니다.

{% codeblock lang:ruby %}
class SomeController < ApplicationController
  serialization_scope :current_admin, except: [:index, :show]
end
{% endcodeblock %}

따라서 대신에 아래와 같이 처리할 수 있습니다.

{% codeblock lang:ruby %}
class CitiesController < ApplicationController
  serialization_scope nil

  def index
    @cities = City.all

    render json: @cities, each_serializer: CitySerializer
  end

  def show
    @city = City.find(params[:id])

    render json: @city, scope: current_admin
  end
end
{% endcodeblock %}

위에 예에서, `current_admin` 메소드가 데이터베이스에서 현재 사용자를 조회할 필요가 있다고 가정한다면, 이러한 방식의 접근방식을 통해서, `serailization_scope`값은 `nil`로 지정하므로써, `index` 액션이 더 이상 데이터베이스를 조회하기 않고 단지, `show` 액션만이 해당 메소드를 실행하게 되는 것입니다.

Testing
===

임의의 serializer 클래스를 테스트하기 위해서는, 단지 해당 serializer 클래스에 대해서 `.new` 메소드를 호출하여 모델 클래스 객체를 넘겨 주면 됩니다.

MiniTest
---

{% codeblock lang:ruby %}
class TestPostSerializer < Minitest::Test
  def setup
    @serializer = PostSerializer.new Post.new(id: 123, title: 'some title', body: 'some text')
  end

  def test_special_json_for_api
    assert_equal '{"post":{"id":123,"title":"some title","body":"some text"}}', @serializer.to_json
  end
{% endcodeblock %}

RSpec
---

{% codeblock lang:ruby %}
describe PostSerializer do
  it "creates special JSON for the API" do
    serializer = PostSerializer.new Post.new(id: 123, title: 'some title', body: 'some text')
    expect(serializer.to_json).to eql('{"post":{"id":123,"title":"some title","body":"some text"}}')
  end
end
{% endcodeblock %}

수고하셨습니다.