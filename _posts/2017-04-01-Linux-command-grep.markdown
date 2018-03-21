---
title:  "grep系列命令用以查找信息"
categories: [Programming-basics]
tags: [Ruby & Rails, Command Line]
---

grep是一个最初用于Unix操作系统的命令行工具。在给出文件列表或标准输入后，grep会对匹配一个或多个正则表达式的文本进行搜索，并只输出匹配（或者不匹配）的行或文本。


这里只引入几个简单的最常用的grep用法，[关于更详细的说明可以点击查看wiki上的条目](https://zh.wikipedia.org/wiki/Grep)。

#### 1 `grep linux computer.text`

这个例子中，grep会返回 computer.text 这个文件中所有包含 linux(小写) 的文本行。要注意的是这里grep不会返回 "Linux",  "liNux", "LINuX" 这类文本的搜索结果，默认情况下会识别大小写。所以，grep会配合一些参数来设置搜索选项，比如上面的例子中如果要不区分大小写：

`grep -i linux computer.text`
> 这里`-i`的意思是 "--ignore-case"忽略大小写的意思。

#### 2 grep用于在git中查找信息

当我们新建专案时执行`git init`就将指定的所有文件加入了git的追踪。

**使用`git grep touch`会列出所有git追踪下所有含touch(小写)的文本行：**

```ruby
_includes/head.html:  <link rel="apple-touch-icon" sizes="57x57" href="{{"images/favicons/apple-touch-icon-57x57.png" | prepend: site.baseurl }}">
_includes/head.html:  <link rel="apple-touch-icon" sizes="60x60" href="{{"images/favicons/apple-touch-icon-60x60.png" | prepend: site.baseurl }}">
_includes/head.html:  <link rel="apple-touch-icon" sizes="72x72" href="{{"images/favicons/apple-touch-icon-72x72.png" | prepend: site.baseurl }}">
 sizes="60x60"href="/images/favicons/apple-touch-icon-60x60.png">

_site/2016/welcome-to-jekyll/index.html:  <link rel="apple-touch-icon"sizes="76x76"href="/images/favicons/apple-touch-icon-76x76.png">
_site/2016/welcome-to-jekyll/index.html:  <link rel="apple-touch-icon" sizes="114x114" href="/images/favicons/apple-touch-icon-114x114.png">
_site/2016/welcome-to-jekyll/index.html:  <link rel="apple-touch-icon" sizes="120x120" href="/images/favicons/apple-touch-icon-120x120.png">
......
```

如果要排除大小写区分，使用`git grep -i touch`

**如果想要在搜索结果中显示文本行的行号，可以再加上`-n` 比如：`git grep -i -n jekyll` 结果就会附上每条文本在其对应文本中的行号。**

```ruby
Gemfile.lock:89:      jekyll (~> 3.0)
Gemfile.lock:90:    jekyll-coffeescript (1.0.1)
Gemfile.lock:92:    jekyll-default-layout (0.1.4)
Gemfile.lock:93:      jekyll (~> 3.0)
Gemfile.lock:94:    jekyll-feed (0.9.2)
......
Gemfile.lock:第n行:
```

**如果只想查看含有某个特定字符的文件的文件名，可以用`git grep --name-only css` 这样会返回所有含有css的文件列表（不是具体的文本行）。**

```ruby
_includes/head.html
_site/2016/welcome-to-jekyll/index.html
_site/2017/Frequent-problems-when-deploying-to-heroku/index.html
_site/categories/index.html
_site/css/main.css
_site/css/uno.css
_site/index.html
_site/js/main.js
......
```

**上一个例子中如果想进一步想显示每个文件中含有多少个 "css" ，就是让系统帮你数好，那么就`git grep -c css` (不需要--name-only)**

```ruby
_includes/head.html:1
_site/2016/welcome-to-jekyll/index.html:1
_site/2017/Frequent-problems-when-deploying-to-heroku/index.html:1
_site/categories/index.html:1
_site/css/main.css:2
_site/css/uno.css:4
_site/index.html:1
_site/js/main.js:1
......
```
`-c`的意思就是`--count`。



#### 3 在heroku logs中查找特定信息

部署到heroku是很容易出错的环节，准确快速地从heroku的logs中找到问题可以提高解决问题的效率。

查找heroku log信息的方式是`heroku logs`，如果用grep抓取则可以是`heroku logs | grep -i error`。如此就会查找到log信息中所有带有error字符的文本行。这里的使用跟在git中很类似，只是前半部分存在区别。

这是使用`heroku logs | grep -i common`的结果

```ruby
caven@CavendeMacBook-Pro ⮀ ~/learnruby/shop ⮀ ⭠ caven-fix-error01 ⮀ heroku logs | grep -i common
2017-04-02T02:19:58.390743+00:00 app[web.1]: I, [2017-04-02T02:19:58.390684 #4]  INFO -- : [74cc96c9-326e-4755-aaa4-08f135bcbd1a]   Rendered common/_flashes.html.erb (0.5ms)
2017-04-02T02:19:58.420455+00:00 app[web.1]: I, [2017-04-02T02:19:58.420407 #4]  INFO -- : [74cc96c9-326e-4755-aaa4-08f135bcbd1a]   Rendered common/_navbar.html.erb (29.3ms)
2017-04-02T02:19:58.421419+00:00 app[web.1]: I, [2017-04-02T02:19:58.421364 #4]  INFO -- : [74cc96c9-326e-4755-aaa4-08f135bcbd1a]   Rendered common/_footer.html.erb (0.4ms)
2017-04-02T02:20:02.522177+00:00 app[web.1]: I, [2017-04-02T02:20:02.522118 #4]  INFO -- : [183aff59-999c-4cbc-898a-7407a76b80fd]   Rendered common/_flashes.html.erb (0.1ms)
2017-04-02T02:20:02.530441+00:00 app[web.1]: I, [2017-04-02T02:20:02.530377 #4]  INFO -- : [183aff59-999c-4cbc-898a-7407a76b80fd]   Rendered common/_navbar.html.erb (8.1ms)
2017-04-02T02:20:02.530661+00:00 app[web.1]: I, [2017-04-02T02:20:02.530600 #4]  INFO -- : [183aff59-999c-4cbc-898a-7407a76b80fd]   Rendered common/_footer.html.erb (0.0ms)
caven@CavendeMacBook-Pro ⮀ ~/learnruby/shop ⮀ ⭠ caven-fix-error01 ⮀
```

`heroku logs | grep -i -E 'error|fatal'` 到log中去找含有error和fatal的行


#### 4 组合条件的grep

`git grep -E -i -n 'has_many|belongs_to'` 可以忽略大小写抓出 含有 has_many 或 belongs_to 的文本行，并标出行号。
> `-E`的意思是 “--extended-regexp” 扩展的正则表达式。

```ruby
app/models/cart.rb:2:  has_many :cart_items
app/models/cart.rb:3:  has_many :products, through: :cart_items, source: :product
app/models/cart_item.rb:2:  belongs_to :cart
app/models/cart_item.rb:3:  belongs_to :product
app/models/comment.rb:2:  belongs_to :product
app/models/comment.rb:3:  belongs_to :user
app/models/order.rb:5:  belongs_to :user
app/models/order.rb:6:  has_many :product_lists
app/models/photo.rb:3:  belongs_to :product
app/models/product.rb:3:  has_many :photos
app/models/product.rb:4:  has_many :comments
app/models/product.rb:7:  belongs_to :location
app/models/product_list.rb:2:  belongs_to :order
app/models/user.rb:7:         has_many :comments
app/models/user.rb:13:  has_many :orders
app/models/welcome.rb:2:  has_many :products
app/models/welcome.rb:3:  has_many :photos, through: :product
config/initializers/new_framework_defaults.rb:17:# Require `belongs_to` associations by default. Previous versions had false.
config/initializers/new_framework_defaults.rb:18:Rails.application.config.active_record
.belongs_to_required_by_default = true
```

---


关于grep的用法还有很多，参数设置也细分了很多种，加上组合使用方法就更加复杂。
这里有可供参考的网站：

* https://zh.wikipedia.org/wiki/Grep
* http://gitbook.liuhui998.com/4_8.html
* https://git-scm.com/book/zh/v2/Git-工具-搜索
* https://git-scm.com/docs/git-grep
