---
title:  "How to upload multiple files in Rails(carrierwave)"
date:   2017-04-19 01:04:23
categories: [Programming]
tags: [Ruby on Rails, carrierwave]
---

上传附件如图片到专案是道跳不过的题，我将在这篇文章中梳理：

1 Rails中，如何上传多张图片给指定model；

2 如何在view页面呼叫出想要的图片。

---

#### 准备工作：

- 安装 [gem 'mini_magick'](https://github.com/minimagick/minimagick)
(后面使用carrierwave上传附件时会用到这个gem)。

**说明：**

> 假设我现在有一个名为**product**的model，它有title,description等栏位，我的目的是要让每个product都能**上传多张图片**，并能够方便地**管理和调用**。

#### step 1 安装 carrierwave

> carrierwave是用来上传图片的gem套件, [github上对其使用方法有详细的说明](https://github.com/carrierwaveuploader/carrierwave)。

在gemfile中加入：
```ruby
gem 'carrierwave'
```

然后`bundle`，重开 server

#### step 2 生成 uploader

> 注意，这一步要在安装carrierwave之后才能成功执行。

终端执行 `rails g uploader box` (box是上传接口的名称，可以自己修改)

`rails g model photo product_id:integer insert:string`  这一步是在生成一个名为photo的中间model来专门存储图片，然后用一个string类型的栏位"insert"来作为上传文件的储存池。嵌入product_id是为了让每一个photo都能与特定的product对上号，为后面调用附件做准备。

`rake db:migrate`

#### step 3 将uploader的接口挂到目标model的目标column上

- **app/models/photo.rb**

```ruby
mount_uploader :insert, BoxUploader  #将BoxUploader这个上传接口挂到photo的insert栏位上
belongs_to :product
```
这样我们就能通过carrierwave的uploader接口BoxUploader将附件上传到**photo的 insert 栏位**中

#### step 4 建立product和photo的资料关系

- **app/models/product.rb**

```ruby
has_many :photos, dependent: :destroy #这样product就能通过photos拥有很多附件，当一个product对象被删除时，与之关联的photo也会一并被删除
accepts_nested_attributes_for :photos #这一行是为了让rails在product的form页面接受并放行photo栏位中的资料，不然上传的图片会被挡掉。
```

#### step 5 到uploader的rb文件中设置文件上传过程中的处理流程

> uploader.rb文件会告诉rails文件上传时:
- 需要接入的其他gem
- 存储地点是在本地还是云端
- 存储前文件裁切成什么尺寸（尺寸名称会在view页面被用到）
- 更多的用法参考[carrierwave的github地址](https://github.com/carrierwaveuploader/carrierwave)

在这个例子中，我们这样设置。

- **app/uploaders/box_uploader.rb**

```ruby

......
  include CarrierWave::MiniMagick  # 这里需要用到准备工作中安装的MiniMagick

  storage :file #说明文件存储在本地

  def store_dir  # 设置存储路径，以及路径名称的规划，前面省略了app/public/
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # model.class.to_s.underscore 其实是将 被上传文件的model的class名称（这个例子中是Photo）转换成string字串的小写格式，所以到这里是： uploads/photo/
  # mounted_as 是 挂上传接口个那个栏位的名称，这个例子中是 insert ,所以到这里是：uploads/photo/insert/
  # model.id 是 存储图片的这个model的id, 其实一个photo对象只存储了一张图（的多个version）所以如果你给一个product传了3张图，其实会生成3个新的photo对象用来存储这3张图片的裁切后的版本格式。所以到这里: uploads/photo/insert/:id/

  # 最终我们得到的能直接面对图片的路径会是: uploads/photo/insert/:id/一张图的多个version。 实际的情况可能是
  #  uploads/photo/insert/1/
      #  - iamgesample.jpg
      #  - full_imagesample.jpg
      #  - medium_imagesample.jpg
      #  - small_imagesample.jpg
      #  - minimal_imagesample.jpg

  #  uploads/photo/insert/2/
  #  ...


  # 下面的full, medium, small, minimal是自定的图片预处理尺寸，这里设置了4个自定义尺寸，但最后每一张图片会被存储5张在指定文件夹中,有4张分别对应这里的尺寸，另外张就是原图，如果想要在上传过程中就约束原图大小，可以加一行： process resize_to_fill: [1200, 1200,] ，这样原图会被裁成这个尺寸，然后在此基础上再去裁切生成不同的version.
  version :full do
    process resize_to_fill: [800, 800,]
  end

  version :medium do
    process resize_to_fill: [400, 400]
  end

  version :small do
    process resize_to_fill: [100, 100]
  end

  version :minimal do
    process resize_to_fill: [50, 50]
  end

```

#### step 6 增加上传文件的栏位到 /products/new & edit 页面

添加这行代码到需要的位置：

```ruby
<%= simple_form_for @produt do |product| %>
  ......
  <%= product.file_field :box, multiple: true, name: "photos[box][]"%>

<% end %>

```

#### step 7 到products_controller中修改new和create和show和update

```ruby
def new
    @prodcut = Product.new                 
    @photos  = @product.photos.new
end

def create
    @product = Product.new(product_params)  

    if @product.save                        

        if params[:photos] != nil
            params[:photos][:box].each do |i|
                @photos = @product.photos.create(:insert => i)
            end       
        end

        redirect_to admin_products_path     
    else                                    
        render :new                         
    end                                     
end                                         

def show
    @product = Product.find(params[:id])    
    @photos  = @product.photos.all # 这样在单个product的show页面才能调用到这个product的所有图片
end

def update  
  @product = Product.find(params[:id])
  if params[:photos] != nil
         @product.photos.destroy_all

          params[:photos][:box].each do |i|
              @photos = @product.photos.create(:insert => i)
          end

          @product.update(product_params)
          redirect_to admin_products_path

  elsif   @product.update(product_params)
          redirect_to admin_products_path
  else
          render :edit
  end

end
```

---

### 如何在view页面调用文件

到此, controller中的部分就完成了，下面说说怎么在不同的页面调用这些图片。

在调用页面，我们通常会用到不同图片的不同尺寸，之前uploarder.rb已经帮我们处理好了尺寸问题，而使用photo这个中间model的用意是让我们使用索引（index）来更准确地管理和调用我们想要的图片。如果我们不建立中间model，直接给product增加一个photo的column用来存储图片，carrierwave也是可以实现的，但当我们需要更准确地管理这些文件时，这种方式就不那么灵了。

还记得product `has_many :photos` & photo `belongs_to :product` 吗
我们将配合索引(index)来调用, 假设我新建了我的第10个product,并给它上传了10张图片，这会生成10个新的photo对象，那么这个product自己的id是10, 10个photo对象的id可能是101,102,..,110(这取决于你之前所有商品总共上传了多少张图片，每一张占用一个photo_id)，这10个photo都有一个相同的外部键product_id = 10, product(10)会has这10个photo对象，并对他们生成索引分别是0，1，2,..,9 。第一张图对应0，第二张图对应1，第三张图对应2...。

> 明显调用图片的位置有：
- products/:id/  调用单个product图片
- products/      调用所有products的图片
- products/:id/edit/  可能会想要显示单个product已经存在的图片


#### products/:id/ 页面调用

```ruby
<% if @photos.present? %>  

    <% @photos.each_with_index do |photo, index| %>
        <td>
            <% if index == 0 %>       # 定义第一张图片的调用尺寸
            <%= image_tag photo.insert.medium.url %>
            <% elsif index == 1 %>    # 定义第一张图片的调用尺寸
            <%= image_tag photo.insert.small.url %>
            <% else %>                # 定义其余图片的调用尺寸
            <%= image_tag photo.insert.minimal.url%>
            <% end %>
        </td>
    <% end %>

<% else %>

        <td>
            <%= image_tag("http://placehold.it/400x400&text=No Pic", class: "thumbnail") %>
        </td>

<% end %>
```

来看看调用的效果:

![](/photos/postimages/call images-s.jpg)

#### products/ 页面调用

这个页面可能需要分别调用不同product的指定图片，比如我们想要调用每个product的第一张图的最小尺寸

```ruby
<td>
    <%= link_to product_path(p) do %>
        <% if p.photos.present? %>
            <%= image_tag(p.photos[0].insert.minimal.url) %>  # 调用index==0的最小尺寸的图
        <% else %>
            <p style="height: 50px;">NO Photos</p>
        <% end %>
    <% end %>
</td>
```

效果会是这样：

![](/photos/postimages/Snip20170422_1.png)

#### products/:id/edit 页面调用

```ruby
<% if @product.photos.present? %>
      <p>current photos:</p>

      <% @product.photos.each do |photo| %> # 这里调用了此product的所有图片
          <%= image_tag photo.insert.minimal.url %>
      <% end %>

<% else %>

      <p>No photos</p>

<% end %>

<br>
<%= product.file_field :box, multiple: true, name: "photos[box][]"%>

```

调用效果如下

![](/photos/postimages/Snip20170422_3.png)


---

### 图片被存在了哪里？

> 此案例中我们在uploader.rb中把存储地址设为了本地，图片会默认在app/public/uploads下

![](/photos/postimages/dir.jpg)

---

>**总结:**

>carrierwave其实已经把上传文件的流程帮我们处理得相当简单和直观，这里的关键在于建立一个中间model用来专门存储上传文件，另一个目的是能够利用生成的索引来调用图片。

参考资料来源：
- [carrierwave官方github](https://github.com/carrierwaveuploader/carrierwave)
- [stackoverflow上的一个回答](http://stackoverflow.com/questions/21411988/rails-4-multiple-image-or-file-upload-using-carrierwave)
- [rail官方指南的相关主题](http://guides.rubyonrails.org/form_helpers.html) - 5 Uploading Files
- [yy的blog](http://yy4ever.logdown.com/posts/1069287)
