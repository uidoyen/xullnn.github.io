---
title:  "Authenticating form scratch 1 - signup and login"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails]
---

最初接触Rails时使用用户注册登录功能直接用的是[devise](https://github.com/plataformatec/devise)。只需要安装好gem然后简单执行几步指令就可以开始使用注册登陆等各种功能了，对于很多页面都会用到的 `current_user` 也是直接用。这只是把devise当做了一个黑箱使用，刚好在一个rails的教材中有这样一部分内容，然后结合RailsCasts中的两个教学视频[(1)signup&login](http://railscasts.com/episodes/250-authentication-from-scratch-revised), [(2)remember&reset](http://railscasts.com/episodes/274-remember-me-reset-password)。用差不多半个月时间把这部分内容做了10遍，总体思路是，**以cookies(和session)为验证基础，结合不同token栏位的设定实现这些功能。** 整个过程不需要新建 model 在 user.rb 的基础上即可完成。

### 1 Sigup
#### 1.1 建立 user Model， 安装 bcrypt
注册登录字面上很和谐，事实上注册实际是create一个user, 登录是建立 session ，分而视之。
这一步需要 `rails g model user email:string` 先给一个 email 栏位，记得migrate。

为了给 user 设定密码，在 user.rb 中加一行：
```ruby
class User < ApplicationRecord

  has_secure_password

```

这么做产生了效果是：
- 赋予user一对虚拟属性，`:password` 和 `:password_confirmation`，新建user时会对这两个值进行存在性验证和匹配验证
- 允许我们使用 `:password_digest` 这个栏位来存储 bcrypt 加密后的密码摘要
- user获得一个`authenticate`方法，用来验证键入密码是否正确

`password_digest` 这个栏位需要自己手工建立，所以 `rails g migration add_password_digest_to_users password_digest:string`，记得migrate。

`password_digest`栏位会用来存储加密后的密码摘要，而不会在数据库中存储用户真实的密码，这一点处于安全考虑。这个加密过程需要用到 [bcrypt](https://github.com/codahale/bcrypt-ruby) 这个gem, gemfile文件中原本是有这个gem行的，只是被注释掉了。 安装好，重开。

#### 1.2 建立注册的回路

新建一个 controller :
```ruby
rails g controller users new
```
到routes.rb中删掉自动加上的 new 路径，然后加上 `resources :users`

到navbar 中加上 `<%= link_to "Signup", new_user_path %>`

到users_controller中定义好 views/users/new.html.erb 中会用到的 @user 变量：
```ruby
class UsersController < ApplicationController
   def new
     @user = User.new
   end
```

接着到new表单页面 views/users/new.html.erb 至少包含:
```ruby
<%= form_for @user do |f| %>

    <%= f.email_field :email, class: "form-control" %>

    <%= f.password_field :password, class: "form-control" %>

    <%= f.password_field :password_confirmation, class: "form-control" %>

    <%= f.submit "Submit", class: "btn btn-primary" %>

<% end %>
```

然后到 users_controller 中新建 create 来接这里送出的数据

```ruby
class UsersController < ApplicationController
  def create
  @user = User.new(user_params)
  if @user.save
    redirect_to root_path
    flash[:notice] = "Successfully signed up."
  else
    render :new
  end
end

private
  def user_params
    params.require(:user).permit(:email, :name, :password, :password_confirmation)
  end
```

注册的回路到这里就完成了，可以试着新建一个user然后到 console 中看看 `User.last` 是不是你新建的那个，还有 `:password_digest` 栏位中是否有乱数。

### 2 Login

上面的步骤对于普通用户来说会很诡异，注册完了就只是把我的账号加到了数据库中，没有登录功能前面的工作也没意义。对于基本的登录功能会使用 cookies 中的 session 机制来处理，如果不清楚cookies以及session怎么工作的，可以先google一下，有个整体印象，然后多写几次验证的code会帮助理解。

简单的说 cookies 用户浏览器端中用来存储一些信息的地方，比如 session 是cookies中包含的其中一种, 这些信息有可能是用户本机产生的，也有可能是用户发出request后服务器返回一些信息告诉浏览器让它存在 cookies 中的，这些信息都是临时的，会有各自的过期时间，但是我们可以让某些信息的过期时间很长，几乎永久。

基础版的登陆实际就是使用 `session[:user_login] = user.id` 这样一个方法来生成一个基于 用户ID 的 session 值（也是一串乱数）存在浏览器中，其中像 `:user_login` 这个key的名称是可以自己定的，比如 user_id, user_auth 等都可以。由于这个session的值是基于用户的 ID 生成的，所以除非退出浏览器重新开，或者手动将其删除，他的值是暂时不会变的，所以我们可以反向让rails通过 `:user_login` 的session值得到用户ID 然后到数据库中找到对应用户，这也是定义 `current_user`  method 的基础。可以把 cookies 和 session 都视作一个(nested) hash。

#### 2.1 建立sessions_controller
处理session不需要再新建model，因为session存在浏览器里，用来查找user的id存在users表中，因此只需要新建一个controller来处理这部分：

```ruby
rails g controller sessions new
```

同样的到 routes.rb 中删掉上一步自动添加的路由，然后加上 `resources :sessions`

接着到navbar文件中增加`<%= link_to "Log In", new_session_path %>`

#### 2.2 sessions#new表单作为login页面

views/sessions/new.html.erb 表单会作为登陆页面，建立一个表单至少包含：
```ruby
<%= form_tag sessions_path do |f| %>

    <%= email_field_tag :email, "", class: "form-control" %> # 中间默认值那里需要一个空值占位符

    <%= password_field_tag :password, "", class: "form-control" %>

    <%= submit_tag "Log In", class: "btn btn-primary" %>

<% end %>
```

#### 2.3 到 sessions_controller 中的 create 中接手new表单传过去的数据

```ruby
class SessionsController < ApplicationController
  def new
  end

  def create
    user = User.find_by_email(params[:email])
    if user && user.authenticate(params[:password])
      session[:user_login] = user.id # 生成对应user.id的session值存入browser
      redirect_to root_path
      flash[:notice] = "Successfully logged in."
    else
      render :new
    end
  end
```

到这一步只是生成了 session 存在了浏览器中，但是我们还需要从users数据库中取回具体的user，还需要一个能在每个页面都能在view层面调用这个user的method

因此我们需要到 application_controller 中定义一个 helper_method `current_user`

```ruby
class application_controller < ActionController::Base

  helper_method :current_user

  def current_user
    @current_user = User.find(session[:user_login]) if session[:user_login]
  end

  # 这里session[:user_login]相当于在做反向解密user id的工作
```

这步完之后就可以到 navbar 文件中给 注册，登入，以及current_user的显示加上条件式，具体code就不列了，基本思路是：
```ruby
if !current_user
  注册
  登陆
else
  current_user.email
  登出(session_path(current_user), method: :delete)
end
```

登出功能实际上就是把 `:user_login` 这个session删掉，因此到 sessions_controller 中：

```ruby
SessionsController < ApplicationController

def destroy
  session.delete(:user_login) # 注意这里用的是 圆括号
  redirect_back(fallback_location: root_path)
end
```

这样一个基础版的注册和登陆还有登出功能就有了，不过默认情况退出浏览器后再进会发现需要重新登陆，实际使用情况中除了银行这类安全要求很高的网站，很多日常使用的网站在一段时间内都不需要我们每次都登陆，他会“记住”我们。在下一篇会写如何用 `cookies.permanent` 来实现记住用户的功能。
