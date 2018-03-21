---
title:  "Authenticating form scratch 3 - password resetting"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails]
---

重置密码实际上就是对user的 password 栏位进行更新，常遇到的情景是用户在登陆页面点击“忘记密码链接”，然后会要求输入自己注册时的邮箱，如果邮箱确实存在会发一封重置邮件到邮箱，邮件内容会包含一个重置链接，在一定时限内点击这个链接可以进入到修改密码页面，修改密码成功后这个重置链接应该即刻失效。

这中间涉及到的验证环节是重置链接，用户不会知道自己账号在数据库中的id，而且直接在重置链接中包含用户id也不安全。因此需要单独生成一个key来验证链接的正确性并能用这个key到数据中定位到具体的用户，所以这里会生成一个新的 `reset_token` 栏位来处理这个会在链接中包含的乱数。

由于出现这种情况，有人输入了别人的邮箱或者用户误操作输入了别人的邮箱，这同样会发送重置邮件到别人的邮箱。为了安全起见一是应该在邮件里说明这种情况，二是给重置链接设定时效。因此还需要一个 `reseted_at` 栏位来存储发出链接的时间，后面才好规定时限问题。

这部分回路不涉及到session，不需要新的model,但需要新建一个controller来接管。

#### 1 准备工作

生成一个 password_resets_controller:
```ruby
rails g controller password_resets new edit
 # new页面用户填邮箱，edit页面用来修改密码。
 # create 会用来完成定位邮箱，生成token并发送重置邮件的工作
```

到routes.rb中删掉新生成的两行，加上 `resources :password_resets`

到sessions/new(login)页面加上忘记密码的链接，导向password_resets/new页面：
```ruby
  <%= link_to "Forget Password?", new_password_reset_path %>
```
然后点一下这个链接看是否会进入到password_resets/new页面

建立reset_token和reseted_at栏位
```ruby
rails g migration add_reset_token_to_users reset_token:string reseted_at:datetime
# 这个token栏位同样可以使用之前写的 generate_token(column) function来处理
```
migrate

#### 2 邮箱定位用户, 注入数据到重置相关栏位

先写好password_resets/new 接受用户输入的邮箱信息
```ruby
<%= form_tag password_resets_path do |f| %>
  <div class="form-group">
    <%= label_tag :Email%>
    <%= email_field_tag :email, "", class: "form-control" %>
  </div>
  <div class="form-group">
    <%= submit_tag "Submit", class: "btn btn-primary" %>
  </div>
<% end %>
```
create接手送来的邮箱：

```ruby
class PasswordResetsController < ApplicationController

  def create
    user = User.find_by_email(params[:email])
    if user
      user.send_reset_email # 这个method到user.rb中写
      redirect_to root_url
      flash[:notice] = "Reset email has been sent."
    else
      redirect_to new_password_reset_path
    end

  end
```
写好send_reset_email，发送email前要先生成reset_token和reseted_at栏位信息

```ruby
class User < ApplicationRecord
  def send_reset_email
    self.generate_token(:reset_token)
    self.reseted_at = Time.zone.now
    self.save
    UserMailer.password_reset(self).deliver
    # 这里要把user传到mailer中
    # 因为后面的重置连接中会用到user的reset_token
  end
```

#### 3 送出包含reset_token的链接

先生成user.rb中提到的UserMailer
```ruby
rails g mailer user_mailer password_reset
```

到config/environments/development.rb中设定默认邮件发送域名
```ruby
+ config.action_mailer.default_url_options = { host: "localhost:3000" }
```

重开server生效。到mailer的.rb文件中定义好 password_reset method
app/mailers/user_mailer.rb:
```ruby
  def password_reset(user) # 这里接受前面送来的 'self'
    @user = user
    mail to: @user.email, subject: "password reset"
  end
```

然后到邮件的view页面中添加重置链接，这个链接中包含两个关键点，1 包含reset_token 2 导向 password_resets/edit 页面。app/views/user_mailer/password_reset.html.erb,略去关于重置密码的安全说明，需要包含的是：
```ruby
<%= link_to "Click here to reset your password", edit_password_reset_url(@user.reset_token)%>
```
**注意这里要用url才能送出正确网址**

这样用户点击的时候会生成类似
`http://localhost:3000/password_resets/8XlMJNlU2P50saOM9baxkg/edit`
的链接。中间的就是reset_token待会会用来去数据库抓用户。

一切正常的话点这个链接会进入 password_resets/edit页面，观察网址就是上面那种形式。网址中并不是 reset_token=8XlMJNlU2P50saOM9baxkg 这种形式，所以下一步拿这个token使用的是 params[:id]。

controller中写好edit接手的对象：
```ruby
  class PasswordResetsController < ApplicationController
    def edit
      @user = User.find_by_reset_token(params[:id])
    end
```

**写好edit页面，让用户输入新密码** （只保留了必须内容）
password_resets/edit:
```ruby
  <%= form_for @user, url: password_reset_path, method: :patch do |f| %>
    <%= f.password_field :password%>
    <%= f.password_field :password_confirmation%>
    <%= f.submit "Submit", class: "btn btn-primary" %>
  <% end %>
```

#### 4 进入重置环节

edit页面送出的只有密码信息，我们还需要reset_token所以使用params[:id]从网址中抓，所以在update中我们会先用reset_token验证链接是否合法，然后再着手修改密码，当然在这之前先会检查token的时效。
```ruby
  class PasswordResetsController < ApplicationController

    def update
      @user = User.find_by_reset_token(params[:id])
      if @user && @user.reseted_at > 2.hours.ago
        @user.update!(reset_params) # 接收的数据写到下面
        @user.reset_token = nil # 更新之后即刻失效
        @user.save
        redirect_to root_url
        flash[:notice] = "Password has been reseted, try login again."
      else
        render :edit
      end
    end

    private

      def reset_params
        params.require(:user).permit(:password_confirmation, :password)
      end
```

到这里就实现重置功能了，为了让用户送出错误链接地址时知道自己错在哪里了，可以改一下 edit 动作

```ruby
  class PasswordResetsController < ApplicationController
    def edit
      @user = User.find_by_reset_token(params[:id])
      if @user.reseted_at < 2.hours.ago
        redirect_to root_path
        flassh[:notice] = "Reset link expired."
      elsif !@user
        redirect_to root_path
        flash[:notice] = "Invalid resetting link."
      end
    end
```

---

除了注册用户环节，登陆，记住用户，以及重置密码的基本思路都很类似，需要注意的只是其中几个送出和接受数据的细节，考虑常见的使用情景。这只是基础版本的功能，安全性不高，但拓展功能也是在这个思路上进行改进。
