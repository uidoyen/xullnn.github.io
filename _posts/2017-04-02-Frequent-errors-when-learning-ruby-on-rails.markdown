---
title:  "Collection of errors when learning Ruby on Rails"
categories: [Error records ℗]
tags: [Ruby & Rails]
---

这里集中所有在学习ruby on rails过程中遇到的错误，错误的按照报错信息的title归类，介于错误很多致使页面冗长，以后会考虑将不同种类错误拆分成独立的文章。

### 1 SyntaxError

> path/path/path/xxxxxxx.xxx :10: syntax error, unexpected keyword_ensure, expecting keyword_end /path/path/path/xxxxxxx.xxx :12: syntax error, unexpected end-of-input, expecting keyword_end

这种错误一般是由于漏掉了`end` 或者 `<% end %>` 引起的。

![](/images/post_images/5f6DhbETT7yWDHW9HYA2_屏幕快照 2017-01-01 下午10.22.00.png)

---


### 2 ActiveRecord::RecordNotFound in XxxController#method

ActiveRecord指的是database，也就是数据资料没找到。这类错误明确指出错误在controller中，一般是由于foreign key如ID丢失。

比如下图中由于`[params(:id)]`中将符号写反——正确的是`(params[:id])`——导致rails不知道用什么找对象。
> “Couldn't find Job without an ID”

![](/images/post_images/tnhoDTMTni5jzHlhyXFY_屏幕快照 2017-01-07 下午8.50.24.png)

---

> Couldn't find Xxx with 'id'=

![](/images/post_images/hJcmJTFmQTGQ2ymO3aCp_屏幕快照 2017-01-09 下午4.39.41-2.png)

上图中没能够通过id这个键去找到一个Job，这是由于route中的`resource :jobs`——正确的是`resources :jobs`——少了s,没有把jobs作为一组资源对待。

---

> ActiveRecord::RecordNotFound in CommentsController#create
Couldn't find Prodcut without an ID

![](/images/post_images/V7VNFBUZTrug08oCsblE_屏幕快照 2017-02-24 下午3.17.16.png)

没有ID就找不到Prodcut...

当时是做一个关于product的评论功能，新建了一个model叫comment，这个creat的method是在comment的controller中的。因为comment既属于user也属于product，所以在create时需要包裹这两个外部键，问题出在语法上。报错的这一行:
```ruby
@product = Product.find[:id]
```
这个写法不管在哪里都是错误的，至少都应该是`(params[:id])`但是由于这是在comments_controller中，而不是在products_controller里所以在这里要说明这个id是谁的，所以这一行正确的写法是：
```ruby
@product = Product.find(params[:product_id])
@comment.product = @product
```

或者直接写
```ruby
@comment.product = Product.find(params[:prodcut_id])
```

> ActiveRecord::StatementInvalid in CommentsController#create
SQLite3::SQLException: no such column: products.product_id: SELECT "products".*FROM "products" WHERE "products"."product_id" IS NULL LIMIT?*

![](/images/post_images/vP5ZaiUCTImGczaDKWkx_屏幕快照 2017-02-24 下午6.07.10.png)


这个错误出在这一行:
```ruby
@comment.product = Product.find_by(product_id: params[:id])
```

这也是尝试解决上面那个问题时的失败尝试，这里返回的错误信息提到了SQLite3，说明是查询问题，no such column:product_id ，这一句说明rails把product_id 作为了product的一个单独的column，因为在product后加上了冒号。

在这个comments_controller中create的正确写法是：

```ruby
def create

  @comment = Comment.new(comment_params)
  @comment.user = current_user
  @comment.product = Product.find(params[:product_id])

  if @comment.save
    redirect_to :back
  else
    render :newe
  end

end
```

---

### 3 ArgumentError in XxxController#method

> Wrong number of arguments (given 0, expected 1)

自变数(参数)错误，给出的是0，期望的是1

![](/images/post_images/ADqbCT2KRECamKQdioyk_屏幕快照 2017-01-09 下午5.47.29.png)

这个错误是由于有关post或patch的method后面没有跟上需要传的参数——上面的例子中是漏掉了`(job_params)`，rails希望得到一个参数，但是这里没有给出所以是“given 0, expected 1”。在其他情况下也有可能是"given 1, expected 3" 这种情况下就本应该传3组parameters，但是只给出了一组。

