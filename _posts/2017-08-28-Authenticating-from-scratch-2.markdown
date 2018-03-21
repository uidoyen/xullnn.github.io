---
title:  "Authenticating form scratch 2 - remember_me"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails]
---

remember me 功能的实现在回路上和login极类似，只在其中两个环节有所不同 1 login使用的是session，是临时性的，而 remember me 用到的是 cookies ，可以将指定信息长时间存储在cookies中即使关机也不会影响；2 session使用的是 user.id 作为加密对象，remember me 这里会重新用一个栏位专用的 remember_token 来生成 cookies 中对应的值。

#### 1 准备工作

在sessions/new(login)页面加上 remember me 链接

```ruby
<%= label_tag "Remember me." %>
<%= check_box_tag :remember_me, 1, class: "form-control" %>
```

登陆时如果勾选，会多送出一组数据 `:rememeber_me => "1"`

增加一个 remember_token 栏位来储存生成 cookies 要用的摘要，记得migrate
```ruby
rails g migration add_remember_token_to_users remember_token:string
```

**写一个function来生成相应栏位token**
```ruby
class User < ApplicationRecord
  def generate_token(column)  # 使用时会传一个 :symbol 值，这样就会抓到指定栏位
    self[column] = SecureRandom.urlsafe_base64
    self.save
  end
```

#### 2 到controller中接手 view 传过去的 "1"

```ruby
class SessionsController < ApplicationController

  def create
    user = User.find_by_email(params[:email])
    if user && user.authenticate(params[:password])
      session[:user_login] = user.id
 +    if params[:rememeber_me] == "1"
 +      user.generate_token(:remember_token)
 +      cookies.permanent[:rememeber_user] = user.remember_token
 +    end
      redirect_to root_path
      flash[:notice] = "Successfully logged in."
    else
      render :new
    end

  end
```

这样就能在用户勾选rememeber_me之后生成一个remember_token并基于此生成并固定一个cookies `:remember_user` 到浏览器中，后面会用到他们到数据库中抓user

#### 3 重新定义 current_user

现在登入有两种情况了，一是不勾选rememeber_me，这会使用基于session的登陆，二是勾选，这会用到cookies的部分

```ruby
class ApplicationController < ActionController::Base

  def current_user
    if session[:user_login]
      @current_user = User.find(session[:user_login])
    elsif cookies[:rememeber_user]
      @current_user = User.find_by_remember_token(cookies[:rememeber_user])
    end
  end

```


#### 4 logout 动作中添加处理 cookies 的部分

```ruby
class SessionsController < ApplicationController
  def destroy
    session.delete(:user_login)
    cookies.delete(:remember_user)
    redirect_back(fallback_location: root_path)
  end
````

#### 5 测试

到登陆页面尝试勾选和不勾选两种情况。

- 勾选情况下到chorome dev tool 的 application 面板侧边栏的 cookies 中查看有没有 remember_user 的摘要值。并尝试退出浏览器重开，看看登陆状态是否连续。点击logout 看cookies 中的remember_user是否被清除。
- 不勾选情况下要确认退出浏览器后重新开会丢失登陆状态，并且cookies中没有remember_user的摘要值。

下一篇会写如何实现密码重置功能，基本思路仍然与前面内容有重叠，同样是使用一个栏位的token来验证密码重置申请，只不过会新增一个 password_resets_controller ，要考虑重置申请的有效期限问题，流程上会稍复杂。
