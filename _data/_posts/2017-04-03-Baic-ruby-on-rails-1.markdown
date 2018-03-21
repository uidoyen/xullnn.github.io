---
title:  "Ruby on Rails 基础问答一"
date:   2017-04-03 01:04:23
categories: [Programming]
tags: [Ruby & Rails]
---

Junior Rails Developer

### 软体工程部分

1 请列举至少7个git指令，并说明他们的作用。

* `git init` 为当前文件目录建立git本地仓库，开始追踪文件变更
* `git add .` 将所有变更加入git本地追踪记录
* `git commit -m "info"` 确认添加的所有变更，并记录相关信息
* `git status` 查看至上一次commit 以来有哪些文件发生了变更
* `git log` 查看git 的历史操作记录
* `git push remotename branchname` 将分支branchname 推送到名为 remotename 的远端仓库
* `git push remotename --all` 将所有文件推送到名为 remotename 的远端仓库
* `git pull branchname` 从远端仓库拉下分支 branchname

2 如何为专案新增连接到一个 remote repository?

`git add remote remotename xxxxxx.git`  

3 如果你在github上fork别人一个专案，之后该专案更新，你要怎么更新到最新版本？
fork之后，该专案会出现在你的github远端仓库中

复制仓库地址后 `git clone repositoryaddress.git` 将专案复制到本地

`git remote add remotename 你要拉取更新的专案的git地址`  添加用于更新的远端地址

if 使用`git fetch remotename` 将会拉下在此仓库地址的所有变更，但不会自动merge到你的本地专案

if 使用`git fetch remotename branchname` 会拿到某一个分支的变更

接着 `git merge remotename:master` 将拉下的内容合并到主干

if 使用`git pull remotename` 兼具 fetch 和 merge 的效果。


---

### 网页开发部分

1 如果有个要实践的功能如下：使用者确认购买某商品后，需要在下一个页面秀出成功购买的信息，此外还要把商品的购买信息email给使用者，你会怎么实作？

a. 先安装bootstrap 的flash套件，定义好相关文件，在下一个页面show出成功购买的信息，在对应controller 的对应action（比如orders _ controller 中的 create）下, 在`redirect_to xxx_path`，其中`xxx_path`应该是订单详情页面路径，后加上一行`flash[:notice] = "感谢您的下单，这是您此次的购物明细"`

b. `rails g mailer OrderMailer` 生成order相关的mailer文件 app/mailers/order _ mailer.rb

c. 在 app/mailers/application _ mailer.rb中 `default from: "xxxxxxx@gmail.com"` 设置发出邮件的地址

d. 在 order _ mailer.rb 中定义好 `notify_order_placed` 这个method，method 的end前加上 `mail(to: @who.email, subject: "邮件title内容")`
`touch app/views/order_mailer/notify_order_placed.html.erb` 生成之前定义好的method的 template，写好页面表单。

e.到对应controller中的生成订单的action下，加入`OrderMailer.notify_order_placed(@order).deliver!`

f.到config/environments/development.rb 和 config/environments/production.rb 与 config/application.yml 中完成对应的configuration

g.进行本地测试。[mailer的内容在官方guide上有说明](http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-configuration-for-gmail)
**mailers/xxxxx.rb与controller.rb是很类似的。**

2 请列举你知道的几种HTTP status code, 并指出他们的意义。

HTTP status code 即 HTTP 状态码， 是web服务器用来告诉客户端发生了什么事的一种标准化语言。

目前HTTP status code 分为五大类，分别从1到5开头。
* 100-102  信息提示 这是一类临时响应的状态码，代表请求已被接受，需要继续处理。
* 200-207  成功 代表请求已经成功被服务器收到，理解，并接受。
* 300-307  重新定向 代表需要客户端进行进一步操作才能完成请求，后续请求的地址（重新定向目标）在本次响应的Location域中指明。
* 400-4xx  客户端错误  代表客户端发生发生了错误妨碍了服务器的处理。
* 500-510  服务器错误  代表服务器在处理请求的过程中有错误或者状态异常。

最常用的HTTP status code 有：

> * 200 OK 服务器成功处理了请求(最常见)
* 301/302 Moved Permanently 重新定向 请求的url已经移走。Resoponse 中应该包含一个Location URL ，说明资源现在所处的位置。
* 304 Not Modified 未修改 如果客户端发送了一个带条件的GET请求且该请求已经被允许，而文档的内容（自上次访问以来或者根据请求条件）并没有改变，则服务器返回这个状态码。
* 404 Not Found 未找到资源 请求失败，请求所希望得到的资源未在服务器上找到。
* 500 Internet Server Error 服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。一般来说，这个问题都会在服务器的程序码出错的时候出现。

---

## 前端部分