---

> conparison of string with 0 failed

string与0的比较失败。

![](/images/post_images/fn8g3i8LTpeh73pGXhpG_屏幕快照 2017-02-08 下午4.43.59.png)

错误出在这一行:
```ruby
<% if @product.quantity.present? && @product.quantity > 0 %>
```
原因是之前建立product的资料表时，qantity这个attribute的style是 string，不是integer，而string和integer是不可以直接比较的。

解决方法有两种，一种是在这一行中把quantity临时转换为integer:
```ruby
<% if @product.quantity.present? && @product.quantity.to_i > 0 %>
```

另一个方式是去资料表把quantity这个attribute的style永久性地改为integer:

`rails g migration change_product_quantiy_to_integer`

然后修改migrate文件
```ruby
class ChangeProductQuantityToInteger < ActiveRecord::Migration[5.0]
  def change
    change_column :products, :quantity, :integer, default: 1
  end
end
```

接着 `rake db:migrate`

---

> showing /path/path/.../views/order_mailer/notify_placed.html.erb where line # 16 raised:
Missing host to link to : Pleaese provide the :host parameter, set default_url_options[:host], or set :only_path to true

![](/images/post_images/qMIMxrWSCGMnAmITjEAD_屏幕快照 2017-03-03 上午11.51.43.png)

这个错误的原因是在config/environments/development.rb中缺少了：
```ruby
config.action_mailer.default_url_options = {host: 'localhost:3000'}
```

