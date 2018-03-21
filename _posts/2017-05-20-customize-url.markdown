---
title:  "Customize url in Rails"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails]
---

Rails中如果我们用 `resources :users` 这种路由写法，那么user的show页面网址一般都是 /users/24 , /users/108 ... 这样，这样有一个坏处是很容易猜到有多少笔user的资料。

---

> Notably, the Rails routing system calls to_param on models to get a value for the :id placeholder. ActiveRecord::Base#to_param returns the id of a model, but you can redefine that method in your models. For example, given
> ```ruby
class User
  def to_param
    "#{id}-#{name.parameterize}"
  end
end
```
> we get:
>`user_path(@user) # => "/users/357-john-smith"`
> [Rails Guide 出处](http://guides.rubyonrails.org/active_support_core_extensions.html)

在Rails中，默认使用 `to_param` method处理 `params[:id]` 以及 `@user` 拿到的数据是什么样子。Rails内建默认的`to_param`的method是 `"self.id"`,上面的例子中我们在需要修改url的Model中重新定义了这个method以达到改写目的。

`to_param` 会默认使用`to_s`的方法来转换一次传进的数据：

```ruby
2.3.1 :004 > 123678.to_param  
=> "123678"
```

在controller中常会用到：
`@user = User.find(params[:id])`或者

`@user = User.find_by_member_id(params[:id])`(等同于`@user = User.find_by(member_id: params[:id])`)

这种查找object的方法，一个重点是 `find` 这个方法会[默认对将要拿去query资料的字串（比如 params[:id]传来的字串）先进行 `to_i` 处理：](http://api.rubyonrails.org/classes/ActiveRecord/FinderMethods.html#method-i-find)
> Find by id - This can either be a specific id (1), a list of ids (1, 5, 6), or an array of ids ([5, 6, 10]). If one or more records can not be found for the requested ids, then RecordNotFound will be raised. If the primary key is an integer, find by id coerces its arguments using to_i.

```ruby
2.3.1 :004 > "6ytg89k03re".to_i
 => 6

 2.3.1 :001 > "um1xb90pr".to_i
 => 0

 2.3.1 :002 > "mc7819lkh0e".to_i
 => 0

 2.3.1 :003 > "1945pxq89tf".to_i
 => 1945

 2.3.1 :003 > "123-caterpiller-big".to_i
 => 123
```

也就是说 `to_i` 方法只会保留以数字开头的连续的整数，后面的所有东西都会略去。**但注意 `find_by` 方法不会，传什么东西过来，find_by 就会拿什么去query。** find_by 拿到 "123-caterpiller-big" 就会直接拿这整个字串去query 而不会像 find 那样只留下 123 。

---

上面是我们基本会用到的基础资料，下面用一个实例来看看如何自定义url。假设我们现在有一个 Product 的 model，有 title , description 等栏位，现在我们想把show页面的网址改掉，不用 resources :products 默认的后面跟ID的方式，比如 products/12 , products/30 这样的网址。

### 1 把网址改成products/id-product.title
因为我们可以redefine `to_param`,仿造guide 中的写法我们可以到 **product.rb** 中：

```ruby
class Product < ApplicationRecord

    def to_param
      "#{self.id}-#{self.title}"
    end

...
end
```

如此，在controller里，`@product = Product.find(params[:id])` 这一行 params[:id] 带来的数据就会是 "product的ID"-"product的title" 这样的组合，比如 "35-niceshoe" 这样的字串， 但find拿到会会先 `to_i`（就只剩下前面的35） 然后去资料库比对id。所以这样改to_param没有影响controller 拿id去 query资料。

view里面 `product_path(@product)` 中 `@product` 不经过处理直接呈现原来的字串，变成  35-niceshoe ，所以网址就会变成 /products/35-niceshoe 。

### 2 网址改成 [uuid](https://zh.wikipedia.org/wiki/%E9%80%9A%E7%94%A8%E5%94%AF%E4%B8%80%E8%AF%86%E5%88%AB%E7%A0%81)

比如 /products/e8055bda-49da-4ea6-8e6a-dde0c7a4d3f5 这样的网址，那首先我们需要：

1.给product加一个栏位来存uuid;
2.给以前没有uuid的product加上uuid;

`rails migration add_uuid_column_to_product`

```ruby
class AddTokenToProducts < ActiveRecord::Migration[5.0]
  def change
    add_column :products, :random_token, :string        #用random_token这个栏位来存uuid
    add_index :products, :random_token, :unique => true #加上索引

    Product.find_each do |p|                            #给所有products生成uuid并存进他们的random_token栏位
      p.update(:random_token => SecureRandom.uuid)
    end
  end
end
```

`rake db:migrate`


接着到 **product.rb** 中加：

```ruby
class Product < ApplicationRecord

    before_create :generate_random_token  #确保新建product前生成uuid

    def to_param                          #让传的数据变成product自己的random_token
      "#{self.random_token}"
    end

    protected

    def generate_random_token             #如果product的random_token为空则生成一个uuid
        self.random_token ||= SecureRandom.uuid
    end

end
```

在 controller 里， 原来的`find(params[:id])` 这里就要改成 `find_by_random_token(param[:id])`。如果用 find 那么 params[:id] 传进来的 uuid 比如 e8055bda-49da-4ea6-8e6a-dde0c7a4d3f5" 一串经过 `to_i` 的处理，这个例子中会返回 0 ， 拿着这个 0 去比对id查询资料肯定会失败的，所以这里用 `find_by` 拿着整个uuid 去查询，而且是 find_by_random_token 也就是比对 random_token 去找而不是去找 product的 id 。

view里面的 `product_path(@product)` 就不用改了，因为 `@product` 拿到的是整个 "e8055bda-49da-4ea6-8e6a-dde0c7a4d3f5"，所以生成的网址是 /products/e8055bda-49da-4ea6-8e6a-dde0c7a4d3f5

### 3 指定位数的uuid + product.title

/products/35-niceshoe 很容易让人猜到有多少笔资料

/products/e8055bda-49da-4ea6-8e6a-dde0c7a4d3f5 太丑

如果我想要有一定的隐秘性而又要有一定的识别度，比如 /products/e8055bd-niceshoe 这样的网址呢

思路：

>   def to_param
    "#{self.random_token}-#{self.title}"
  end

self.random_token 拿到的是整个 uuid， 那就去限制存入 random_token 整个 column 中的 uuid 的位数。因此到 product.rb 中修改 `generate_random_token`:

```ruby
class Product < ApplicationRecord

  def generate_random_token
  # self.random_token ||= SecureRandom.uuid
    self.random_token ||= SecureRandom.uuid[0..7] # 这样存进random_token的就只有前面7位的uuid
  end

end
```

controller 跟第二种情况一样需要用 `find_by` 传完整的 `to_param` 那么传过来会是 "e8055bd-niceshoe" 。显然拿着整个带title的字串去比对资料库里只有7位的random_token是会报错的，所以在controller中会去query product 的地方（edit, update, destory ...）都也要限制用前7位，所以写法是

`@product = Product.find_by_random_token(params[:id][0..7])`

这样一来网址是中文都可以的，因为只取了前面7位uuid去查询。比如可以使用 /products/aab0c24a-中文商品名 这样的网址也没问题。


--


#### 总结

- 总体思路是首先 redefine `to_param` 决定  parmas[:id] 和 @product 会拿到什么？

- 其实要考虑 `find` 和 `find_by` 的区别，想好用什么去query 资料更合理。

- 最后要检查所有会查询 product 的地方，考虑传过来的会是什么，这里会不会经过其他处理再去query资料。

- 报错的时候首先看看用来 query 所传的数据是什么？数据为什么是这样？为什么拿着这个去query会报错？