1 如果一个div 元素里所有子元素都有 float: left; 这个属性时，这个div 会发生什么情况，为什么？
所有的子元素都会向左浮动，以父元素左边边界为基准对齐。但某些子元素尺寸较大时可能会导致其突破父元素边界，此时如果要完全包裹子元素，需要添加 `style="overflow: atuo"` 给父元素以修复此问题。
[对float解释得很具体的视频](https://www.youtube.com/watch?v=xFGBNv2KeVU)


![Snip20170329_107.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/WSPsAEPWSnmglZ8AIvDa_Snip20170329_107.png)

2 css属性 box-sizing 里面的 content-box 和 border-box 之间的差异是什么？

content-box 所定义的元素的width和height 值，没有包含padding和border的宽度，元素的实际宽度等于：自身宽度+padding宽度+border宽度。换句话说它所约束的是内容部分的尺寸。

border-box 所定义的width 和 height 则指的是包含了padding 和 border的宽度，相当于绝对控制了元素整体尺寸。


![Snip20170329_108.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/jfj9kvLHQHKhnXkDaUw1_Snip20170329_108.png)

3 如何解决各个浏览器之间，预设元素值属性不同的问题？

使用统一的 reset.css 或 normalize.css 来重新设定，让不同浏览器的元素有相同的预设值。

---

## Rails 部分

1 在routes.rb 中写 resources :posts， 请说明在controller中要定义哪七个对应的action，并简单说明他们的作用。

* `index` 定义首页（索引，get所有post的资源集）
* `show` get到单个post的资源
* `new`  get到新建post的表单页面
* `create` post（送出并记录）新的post对象
* `edit` get到编辑post的表单页面
* `update` patch(送出并更新)post的资料
* `destroy` delete（删除）特定post

2 view的逻辑太复杂不是好的实践，你会怎么简化？

使用对应的helper封装view中的逻辑判断部分，简化view代码。多个view中有同一段使用率较高的代码可以封装为partial 在view中引用简化代码。

3 如果今天有一个Rails 商业 app， 他需要让产品有不同的规格不同属性，譬如买一件衣服M号1000元，库存10件，S号800元库存5件，你会怎么设计这个Product关联的model?请写出各个model的栏位跟关系。

先生成一个Model叫 **Product** ，包含： title:string description:text

然后生成一个Model **Product _ record** ，包含： product _ id:integer, quantity:integer, size:integer, price:integer

接着设定二者的关系：

在models/product.rb中declare `has_many :product_records` `accepts_nested_attributes_for :product_records`

在models/product _ record.rb 中 declare `belongs_to :product`

这样专门有一model来存储商品的信息，product就可以拥有不同的参数

4 n+1 query 是什么？可以怎么解决？

*n+1 query 指的是在进行each do  类似的查询动作时发生的重复查询的问题。*

比如现在有两个Model,一个叫Group,一个叫Post，我分别在

models/group.rb 中设定
```ruby
class Group < ApplicationRecord
has_many :posts
```

models/post.rb 中设定
```ruby
class Post < ApplicationRecord
belongs_to :group
```
在groups _ controller中：
```ruby
def index
  @groups = Group.all
end
```
那么如果我在view中用到这样的语句
```ruby
<% @groups.each do |group| %>
  <%= group.posts.map(&:title) %>  # 以阵列形式列出当前group _ id下的所有posts的title
<% end %>
```

那么执行这个回圈的时候假设 group 有 n 个，那么总共就会进行**n+1**次query

* group第1次： 查询到所有的groups

* post第1次： 从所有posts中查到 group _ id = 1 的posts的title
* post第2次： 从所有posts中查到 group _ id = 2 的posts的title
* post第3次： 从所有posts中查到 group _ id = 3 的posts的title
* ......
* post第n次： 从所有posts中查到 group _ id = n 的那个post的title


1次对group的query+n次对post的query，就是n+1 次。

**解决方法：**
把groups _ controller 中的 index 修改一下
```ruby
def index
    @groups = Group.includes(:post)
end
```
那么再执行view中那个回圈时就是

* 第一次：查询到所有的groups
* 第二次：从所有posts中一次性去捞 group _ id = 1,2,3...n 的posts的title


https://github.com/flyerhzm/bullet

gem 'bullet' 是一个可以侦测app中n+1 query现象的gem，说明文档中也有用一个Demo对此现象简单说明。

`rails new test_bullet`新建专案

生成两个model:  `post` 和 `comment` 的构架

![屏幕快照 2017-03-30 下午11.24.40.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/DTfJbA7EQrqlpsi0cxDz_%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-30%20%E4%B8%8B%E5%8D%8811.24.40.png)

设定model 间的关系：

![Snip20170330_4.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/yrE3LyC5SEkUGYqr6knQ_Snip20170330_4.png)


![Snip20170330_6.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/d3c1FAMTCCNIv6sbpUA0_Snip20170330_6.png)

posts _ controller 中默认的 index 是这样：

![Snip20170330_8.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/sdZGGPQQTiWM6bZfBLv7_Snip20170330_8.png)

在 app/views/posts/index.html.erb 中加上一行：


![Snip20170330_7.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/rMEUrAUR5mZfjpHS8AKw_Snip20170330_7.png)


`rails s` 运行本地服务器，打开网页首先会弹出一个窗口：

![屏幕快照 2017-03-30 下午10.48.30.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/zJO92eKSA6Wq4SKoQftU_%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-30%20%E4%B8%8B%E5%8D%8810.48.30.png)

x掉后

![Snip20170330_3.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/CXoiAgQQTRWOUrVWn4Ht_Snip20170330_3.png)

查看服务器终端：

![Snip20170330_1.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/s1RTzBIUSoySqZ5sOzi0_Snip20170330_1.png)

总共发起了3次（post数量为2）query

接着修改index

![Snip20170330_11.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/CFxDZH51QSOFxv2aic9G_Snip20170330_11.png)

然后再次打开主页， 查看服务器终端：

![Snip20170330_10.png](http://user-image.logdown.io/user/22166/blog/21189/post/1650433/RI27ZDQRQyBMwCWyZgQh_Snip20170330_10.png)

这次只发起了2次query

5 我们经常用以下script 寄电子邮件给所有注册的使用者。但当网站规模增大后在伺服器执行此script时经常会当掉，该如何解决
```ruby
User.all.each do |user|
......
end
```
解决方法是用batch finding让rails一次只发送给定的数量：
```ruby
User.find_each(:batch_size => 100) do |user|
......
end
```
这样一次就只会捞100个user来发送，而不会无限制地发送。
参考自：（https://ihower.tw/rails/performance.html）ActiveRecord和SQL 部分