加上这一行应该是设定在development环境下默认的发送邮件的地址，[一个stackoverflow上的回答说明了这点](http://stackoverflow.com/questions/7219732/missing-host-to-link-to-please-provide-host-parameter-or-set-default-url-optio)。

---

> wrong number of arguments (given 1, expected 0)

![](/images/post_images/EbAeUzBbSKWLUogmZYg1_屏幕快照 2017-03-26 下午10.07.57.png)

这类参数错误一般都是后面少了（或多了）需要传的参数，这里的错误是helpers/orders_helper.rb中定义helper method时少了参数，但是`<%= render_order_paid_state(@order)%>`在调用这个helper时却裹了参数，所以是given 1 , expected 0。

正确的写法应该在helper中def这行最后加上`(order)`
```ruby
  def render_order_paid_state(order)
    ......
  end
```
---


### 4 NoMethodError in XxxController#method

> undefined method 'update（job_params）' for # <Job:0xx0xxxxx> Did you mean? xxx

![](/images/post_images/jCVyoW74SGSRxpMw7oRR_屏幕快照 2017-01-09 下午6.31.43.png)

这个错误从显示层面可以看出是符号错误，括号写成了中文格式。

---

> undefined method 'any' for #<actionDispatch::Flash::FlashHash:000xxx00x>
Did you mean? any?

![](/images/post_images/JLKMONQmTxK9j5KvZg1m_屏幕快照 2017-01-29 下午10.18.59.png)

这里的错误是`<% if flash.any %>` 这一行中的any少了问号，正确的是`any?`


---

> undefined method 'cart_item' for #<Cart:0x00...>
Did you mean? cart_items , cart_items=

![](/images/post_images/qYTDIqBHSmON4FkzNxvf_屏幕快照 2017-02-07 下午3.58.20.png)

这里给出了明显的提示`cart_items`应该是复数，在包含`each do `这个method的回圈中，对象是多个，不是单独的，一般都是复数。

> undefined method 'user=' for #<Order:00xx0xx...> Did you mean? user_id+

![](/images/post_images/pyaxnhDoRWiYbGWq8jEB_屏幕快照 2017-02-11 下午11.03.18.png)

显然这里rails 把`user =` 视作了`@order`的一个method，但实际这里是想去抓order的user_id这个外部键。这个错误的发生是由于models/order.rb中漏写了`belongs_to :user`,所以两个model没能关联起来。

---

> undefined method 'before_action' for #<Class:00x00x...> Did you mean? before_commit

![](/images/post_images/4h9jPb4USFeFoIu7Wllk_屏幕快照 2017-02-12 下午2.18.47.png)

指出错误在：
```ruby
before_action :generate_token
```
这一行。
这一行代码是写在Model中而不是controller中的，本意是让rails在create新的order前要生成token乱码。
错误是应该写`before_create`而不是`before_action`。这个错误有一点不同的是反馈信息中是"for #<Class:00xx...>"，也就是说在Model中定义的`before_xxxxx`必须是class method，但`before_action`不是一个class method，应该是instance method 所以它都被写在controller里。

---

> undefined method 'first' for nil:NilClass

![](/images/post_images/6jvg1kOeSpGkmtBDoq82_屏幕快照 2017-03-11 下午2.01.30.png)

问题在这一行:

```ruby
@topic.votes.destroy.first
```

first是一个选择动作，所以应该放在votes后面，正确的写法是`@topic.votes.first.destroy`

---

> showing /path/path/.../views/prosts/edit.html.erb where line #5 raised
undefined method 'post_path' for #<#<Class:0xx0x....>:000xx0xx>
Did you mean? font_path, root_path

![](/images/post_images/oJsD3gIvQFCb7mdHETyU_屏幕快照 2017-03-14 下午6.43.49.png)

错误信息中提到的'post_path'在这个erb文件中并没有出现，但rails说是又这里引起的。
之前定义了post是belongs_to :group的，所以其实对post的修改会传两个参数，一个是group_id ， 另一个就是自己的id

post的edit这个method的路径在view中会是`edit_group_post_path(post.group, post)`，可以看到后面也裹了两个参数。

所以这里正确的写法是:
```ruby
<% simple_form for [@group, @post] do |p| %>
```
一个直观的说明是...
![](/images/post_images/p0Bj8oWnR4WuAA0xwDlm_Snip20170314_26.png)

---

> undefined method 'job=' for nil:NilClass

![](/images/post_images/DXhHnGQpQr2ep1Ll6tHh_屏幕快照 2017-03-17 上午10.58.02.png)

这里rails把job=当做了一个method，也许会猜是resume和job在model中的关系没挂上，但不是。原因是`Resume.new`的位置不对，它的位置被放在了最后，但是在这之前就在去定位这个resume的job_id， 不能给还没有的对象裹外部键。所以这里正确的写法是：
```ruby
@resume = Resume.new(resume_params)

@resume.job = Job.find(params[:job_id]) #这是在resumes_controller里，所以要指明id是谁的
@resume.user = current_user
```

---

> undefined method 'product_path' for #<#<Class:0x00xx..>:0xx032..>

![](/images/post_images/TIbuI0x8RB6Xjw7Z4TLF_Snip20170322_13.png)

这里的代码本身是没有错误的，但问题是 produt_path 对应的controller应该是products_controller，但错误信息指明这个路径定位到admin/products_controller中去了。

原因是还没有生成product的controller。聪明的rails会去匹配其他能够匹配到products_controller的文件，于是找到了admin/products_controller.rb中去。

---

> undefined method 'model_name' for nil:NilClass

![](/images/post_images/屏幕快照 2017-04-07 下午11.00.45.png)

报错title指向jobs_controller 中的 new 这个method, 下面的报错快照显示的是 views/new.html.erb 这个文件引起的。错误指出 `model_name` 是空，意思是 new 页面中 `@job` 这里没有get到东西。到controller里发现是在 第一个method的下面多了个 `end` 导致controller在这里提前被结束，后面的内容失效，new页面呼叫对象失败。

```ruby
class JobsController < ApplicationController

  before_action :authenticate_user! except: [:show, :index]

  def index
    @jobs = Job.all
  end

  end  #就是此处多出的end导致错误

  def new
  ...
  ...
  ...

end
```
---

> undefined method 'each' for nil:NilClass

这个错误发生在对多个对象进行排序时controller中引发的错误。

在view页面设置了3种排序触发方式：

```ruby
<ul class="dropdown-menu">
    <li><%= link_to("按薪资下限", jobs_path(order: "by_lower_bound"))%></li>
    <li><%= link_to("按薪资上限", jobs_path(order: "by_upper_bound"))%></li>
    <li><%= link_to("按更新时间", jobs_path(order: "by_updated_time"))%></li>
</ul>
```

然后在对应的controller中定义好每个网址去资料库抓什么资料：

```ruby
def index
  @jobs = case params[:order]

  when "by_lower_bound"
    Job.visible.order("wage_lower_bound desc")

  when "by_upper_bound"
    Job.visible.order("wage_upper_bound desc")

  when "by_updated_time"
    Job.visible.order("updated_at desc")
  end

end
```

![](/images/post_images/屏幕快照 2017-04-18 上午11.05.55.png)

报错显示是没能抓到`@jobs`的资料，最后发现是在用when定义不同触发情况时出现了问题，范例中原来对三种情况的划分用的是`when when else`
而我改成了`when when when`，这导致了一个问题就是没能覆盖的所有情况，原先最后一个用`else`其实涵盖了用时间倒序排列和余下不排序的情况，也就是说直接进/jobs/页面不点任何排序也是会用时间倒序排列的。但是如果三个都用`when`那么就只划定了三种触发方式下会去抓的资料类型，剩下的rails就不知道了，所以抓不到，因为没有定义除了这三种情况以外的情况。

因此，解决方式是把最后一个`when`改成`else`

```ruby
else "by_updated_time"
```

---

> undefined method `clean_up' for #<Cart:0x007fc87d4afce8> Did you mean? clean!

![](/images/post_images/屏幕快照 2017-04-24 下午3.03.01.png)

错误在这个orders_controller中被触发：

```ruby
class OrdersController < ApplicationController

def create
    @order = Order.new(order_params)
    @order.user = current_user
    @order.total_money = current_cart.total_price


    if @order.save
      current_cart.cart_items.each do |cart_item|
          purchase_item = PurchaseItem.new
          purchase_item.order = @order
          purchase_item.product_name = cart_item.product.title
          purchase_item.purchase_price = cart_item.product.price
          purchase_item.purchase_quantity = cart_item.number
          purchase_item.save
      end
          current_cart.clean_up          #错误发生在这一行，本意是想在建立order之后清空当前cart中的所有条目
          OrderMailer.notify_order_placed(@order).deliver!
          redirect_to order_path(@order.token)
    else
          render 'carts/checkout'
    end
  end

```

carts_controller中对clean_up这个动作有定义，但不能使用：
```ruby
class CartsController < ApplicationController

  def clean_up
      current_cart.clean!
      flash[:notice]="已清空购物车"
      redirect_to carts_path
  end

```

其中的 `clean!` 重构到了models/cart.rb中：
```ruby
class Cart < ApplicationRecord

  def clean!
  cart_items.destroy_all
  end

```

看看执行流程 current_cart --> clean_up --> clean!

= current_cart.current_cart.clean!
= current_cart.current_cart.cart_items.destory_all

前面的current_cart其实被要求执行了两次，修正这个错误可以改成： `current_cart.clean!` 直接执行model中的method, 或者： `current_cart.cart_items.destroy_all` 这样稍微直观但会复杂一些。

---

### 5 LoadError in XxxxController#method

> Unable to autoload Order_record, expected /path/path/.../models/Order_record.rb to define it

![](/images/post_images/NnOJMHUHQEeiD7ele5u2_屏幕快照 2017-03-24 下午10.02.59.png)

错误出在这一行(orders_controller)：
```ruby
Order_record = Order_record.new
```
controller里的加载错误，无法自动加载 常量 "Order_record" ，期望 order_record.rb 对其进行定义，显然是 `order_record`这个东西没有被认可。
`new`是一个class method，前面跟的应该是一个model名称，但model名称只会在文件名中带有连字号，代码中的model名称是首字母大写且没有间隔的，所以这里正确的写法是
```ruby
order_record = OrderRecord.new
```




---


### 6 ActionController::UrlGenerationError in Xxx::Xxx#method

> no route matches {:action=>"show", :controller =>"admin/jobs"} missing required keys: [:id]

![](/images/post_images/3tz8wozkTHWBwBN1ThOH_屏幕快照 2017-03-15 下午8.52.00.png)

缺少了 id 这个 key， 正确的写法是：

```ruby
<td><%= link_to(job.title, admin_job_path(job))%></td>
```

---

> no route matches{:action=> "show", :controller=>"movies"}missing required keys: [:id]

![](/images/post_images/屏幕快照 2017-04-10 下午9.46.20.png)

问题出在 movies_controller.rb中的 create, redirect那一行缺少了key,rails不知道要呼叫哪个movie

```ruby
def create
  @movie = Movie.new(movie_params)
  @movie.user = current_user

  if @movie.save
    redirect_to movie_path #这一行缺少了 :id 这个 key
  else
    render :new
  end
end
```

所以正确的写法是在 `redirect_to movie_path` 后面加上 `(@movie)` 也就是前面新建的movie

---

> no route matches {:action=> ”edit”, :controller=>"comments", :id=>nil, :movie_id=>"5"} missing required keys: [:id]

![](/images/post_images/屏幕快照 2017-04-10 下午11.15.15.png)

comment 是 belongs_to movie的，这里返回的信息说缺少了 [:id]，那就是controller中对应的method里没有抓到comment的key,所以到comments_controller对应的method中的 `@comment = Comment.find(params[:id])`这一行的某个地方出错

---

### ActionController::UnknownFormat in Xxx::XxxxsController#method

> Account::MoivesController#index is missing a template for this request format and variant. request.formats: ["text/html"] request.variant: [] NOTE! For XHR/Ajax or API requests, this action would normally respond with 204 No Content: an empty white screen. Since you're loading it in a web browser, we assume that you expected to actually render a template, not nothing, so we're showing an error to be extra-clear. If you expect 204 No Content, carry on. That's what you'll get from an XHR or API request. Give it a shot.

![](/images/post_images/Snip20170412_20.png)


---

### 7 NameError in Xxxx#method

> Shwoing /path/views/carts/index.html.erb where line 47 raised:
undefined local variable or method 'cart' for #<Cart:0x00...>

![](/images/post_images/7X4zyChCTC27MXNmAtj3_屏幕快照 2017-02-14 下午3.40.19.png)

这个错误在models/cart.rb中，由这一行引起：
```ruby
cart.cart_items.each do |cart_item|
```
错误在于这本身已经在cart的model文件里，默认动作都是针对cart展开，所以之前的`cart.`引起了rails的困惑。

正确的写法是：

```ruby
cart_items.each do |cart_item|
```


---
### 8 Routing Error

> undefined method 'current_cart' for class '#<Class:ApplicationController>'

![](/images/post_images/PtqkNBBgTbqMJ8zUFMh8_屏幕快照 2017-02-14 下午3.01.28.png)

这个错误虽然title指向route，但实际错误不在routes.rb中

之前因为需要在view页面中调用`current_cart`，这是一个属于controller的method，不同于 product.title 这类可以在view中调用的键值，所以要先在application_controller.rb中加入一行
```ruby
class ApplicationController < ActionController::Base
...
  helper_method :current_cart
...
end
```
声明它是helper_method才能在view中使用它。

这里的错误是在application_controller中helper和method之间少了连字号：
```ruby
helper method :current_cart   #错误的写法
```

---

### 9 Template error

> Template is missing
missing template order_mailer/apply_cancel with "mailer", Searched in : * "order_mailer"

这类错误一般是method对应的表单文件(erb文件)不匹配，有可能是没有对应名称的erb，或文件放的路径不对，或文件名有错字，或生成文件的时候缺少拓展名或者拓展名有误。

![](/images/post_images/GdDzZtJHSNOd4HSXNWbN_屏幕快照 2017-02-16 下午11.06.45.png)

上面的例子中的app/controllers/order_controller.rb中有个apply_to_cancel的method，里面还有一个在OrderMailerl中定义的method`apply_cancel`。

app/controllers/orders_controller.rb
```ruby
...
  def apply_to_cancel
    @order = Order.find(params[:id])
    OrderMailer.apply_cancel(@order).deliver!
    flash[:notice] = "已提交申请"
    redirect_to :back
  end
...
```

app/mailers/order_mailer.rb
```ruby
...
  def apply_cancel(order)
    @order       = order
    @user        = order.user
    @product_lists = @order.product_lists

    mail(to: "admin@test.com" , subject: "[JDStore] 用户#{order.user.email}申请取消订单 #{order.token}")
  end
...
```

在OrderMailer中的 apply_cancel(order) 这个method需要一个邮件模板，对应的erb应该是 app/views/order_mailer/apply_cancel.html.erb

![](/images/post_images/Snip20170402_16.png)

这里的错误在于生成erb文件时写的是`touch app/views/order_mailer/apply_to_cancel.html.erb` 而实际上需要模板的是`apply_cancel`这个mailer中的method，名称也应该对应。

---

### 10 本地server运行app失败

**本地server运行中未先`control C`终止程序就退出终端，导致再次`rails s`时报错**

这类错误的信息一般是:

```ruby
path/path/path/xxx.xx rb:268 in 'innitialize': Address already in use - bind(2) for "::1" port 3000 (Errno::EADDRINUSE)
```

处理方式是：

` kill -9 $(lsof -i tcp:3000 -t)`

然后重新跑server

---

### 11 Errors in Console

> 在console中执行`Cart.creat`时

```ruby
ArgumentError: Unknown key: :sources. Valid keys are: :class_name, :anonymous_class, :foreign_key, :validate, :autosave, :table_name, :before_add, :after_add, :before_remove, :after_remove, :extend, :primary_key, :dependent, :as, :through, :source, :source_type, :inverse_of, :counter_cache, :join_table, :foreign_type, :index_errors
```

参数错误，未知键 :sources 有效的key有：:class_name, :anonymous_class, :foreign_key, :validate...

这里的错误在models/cart.rb中

```ruby
has_many :products, through: :cart_items, sources: :product
```

这一行中的`sources`应该是单数`source`。同时也告诉了有哪些有效的键值。

---

### 12 Others

![](/images/post_images/t6WWuQyQT2sUTSGhFc9Q_Snip20170317_41.png)

admin/jobs/:id/resumes 这个页面最下方会出现所有resume的详细信息，显示方式和console中的显示方式一致。

错误出在这一行（app/views/admin/resumes/index.html.erb）:
```ruby
<%= @job.resumes.each do |r| %>
```
 这一行中多了等号，让所有resume的详细信息在`<% end %>`那个位置显示出来。

 * [stackoverflow上有一个关于这类错误的解释](http://stackoverflow.com/questions/6946206/ruby-on-rails-3-each-do-problem)


 ---

**由于多了一个空行导致的报错s。**

 导致错误的写法是：

 ![](/images/post_images/5nT6kCQGQMGSYc1OZRkQ_Snip20170326_62.png)

 报错信息一大片：

 ![](/images/post_images/XzDNb8W1SCCVeoUp9QCf_Snip20170326_63.png)

 这个错误就是由于多了一个空行引起的...去掉空行后恢复正常。

 app/views/admin/orders/_state_option.html.erb
 ```ruby
 <div style="padding:10px; float:right;">

   <% case order.aasm_state %>
   <% when "order_placed" %>
     <%= link_to("取消订单",
                 cancel_admin_order_path(order),
                 class: "btn btn-default btn-sm", method: :post) %>

   <% when "paid" %>
   ......

 </div>
 ```

 ---

 **yml文件中的嵌入式ruby `<%= %>` 句法错误**

错误出现在测试过程中

测试结果

`39 tests, 0 assertions, 0 failures, 39 errors, 0 skips`

全是error, 应该是代码功能上的错误。

错误信息是句法上的

```ruby
SyntaxError: /Users/caven/Programming/learnruby/sample_app/test/fixtures/users.yml:16: syntax error, unexpected ':', expecting ')'
malory:
      ^
/Users/caven/Programming/learnruby/sample_app/test/fixtures/users.yml:18: syntax error, unexpected ':', expecting ')'
  email: boss@example.gov
       ^
/Users/caven/Programming/learnruby/sample_app/test/fixtures/users.yml:19: syntax error, unexpected ':', expecting ')'
  password_digest: <%= User.digest("password"))...
                 ^
/Users/caven/Programming/learnruby/sample_app/test/fixtures/users.yml:27: unterminated string meets end of file
); _erbout
          ^
/Users/caven/Programming/learnruby/sample_app/test/fixtures/users.yml:27: syntax error, unexpected end-of-input, expecting ')'
); _erbout
```

检查了缩进没有问题。

google过也没定位到。

最后发现是 `<%= >` 少了一个 `%`， `_erbout` 是一个提示线索， SyntaxError 也是一个线索。

---

**view中设置的 confirm 信息连续出现两次**

'delete' 按键设置了js的 confirm data

点击后出现一次，确认删除后再次出现

https://stackoverflow.com/questions/4475449/link-to-confirm-displays-popup-twice

原因是 application.js

中重复引入了2次 ujs 相关的 library

```ruby
...
//= require jquery-ujs
//= require rails-ujs
//= require_tree .
```
