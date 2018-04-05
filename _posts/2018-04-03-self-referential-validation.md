---
title:  "Self referential validation"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails]
---


社交网站的用户之间通常都可以相互 follow 或者 unfollow。Rails 中这种关系的实现使用的中间表，这个table只有两个栏位，一边是发出 follow 动作的 user 的id, 另一边是被follow一方的id。

比如这样的栏位设置

table name: relationships

|follower_id|followed_id|
|:-:|:-:|
|1|2|
|2|1|

注意，这个中间表实际是有方向性的，第一行是user1 follow 了user2, 第二行是 user2 follow了user1。顺序不一样，实际产生的关系也不一样。

单看第一行 user1 是主动的一方， user2 是被动的一方。第二行则反过来。

而在 model 中，要使用的名称又需要转换一下思维。

实际app中，我们通常会说， user1 有多少个 follower (被多少人跟随), 以及 user1 有多少following（跟随了多少人）。

那么到table中拿 user1 的 follower 实际是在找 user1 对应的被动关系，也就是找到右边一栏全是 user1.id的 rows，然后 vice versa。可以理解为

```ruby
user1.followers = Relationship.where(followed_id: 1)

user1.followings = Relationship.where(follower_id: 1)
```


但有一种情况是用户自己 follow 自己，虽然在 view 的逻辑中可以屏蔽掉自己follow自己的按键，也可以在 controller层做限制，但最好在 model 层做 validate。

自己 follow 自己

|follower_id|followed_id|
|:-:|:-:|
|1|1|

先确认没有加验证之前是否可以实现上面的情况：

```ruby
rails c --sandbox
# ...
u = User.first
Relationship.create(follower_id: u.id, followed_id: u.id)
# ...
=> #<Relationship id: 88, follower_id: 1, followed_id: 1, created_at: "2018-04-04 15:34:32", updated_at: "2018-04-04 15:34:32">
```

的确是可以的。

自定义一个验证

```ruby
class Relationship < ApplicationRecord
  # ...
  # 注意用单数
  validate :self_referential_not_allowed
  # ...
  private
    def self_referential_not_allowed
      if self.follower_id == self.following_id
        errors.add(:self_refer, "can't follow yourself.")
      end
    end
end
```

加上之后可以到 console 中再次验证，也可以在 rspec 中加上对应的测试看是否通过

**console 验证**

```ruby
rails c --sandbox
# ...
u = User.first
Relationship.create(follower_id: u.id, followed_id: u.id)
# ...
(0.2ms)  ROLLBACK TO SAVEPOINT active_record_1
=> #<Relationship id: nil, follower_id: 1, followed_id: 1, created_at: nil, updated_at: nil>
```

拿到了一个 ROLLBACK， 看来验证是有效的。

**Rspec中写测试**

spec/models/relationship_spec.rb

```ruby
# ...

before do
  @user = # ...
end

describe "Following behavior test" do
  context "one can't following itself" do
    it "is invalid that :follower_id and :following_id are same" do
      relationship = Relationship.new(follower_id: @user.id, followed_id: @user.id)
      relationship.valid?
      expect(relationship.errors[:self_refer]).to_not be_empty
    end
  end
end
```

测试应该通过。
