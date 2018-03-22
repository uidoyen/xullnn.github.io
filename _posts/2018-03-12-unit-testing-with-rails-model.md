---
title:  "Basic unit testing with model in Rails"
categories: [Test ℗]
tags: [Rails Tests]
---

Rails MVC 构架中model是单元测试较多用到的地方，因为model中可以进行小而独立的操作，可以不依赖view和controller完成一些功能。实际上一个model就是一个ruby class，很多object的新建，数据的计算都可以在class层面在内存中完成。

而controller的主要目的就是在model和view之间充当枢纽，很少出现独立在controller中，不关联到view或者model而实现的小功能单元。

Rails guides：

> Model tests are used to test the various models of your application.

但没有具体的示范，只写了生成测试文件的指令

```
$ bin/rails generate test_unit:model article title:string body:text
create  test/models/article_test.rb
create  test/fixtures/articles.yml
```

下面是一些从其他地方收集到的简单示范：

### 1 column存在性和格式验证

这是一个[TDD](https://en.wikipedia.org/wiki/Test-driven_development)风格的测试小案例。其测试行为遵循下面这样的循环：

- Add a test
- Run all tests and see if the new one fails
- Write some code
- Run tests
- Refactor code
- Repeat

1 先在setup中写合法的 model 数据（会在每一个测试block执行前运行setup中的代码），也就是各个column是符合要求的
比如：

test/models/user_test.rb

```ruby
def setup
  @user = User.new(name: "Example User", email: "user@example.com")
end
```
2 写一个测试验证 `@user` 在这个背景下的合法性

test/models/user_test.rb

```ruby
test "should be valid" do
  assert @user.valid?
end
```

3 接着将其中某个column改成不合法的，断言这个新model对象合法，让测试不通过

test/models/user_test.rb

```ruby
test "name should be present" do
  @user.name = "    "
  assert_not @user.valid?
end
```

后续修改代码

- 到model中写对应的`validates :name, presence: true`，这会使上面的assert_not通过
- 最后测试一遍看 `validates` 是否能让测试通过。
- 其他column也依照这个模式

### 2 证某个column的长度

1 测试中先将@user对象需要限制长度的栏位值设置为超过限制长度的值。

test/models/user_test.rb

```ruby
test "email shoud not > 255 char" do
  @user.email = "a" * 244 + "@example.com"
  assert_not @user.valid?
end
```

没有添加model层验证前测试会失败，因为没有长度限制，不管多长都是 valid? 都是 true

2 接着添加验证到model

user.rb

```ruby
validates ... length: { maximum: 255 } ...
```

3 这样超长的email就不合法了。测试通过。

### 3 column 唯一性验证

1 思路是先复制setup中new的@user对象赋给variable，然后将@user对象存入数据库，然后看variable中存的那个对象是否合法，正常情况应该失败，因为没有在model层添加验证前是可以存入email相同的user对象的。

test/models/user_test.rb

```ruby
test "email addresses should be unique" do
  duplicate_user = @user.dup
  @user.save
  assert_not duplicate_user.valid?
end
```

2 然后到model中给email添加 `uniqueness: { case_sensitive: false }`（默认uniqueness: true为前提），限制email重复的情况不合法， 再次测试应该通过，即判定重复email的对象不合法。

### 关于 valid? 方法

Rails api:

> Runs **all the specified validations** and returns true if no errors were added otherwise false.

1 valid? 不是ruby自带的方法, 他是rails中的。
2 valid? 的判定标准是在model中添加的所有 validates 限制。

api中给出的例子：

```ruby
class Person
  include ActiveModel::Validations # 引入validates系列方法

  attr_accessor :name
  validates_presence_of :name # 限制 :name 栏位不能为空
end

person = Person.new
person.name = ''              # 新建person对象并让name为空
person.valid? # => false
person.name = 'david'         # 给 :name 赋值然后再测
person.valid? # => true
```

这个方法接受一个可选参数 valid?(content = nil)

> Context can optionally be supplied to define which callbacks to test against (the context is defined on the validations using :on).

也就是指定在哪个callback点进行验证。

给上面例子中的 `  validates_presence_of :name` 后加上 `  validates_presence_of :name, on: :new`
然后

```ruby
person = Person.new
person.valid?       # => true
person.valid?(:new) # => false
```


因此 `object.valid?` 就是对 object所在model中指定validates进行逐个验证。
