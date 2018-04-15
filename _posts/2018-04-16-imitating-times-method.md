---
title:  "Imitating a n.times method in Ruby"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails, Ruby]
---

失眠了，突然想到之前David那本关于Ruby的书中讲的关于自己用loop写一个 `n.times` 方法的例子。只记得整体思路，关于`yield`的具体用法细节失联，google了下。

#### 1 关于 yield

- yield 通常用在 method 内部
- yield 代表马上执行一次传给该 method 的 block
- yield(arg) 代表马上执行传给该 method 的 block 并把 arg 作为block parameter传进block
- yield 默认该 method 必须要接受(require)一个block作为参数，所以内部有yield而在call这个method时没带block会报错。

#### 2 不使用 yield 的方式

```ruby
class Integer
  def mimic_times(&block)
    n = 0
    while n < self
      block.call(n) # 把当前计数器次数传给 block
      n += 1
    end
    return self
  end
end
```

测试：

```ruby
2.5.0 :001 > require_relative 'imi_times' # 也可以直接在 irb 中完成
 => true

2.5.0 :002 > 4.mimic_times { |i| puts "time -- #{i}" }
time -- 0
time -- 1
time -- 2
time -- 3
 => 4

2.5.0 :003 > 4.mimic_times { puts "time(no block param)" }
time(no block param)
time(no block param)
time(no block param)
time(no block param)
 => 4

2.5.0 :004 > 4.mimic_times
Traceback (most recent call last):
        3: from /Users/caven/.rvm/rubies/ruby-2.5.0/bin/irb:11:in `<main>'
        2: from (irb):4
        1: from /Users/caven/imi_times.rb:18:in `mimic_times'
NoMethodError (undefined method `call' for nil:NilClass)
```


#### 3 使用 yield 的方式

```ruby
class Integer
  def mimic_times
    n = 0
    while n < self
      yield(n) # 把当前计数器数字传给 block
      n += 1
    end
    return self
  end
end
```

测试:

```ruby
2.5.0 :001 > require_relative 'imi_times'
 => true
2.5.0 :002 > 4.mimic_times { |i| puts "time -- #{i}" }
time -- 0
time -- 1
time -- 2
time -- 3
 => 4
2.5.0 :003 > 4.mimic_times {puts "time(no block param)" }
time(no block param)
time(no block param)
time(no block param)
time(no block param)
 => 4
2.5.0 :004 > 4.mimic_times
Traceback (most recent call last):
        3: from /Users/caven/.rvm/rubies/ruby-2.5.0/bin/irb:11:in `<main>'
        2: from (irb):4
        1: from /Users/caven/imi_times.rb:6:in `mimic_times'
LocalJumpError (no block given (yield))
```

#### 4 关于不给 block 时的报错

yield 风格的报错：

`LocalJumpError (no block given (yield))`

block.call 风格报错：

`NoMethodError (undefined method 'call' for nil:NilClass)`

注意两个Error的class不一样。在ruby原版的 `times` 方法中，不传 block 并不会报错，而是返回一个 enumerator 对象。

```ruby
2.5.0 :001 > 4.times
 => #<Enumerator: 4:times>
2.5.0 :002 > enum = 4.times
 => #<Enumerator: 4:times>
2.5.0 :003 > enum.next
 => 0
2.5.0 :004 > enum.next
 => 1
2.5.0 :005 > enum.next
 => 2
2.5.0 :006 > enum.next
 => 3
2.5.0 :007 > enum.next
Traceback (most recent call last):
        3: from /Users/caven/.rvm/rubies/ruby-2.5.0/bin/irb:11:in `<main>'
        2: from (irb):7
        1: from (irb):7:in `next'
StopIteration (iteration reached an end)
```

**关于Enumerator又是另一个更大的话题，这里只关注如何用简单的方法实现基础的 `times` 效果。**

#### 5 小结

不管使用`yield`还是使用 `&block` + `block.call(arg)` 思路都是一样的。

- `n.times` 这里的 `n` 是一个integer, 实际就是 Integer 这个 class 的一个 instance ，所以可以直接打开 class Integer 在里面写instance method
  - 如果使用`yield`风格，那么定义method名称时后面不用带参数
  - 如果使用`block.call`风格，那么定义method名称时后面带上(&block)
- loop 开始前初始化一个计数器 variable = 0
- loop 断点设置为 n < self ，也就是最后一次loop执行是 n == self 的时候
- 用 yield 或 block.call 执行 block，并把当前计数器数字传入 block
- 每一次loop循环末尾计数器 +1
- 计数器累加到 n == self + 1 时，loop 打断不再执行，return 方法开头给出的整数。
