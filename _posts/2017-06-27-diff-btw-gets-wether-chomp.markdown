---
title:  "Difference between gets and gets.chomp"
categories: [Command Line ℗]
tags: [Command Line]
---

Ruby 中 gets, gets.chomp, gets.chomp() 的区别。

```ruby
2.3.1 :002 >
2.3.1 :003 >   name = gets
caven
 => "caven\n"                      # gets 会将接收到的数据转换为string 并在末尾换行 '\n'
2.3.1 :004 > name = gets.chomp     
caven
 => "caven"                        # gets带chomp 则会默认“吃”掉换行符号
2.3.1 :005 > day = gets
monday
 => "monday\n"
2.3.1 :006 > day.chomp
 => "monday"
2.3.1 :007 > day.chomp("ay\n")
 => "mond"                         # gets.chomp()带括号，里面加上参数包含换行符号（这里必须是字串格式，并且是get到的字串的末尾的字符）

-----------------------------------下面是错误情况示范-------------------------------------

 2.3.1 :008 > day.chomp(ay\n)
 2.3.1 :009?>                       # chomp()括号中参数不是string 格式，irb 以为括号里是一个待赋值的变数参数
 2.3.1 :010 >     end               # 所以这里出现了loop 的情况，要输入end 才会中断回圈并报错。  
 SyntaxError: (irb):8: syntax error, unexpected $undefined
 day.chomp(ay\n)
              ^
 from /Users/caven/.rvm/rubies/ruby-2.3.1/bin/irb:11:in `<main>'
 2.3.1 :011 > day.chomp("ay")       # 括号中字串没有完整地加上 "\n", chomp无效
  => "monday\n"

2.3.1 :013 > day.chomp("on")        # 字串不是末尾的字符，也无效
 => "monday\n"
2.3.1 :014 >

```

参考来源：

https://stackoverflow.com/questions/22166108/difference-between-gets-gets-chomp-and-gets-chomp


http://ruby-doc.org/docs/ruby-doc-bundle/Tutorial/part_02/user_input.html
