---
title:  "In an one to many association, what's the difference between Model.new and models.new?"
date:   2017-05-10 01:04:23
categories: [Programming]
tags: [Ruby on Rails, database]
---


今天发现在一个 one to many 的 association 中， 一个 model 中出现了 `models.new` 的用法。通常能见到的是 `Model.new` 这种用法。

## 背景说明：

有 Product, Cart, CartItem, 三个 model , 他们的关系如下：

**cart.rb**

```ruby
class Cart < ApplicationRecord
  has_many :cart_items
  has_many :products, :through => :cart_items, :source => :product

  def add_product_to_cart(product)
    ci = CartItem.new   # ----- 疑问就在这一行 -----
    ci.cart_id = self.id
    ci.product = product
    ci.number = 1
    ci.save
  end

end

```


**cart_item.rb**

```ruby
class CartItem < ApplicationRecord
  belongs_to :cart
  belongs_to :product
end

```

cart_items的主要栏位 **db/schema.rb**

```ruby
create_table "cart_items", force: :cascade do |t|

   t.integer  "cart_id"
   t.integer  "product_id"
   t.integer  "number",     default: 1
   ...

end
```

---


然后在 console 中操作：

```ruby
>> p = Product.first
>> c = Cart.create
>> c.add_product_to_cart(p)
>> CartItem.last
```

---

在cart.rb 中的 `add_product_to_cart` 这个method的第一行：

原来使用的是 `ci = cart_items.new` 之前没有注意到这里，在拥有一对多关系的model中用这种写法来新建object。当我在console中测试后发现一切正常,最后执行 `CartItem.last` 检查下刚刚建立的那个 cart_item, 返回的信息：

```ruby
#<CartItem:0x007fd1902c8490> {
               :id => 1,
          :cart_id => 4,
       :product_id => 8,
           :number => 1,
       :created_at => ...
       :updated_at => ...
   }
```

但是如果把第一行换成 `ci = CartItem.new` ,然后在 console 中执行相同的操作，返回的信息是：

```ruby
#<CartItem:0x007fd1902c86c0> {
               :id => 2,
          :cart_id => nil,
       :product_id => 8,
           :number => 1,
       :created_at => ...
       :updated_at => ...
   }
```

可以看到 `:cart_id => nil` 这次cart_item 没有抓到，cart_id 。

困惑的是，以前只见过 `Model.new` 这种写法， 这种写法在这里明显漏掉了什么环节。查了一段时间资料没能找到答案，后来到stackoverflow去提问，得到了一个[说得通的答案](http://stackoverflow.com/questions/43952096/in-an-one-to-many-association-whats-the-difference-between-model-new-and-model)。

要点有：

- 当我在 cart.rb 中写 `ci = cart_items.new` 的时候， 这里相当于给出了一个“沉默的前提”，告诉rails直接新建了一个belongs_to 当前这个cart的 cart_item, 同时也就把 :cart_id 这个资料暂存进了这个 cart_item 的 `:cart_id` 这个栏位。这种写法是行得通的。

- 当使用 `ci = CartItem.new` 的时候，只是让rails造了一个新的cart_item, 当然就少了上面说的那个前提，这只是个空的 cart_item。所以save之后 `:cart_id` 这个栏位是 nil 。

**受到这个答案的启发，我尝试这么写：**

```ruby
def add_product_to_cart(product)
  ci = CartItem.new
  ci.cart_id = self.id  # 加了这一行
  ci.product = product
  ci.number = 1
  ci.save
end
```

使用 `CartItem.new` 但是加了一行 `ci.cart_id = self.id` 把 `:cart_id` 给了 cart_item 。

在 console 中测试，没有报错也可以抓到 `:cart_id` 。

---
在console中 执行 `Cart.methods`，可以看到返回了696个method。 其中包含一个`new` method：

```ruby
  [448]    new(*args, &block)          Class (ActiveRecord::Inheritance::ClassMethods)

```

在console中 执行 `CartItem.methods`，可以看到返回了672个method, 其中也包含一个与上面相同的 `new` method。

在console中 执行 `Cart.last.cart_items.methods`，可以看到返回了387个method, 其中包含一个 `new` method:

```ruby
[229]       new(*attributes, &block)    CartItem::ActiveRecord_Associations_CollectionProxy (ActiveRecord::Associations::CollectionProxy)
```

在console中 执行 `Cart.last.cart_items.last.methods`，可以看到返回了428个method, 其中**没有**再包含一个 `new` method。


> **在第三种情况中的new方法和第一种中的出现了明显的差异，写的是 CartItem::ActiveRecord_Associations_CollectionProxy (ActiveRecord::Associations::CollectionProxy)**

> 暂译为 “关联集合代理” 的method，在[rails 的 api 说明中专门有提到这一类方法](http://api.rubyonrails.org/classes/ActiveRecord/Associations/CollectionProxy.html)，其中的讲 `build` 的地方就可以对应到这里的 `new` 。

其中一段说得很清楚了：

> （在这种一对多的关联model中, build/new） Returns a new object of the collection type that has been instantiated with attributes and linked to this object, but have not yet been saved. You can pass an array of attributes hashes, this will return an array with the new objects.

has been instantiated with attributes and linked to this object。说得很清楚了，当然用create也是可以的。

下面也用一个简短的例子来说明了这种用法的效果：

```ruby
class Person
  has_many :pets
end

person.pets.build
# => #<Pet id: nil, name: nil, person_id: 1>

person.pets.build(name: 'Fancy-Fancy')
# => #<Pet id: nil, name: "Fancy-Fancy", person_id: 1>

person.pets.build([{name: 'Spook'}, {name: 'Choo-Choo'}, {name: 'Brain'}])
# => [
#      #<Pet id: nil, name: "Spook", person_id: 1>,
#      #<Pet id: nil, name: "Choo-Choo", person_id: 1>,
#      #<Pet id: nil, name: "Brain", person_id: 1>
#    ]

person.pets.size  # => 5 # size of the collection
person.pets.count # => 0 # count from database
Also aliased as: new

```

---

#### 总结：

在一对多的关系中，在 has_many 一方的model.rb中写 `models.new`(这个是前者has的model)可以默认地告诉rails新建的后者默认属于前者并注入关联的外部键。
