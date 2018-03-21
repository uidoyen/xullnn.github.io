---
title:  "How to write a seed in Rails"
date:   2017-04-29 01:04:23
categories: [Programming]
tags: [Ruby on Rails, database]
---


seed是开发过程中一个小而强的工具，避免了每次重置资料库后需要手动去增加单笔资料用于测试。开发到一定阶段，用好seed可以省出部分时间和精力。

**seeds.rb** 这个文件在 app/db/seeds.rb这里，从文件类型可以看出，里面的代码是用ruby写的。

一个简单的示例用来演示 seed 的基本用法：

#### 准备工作：
`rails new seed-sample`

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo ⮀ rails new seed-sample
      create
      create  README.md
      .....
      .....
      run  bundle exec spring binstub --all
* bin/rake: spring inserted
* bin/rails: spring inserted
caven@CavendeMacBook-Pro ⮀ ~/demo ⮀
```

`cd seed-sample`

`git init, git add .....`

`rails g scaffold city name:string description:text populaton:integer`

然后会产生一个migrate文件：

```ruby
class CreateCities < ActiveRecord::Migration[5.0]
  def change
    create_table :cities do |t|
      t.string :name
      t.text :description
      t.integer :population

      t.timestamps
    end
  end
end
```

接着 `rake db:migrate`

这样就建立好了用于演示的model以及资料库栏位，这个model叫 City , 有三个栏位，现在来看用seed如何向栏位中注入信息。

---

在console中，如果想建立一条city的资料可以怎么做？
`rails c`
接着
 `City.create(name:  "Leshan", description: "cool", population: "19999")`

 ```ruby
 2.3.1 :001 > City.create(name:  "Leshan", description: "cool", population: "19999")
    (0.1ms)  begin transaction
   SQL (0.4ms)  INSERT INTO "cities" ("name", "description", "population", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["name", "Leshan"], ["description", "cool"], ["population", 19999], ["created_at", 2017-04-30 02:08:54 UTC], ["updated_at", 2017-04-30 02:08:54 UTC]]
    (1.7ms)  commit transaction
  => #<City id: 1, name: "Leshan", description: "cool", population: 19999, created_at: "2017-04-30 02:08:54", updated_at: "2017-04-30 02:08:54">
 ```

这种写法其实就可以用在Seed中。

#### 1 建立一笔资料

只需在seeds.rb中写：

```ruby

City.create(name:  "Leshan", description: "cool", population: "19999")

puts "--------------------------Successfully created a city-----------------------------"

```

使用seed只需 `rake db:seed`

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo/seed-sample ⮀ ⭠ master± ⮀ rake db:seed
--------------------------Successfully created a city-----------------------------
caven@CavendeMacBook-Pro ⮀ ~/demo/seed-sample ⮀ ⭠ master± ⮀
```

这样我们就用seed建立了一笔city资料。

#### 2 笨办法建立多笔资料

**方法一：**

```ruby
City.create(name:  "Leshan", description: "cool", population: "19999")
City.create(name:  "Shanghai", description: "nice", population: "100000")
City.create(name:  "New York", description: "awesome", population: "900000")

.....

```

`rake db：seed`

每一行都建立了一笔city的资料，当然如果过你愿意可以一直写下去。

**方法二：**

```ruby
City.create([
             {name: "Leshan", description: "cool", population: 100000 },
             {name: "Shanghai", description: "nice", population: 800000 },
             {name: "New York", description: "awesome", population: 900000}
            ])
```

`rake db:seed` 同样得到三笔资料。

**方法三：**

```ruby
city_items = [
              ["Leshan", "cool", 800000],
              ["Shanghai", "nice", 10000],
              ["New York", "cold", 900000]
              ]

city_items.each do |ci|
  City.create(:name => ci[0], :description => ci[1], :population => ci[2])
end
```

**以上三种方式得到的结果是一样的，只是采用了不同的写法，用哪种取决于你的偏好。**

#### 懒办法建立多笔资料

有时不需要资料栏位中的信息都是真实可循的，那么看利用索引来快速生成很多的类似的资料，栏位中的信息区别只在后面的索引数字。

比如我现在要生成100笔city资料， 我可不想一笔一笔得去写每个栏位，那么可以这样：

```ruby
100.times do |num|
    City.create(name: "City #{num}", description: "Description #{num}", population: "#{num}")
end
```

这样生成的资料会是：

```ruby
    [ 0] #<City:0x007ff913b12f60> {
                 :id => 912,
               :name => "City 0",
        :description => "Description 0",
         :population => 0,
         :created_at => Sun, 30 Apr 2017 02:56:24 UTC +00:00,
         :updated_at => Sun, 30 Apr 2017 02:56:24 UTC +00:00
    },
    [ 1] #<City:0x007ff913b00f40> {
                 :id => 913,
               :name => "City 1",
        :description => "Description 1",
         :population => 1,
         :created_at => Sun, 30 Apr 2017 02:56:24 UTC +00:00,
         :updated_at => Sun, 30 Apr 2017 02:56:24 UTC +00:00
    },
    [ 2] #<City:0x007ff913b00108> {
                 :id => 914,
               :name => "City 2",
        :description => "Description 2",
         :population => 2,
         :created_at => Sun, 30 Apr 2017 02:56:24 UTC +00:00,
         :updated_at => Sun, 30 Apr 2017 02:56:24 UTC +00:00
    },
    .
    .
    .
    .
    .
    .
```

一直到City 99。这样的好处是快，缺点是资料信息很雷同。

#### 一个需要注意的问题

> 在seed档没有修改过的情况下，连续运行两次 `rake db:seed` 后一次产生的资料并不会覆盖前一次的，而是在之前的基础上再增加。为了避免这种情况，可以在seeds.rb中最上面写一行 `City.destroy_all` 。这样每次跑seed前都会先清空之前的资料，当然这只限于开发环境下资料丢失不重要的情况下。

如果我们用1中的seed跑了两次 `rake db:seed` 那么会产生6笔资料，id从1到6, 主要三个栏位的信息会是重复的。
