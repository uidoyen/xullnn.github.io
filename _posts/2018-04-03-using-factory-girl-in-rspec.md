---
title:  "Rspec on Rails - 3 - PORO vs fixtures vs factory_bot"
categories: [Test ℗]
tags: [Rails Tests, Rspec]
---

测试不可避免地伴随测试用data，生成这类data通常有三种方式：

#### 1 直接在spec文件中现写数据

也叫 Plain Old Ruby Objects (POROs)

- 优点是最直白，最易读，不用在各个文件之间翻找案例，记忆案例名称和案例特征等。
- 缺点是：1 specs文件之间的对象不能复用，如果某个model对象(比如 user)在所有spec文件中都有出演，那么每次都要重写；2 当objects之间的关系比较复杂的时候，代码会变得冗长，这一点与第1点提到的问题相乘，问题会更大。


```ruby
require 'rails_helper'
RSpec.describe Relationship, type: :model do

  before do
    @user1 = User.create(name: "user1",
                         email: "user1@example.com",
                         password: "password")
    @user2 = User.create(name: "user2",
                         email: "user2@example.com",
                         password: "passwor")
  end

  describe .....
end   
```

如上面这样的方式 `@user1`, `@user2` 可以在后面所有的测试案例中使用。而且随时可以回顾这两个测试对象的详细情况。

如果另外还有10个model要测，每个中都会牵涉到user对象，那么就还要写10次。

#### 2 使用 fixtures

这是Rails自带的功能，按惯例在测试文件夹中单独有个 `fixtures/` 目录，里面放很多 `yml` 文件，一个文件代表一个 model，里面写测试用的objects

- 优点是，速度快，单个文件清晰易读，而且是Rails自带的，不会有引入第三方工具的额外配置。
- 缺点是灵活性低，应对复杂model关系时需要花较多精力设计和维护。

文件结构

```
test/fixtures/
  users.yml
  relationships.yml
  posts.yml
  ...

```
单个文件中比如 users.yml 中，缩进层级用来区分不同对象

```yml
caven:
  name: caven Example
  email: caven@example.com
  password_digest: <%= User.digest('password')%>
  admin: true
  activated: true
  activated_at: <%= Time.zone.now %>

Joe:
  name: Joe Black
  email: joe@example.org
  password_digest: <%= User.digest("password")%>
  activated: true
  activated_at: <%= Time.zone.now %>

# 还可以使用 ruby 批量生产objects

<% 10.times do |n| %>
user_<%= n %>:
  name: <%= "User No.#{n}"%>
  email: <%= "user-#{n}@example.com"%>
  password_digest: <%= User.digest("password")%>
  activated: true
  activated_at: <%= Time.zone.now %>
<% end %>

```

上面例子中生产出了 2 + 10 = 12 个 user 对象，后面10个是用loop产生的。

测试文件中拿取这些样本对象的方法是使用

`users(:caven)`

`users(:Joe)`

这样的方式。

#### 3 引入第三方工具

以 factory_bot_rails 为例

- 优点，足够灵活，足够抽象，能用少量代码生成不同特征的测试案例，可读性高。应对复杂model结构的时候仍然可以保持简洁。
- 缺点，复杂化了只需简单测试的案例。句法强大但需要额外花注意力熟悉，尤其是对于model之间有 associations 的情况。可能不小心生成过多的测试资料。

https://github.com/thoughtbot/factory_bot_rails

如果引入并配置好后。测试数据文件会放在 `spec/factories/` 中，以models.rb形式命名。

```
spec/factories/
  users.rb
  posts.rb
  relationships.rb
  ...
```

在单个文件比如 `users.rb` 中, 如何生产数据资料：

先 `bin/rails g factory_bot:model user`

```ruby
FactoryBot.define do
  factory :user do
    name "caven"
    email "example@gmail.com"
    password "password"
  end
end
```

然后在 spec 文件中调用

`user = FactoryBot.build(:user)` 内存中

`user = FactoryBot.create(:user)` 数据库中

其实到这一步可以发现 factory_girl_rspec 所能实现的和 fixtures 是一样的，只不过是句法上的区别，甚至还更复杂了。

**beyond fixtures**

一个简单的示例，rspec中测试email栏位的presence，看两种方法的区别

新建spec/fixtures/users.yml 文件

1 fixtures style

spec/fixtures/users.yml

```yml
one:
  name: one
  email: one@example.com
  ...

one_without_email:
  name: one
  email:
  ...
```

spec/models/user_spec.rb

```ruby
require 'rails_helper'

RSpec.describe User, type: :model do
  fixtures :users
  it "is valid with all columns" do
    expect(users(:one)).to be_valid
  end

  it "is invalid without email" do
    expect(users(:one_without_email)).to_not be_valid
  end
end
```

假设user有很多栏位，有5个要验证presence，有6个需要验证长度和格式，那么在 yml 中就需要写很多个user

并且user在fixtures中的命名需要有semantics上的含义，不然在spec文件中就不知道这个user的特征是什么

2 factory bot style

`bin/rails g factory_bot:model user`

spec/factories/users.rb

```ruby
FactoryBot.define do
  factory :user do
    name "Joe"
    email "joe@example.com"

    trait :without_email do
      email ""
    end

  end
end
```

spec/models/user_spec.rb

```ruby
require 'rails_helper'

RSpec.describe User, type: :model do

  it "is valid with all columns" do
    expect(FactoryBot.build(:user)).to be_valid
  end

  it "is invalid without email" do
    expect(FactoryBot.build(:user, :without_email)).to_not be_valid
  end

end
```

factory_bot 提供的 `trait` 方法，可以基于一个标准的user object, 使用嵌套block的方式修改指定column

而且 像`FactoryBot.build(:user, :without_email)` 就具有了很高的可读性，一眼就知道用来测试的这个user有什么特征。

对于上面这个简答的案例可能没有明显的感受，但如果app中model结构复杂，比如有很多 join table，那么写fixture的时候就会变得吃力。

---

### 总结

- 三种方法各有各的特点，各有各的适用场景，且各自的适用场景之间也有重叠。所以不该抛开应用场景谈优劣。
  - 如果是很简单的测试，那么直接在spec文件中现写测试资料是不错的选择，不需要花时间去维护fixtures或第三方工具。
  - 如果很想spec文件看起来很干净，那么简单的app也可以选择 fixtures, 但命名对象的时候最好带点语义。
  - 中等复杂程度的app
    - 如果确定app以后不会变得更加复杂，可以考虑使用 fixtures, 但需要规划好object的名称，对应好不同yml文件中对象的关系。
    - 也可以考虑第三方工具。
  - 复杂的app, 推荐第三方工具，花时间熟悉句法，多读doc。
- 三种方法的使用前提都是明确清楚测试目的，反过来才会知道需要什么样的资料，尤其对于第三方工具来说，需要额外的句法来实现案例特征。
- 熟练简单、基本的用法，再染指复杂的功能。
