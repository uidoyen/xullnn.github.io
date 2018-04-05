---
title:  "Rspec on Rails - 1 - Setup"
categories: [Test ℗]
tags: [Rails Tests, Rspec]
---

Rails 中默认是不带Rspec的，需要手动安装和一些配置。

rspec-rails github 地址： https://github.com/rspec/rspec-rails

### 1 Gemfile 中加入 rspec-rails

注意是放在 group :development, :test 中

Gemfile

```ruby
group :development, :test do
  gem 'rspec-rails', '~> 3.6.0' # 版本可选
end
```

接着 `bundle`

### 2 检查测试用的数据库配置

config/database.yml 中，看下有没有对应的

```
test:
  <<: *default
  database: db/test.sqlite3
```
当然如果使用的是其他数据库这里作相应调整。

### 3 install并生成rspec的配置文件

执行

```ruby
bin/rails g rspec:install
```

会生成

```
Running via Spring preloader in process xxxx
      create  .rspec
      create  spec
      create  spec/spec_helper.rb
      create  spec/rails_helper.rb
```

- root下的 `.rspec` 文件是rspec的配置文件
- spec/ 文件夹是写测试文件的地方
- spec/spec_helper.rb 和 rails_helper.rb 有很多可自定义的选项。这两个文件中都有大量的注释说明，可以阅读了解。

`.rspec` 文件中默认有一行

`--require spec_helper`用来加载到刚刚生成的 spec/spec_helper.rb

可以再加上一行

`--format documentation` 让输出格式模拟doc，更加易读

最后 `.rspec` 文件会是

```
--require spec_helper
--format documentation
```

### 4 （可选）安装 spring-commands-respec 加快测试的启动时间

https://github.com/jonleighton/spring-commands-rspec

spring-commands-rspec 是一个针对 Rspec测试运行器的[binstub](https://github.com/rbenv/rbenv/wiki/Understanding-binstubs
)。

> Binstubs are wrapper scripts around executables (sometimes referred to as "binaries", although they don't have to be compiled) whose purpose is to prepare the environment before dispatching the call to the original executable.
In the Ruby world, the most common binstubs are the ones that RubyGems generates after installing a gem that contains executables. But binstubs can be written in any language, and it often makes sense to create them manually.

Gemfile

```
group :development, :test do
  # ...
  gem 'spring-commands-rspec'
end
```

执行 `bundle`

执行`bundle exec spring binstub rspec`生成新的binstub

这会在 bin/ 文件夹中生成 `rspec` 文件

这一步之后就可以尝试执行 `bin/respec` 看下有没有问题。

### 5 配置 `rails generator` 命令，定制会生成哪些rspec测试相关的文件

默认配置下，使用 `generate` 命令会生成很多测试相关的文件，比如fixtures, 测试helper的，测试 view 的，测试 routes 的等。但这些很多都用不到，可以关掉。

config/application.rb

```ruby
# require ...
# ...

module Projects
  class Application < Rails::Application
    config.load_default 5.1
    # ...

    config.generators do |g|
      g.test_framework :rspec,
        fixtures: false,
        view_specs: false,
        helper_specs: false,
        routing_specs: false
    end
  end
end
```

注意没有关掉 controller 和 model 测试文件的自动生成。

### 6 (可选)配合 guard-rspec 简化测试触发

https://github.com/guard/guard-rspec

guard 会在每次 spec 文件改动并存档的时候，自动跑一次刚刚变动到的spec文件中所包含的测试，这样免去了每完成了一次测试都要切到terminal中执行 `rspec` 的重复，可以让 guard 一直 watching 测试文件的变动。可以单开一个窗口跑guard，这样可以即时看到最新测试结果。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/guard-demo.gif)

RailsCasts 上的演示: http://railscasts.com/episodes/264-guard

**配置步骤：**

Gemfile

```ruby
group :development, :test do
  # ...
  gem 'guard-rspec'
end
```

`bundle`

`bundle exec guard init rspec`

接着就可以开始跑起来了

`bundle exec guard` 或者直接 `guard`



















the end.
