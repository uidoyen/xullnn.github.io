---
title:  "Production环境下carrierwave + Heroku + Amazon S3 + figaro 存储文件"
date:   2017-04-15 01:04:23
categories: [Programming]
tags: [Heroku, Ruby on Rails, Deploy, aws]
---

前面的文章中谈到在development环境下[如何使用carrierwave实现附件的上传和管理](https://gitcavendish.github.io/2017/How-to-upload-multi-files-in-rails/)，在开发环境中我们将文件存储路径设置为 **app/public/uploads/** 下，这个目录下的文件可以不经过rails的处理直接被调用。那么在production产品环境中上传的文件放哪里呢？

同样使用carrierwave我们看下部署到heroku上如何使用Amazon Simple Storage Service (Amazon S3)服务来将上传的文件存在专门的存储服务器。
> 实际上也可以将上传的文件就留在public文件夹中，但记得不要在 .gitignore 文件中加上 public/uploads。这样做也是凑效的，不过你的app体积会随着文件上传量的增加而快速增加。heroku的免费账户对单个app大小的限制是300MB。



### 1. [在本地安装好carrierwave](https://gitcavendish.github.io/2017/How-to-upload-multi-files-in-rails/)并测试各项功能是否正常。

### 2. Amazon S3上存储桶的建立

 - 到[Amazon S3](https://aws.amazon.com/cn/s3/)上注册账户(会需要用到带master或visa的信用卡)。
    -  根据AWS安全审查指南，创建 IAM 用户
    -  到“选择AWS访问类型”这一步时，选择 **编程访问** 的访问类型。
    -  到达“权限设置”这一步时，点击 **直接附加现有策略** --》 搜索 “**s3**”  --》勾选 **AmazonS3FullAccess**

 - 用户创建好之后会显示 **access key id** 和 **secret access key** 两个密钥并提示让你下载存储密钥的 .csv文件。将这个文件保存在安全的地方，以后可能会用到。

 - 找到存储服务中的**S3**，点击进入并创建一个新的bucket,包括名称与区域。

这样就在s3上建立好的存储文件的仓库了。

### 3. 安装figaro来管理s3（以及其他服务）的密钥
>类似s3这类服务都有密钥设置来保证数据安全和规定用户权限，当某些功能（比如carrierwave）要使用s3服务来存储上传的文件时，就需要在config/initializers/carrierwave.rb中写入s3的密钥来通过验证，不然就无法使用S3来存储上传的图片。
[figaro](https://github.com/laserlemon/figaro) 的作用是安全地将app中各项服务的密钥同步到部署环境的服务器中以便各项服务能够正常使用，而又不需要将储存密钥的文件直接push到github或者heroku引起重大安全问题。

在gemifile中：

```ruby
gem 'figaro'
```

接着 `bundle`

记得 `figaro install`
> 这一步会生成一个文件 **config/application.yml** 并将其自动添加到 .gitignore 档中这样这个文件就只会留在本地，以后各个服务商提供的各种服务比如邮件发送，云储存的密钥都会存在这个文件中，然后通过规定的语法在对应的.rb设置文件中安全的调用密钥。而不是将密钥写在不同的.rb文件中最后push的满地都是。

![](/images/post_images/Snip20170426_13.png)

执行`cp config/application.yml config/application.yml.example`

这一步是为了复制一份config/application.yml并将其重命名为config/application.yml.example作为协作时给同事看的样板（注意不要把密钥写到这里面，这个文件中只提供如何写密钥的格式样本并列出使用了哪些服务的哪些密钥。）yml文件一定要注意不同内容的缩进关系，因为与rb文件不同，在yml中，缩进代表了层级关系，也是一个语法。在config/application.yml中按照下面的格式写好刚刚申请的s3的密钥和储存桶的信息。

**config/application.yml**

```ruby
 production:
   AWS_ACCESS_KEY_ID: "xxxx"  # 你的 Access key ID

   AWS_SECRET_ACCESS_KEY: "xxxx"  # 你的 Secret access key

   AWS_REGION: "xxxx"  # 你的 S3 bucket 的 Region 位置

   AWS_BUCKET_NAME: "xxxx"  # 你设定的 bucket name

 development:
   AWS_ACCESS_KEY_ID: "xxxx"  # 你的 Access key ID

   AWS_SECRET_ACCESS_KEY: "xxxx"  # 你的 Secret access key

   AWS_REGION: "xxxx"  # 你的 S3 bucket 的 Region 位置

   AWS_BUCKET_NAME: "xxxx"  # 你设定的 bucket name
```

**这里的aws_region指的是：**

![](/images/post_images/Snip20170426_15.png)

可到这里查看[s3的region列表](http://docs.aws.amazon.com/zh_cn/general/latest/gr/rande.html#s3_region)

### 4. 安装 gem fog 让carrierwave能把文件传往远端存储桶

fog是一个功能强大的gem,支持很多种存储服务的转接，最近为了使gem更小巧，针对不同服务进行了拆分，比如: fog-aws, fog-aws, fog-google 等，具体说明可以看[fog的官方git](https://github.com/fog/fog)
> Currently all fog providers are getting separated into metagems to lower the load time and dependency count.
If there's a metagem available for your cloud provider, e.g. fog-aws, you should be using it instead of requiring the full fog collection to avoid unnecessary dependencies.

[carrierwave的readme中也专门针对fog](https://github.com/carrierwaveuploader/carrierwave)的使用设置进行了说明,我们可以使用fog也可以使用fog-aws。

在gemfile中：

`gem 'fog-aws'`

在之前多图上传生成的box_uploader.rb文件中修改储存设置，仅本地存储文件时只用写 `storage :file`,而现在如果只顾production环境可以直接改成`storage :fog`， 但为了兼顾各种环境下的不同情况，我们可以用if语句来设定不同环境下使用什么存储方式：

**app/uploaders/box_uploader.rb**

```ruby
if Rails.env.development?  #这样我们就分别设置好了在开发环境和产品环境各自用什么存储方式。
  storage :file
elsif Rails.env.production?
  storage :fog
end
```

然后 `touch config/initializers/carrierwave.rb` 生成carrierwave的配置文件，并且写好设置：

**config/initializers/carrierwave.rb**

```ruby
CarrierWave.configure do |config|
  if Rails.env.production?                  #这里同样也用条件语句写明了不同环境用什么设置。
    config.fog_provider = 'fog'                  
    config.fog_credentials = {
      provider:              'AWS',                        #这里写明了存储服务的提供商，下面就是各种aws的key
      aws_access_key_id:     ENV["AWS_ACCESS_KEY_ID"],       # 这样写rails就会自动去figaro之前生成的application.yml中去抓对应名称的key和信息
                                                             # 如此这些rb文件被push上去就不会泄露信息
      aws_secret_access_key: ENV["AWS_SECRET_ACCESS_KEY"],   

      region:                ENV["AWS_REGION"]            # 这个区域如果不清楚就去Amazon上查下建立的储存桶的信息

    }
    config.fog_directory  = ENV["AWS_BUCKET_NAME"]    # 这里写明储存桶的名称


  else                        #这里写明非产品环境就存储在本地。
    config.storage :file
  end
end
```

然后记得`bundle`, 重开。


### 5. 修改gemfile准备push到heroku
  - 把 sqlite3 从第7行搬到约第30到40行的group :development, :test do
  - 在末尾新增一个 production group，加上 pg 这个 gem

    ```ruby
      group :production do
        gem 'pg'
      end
    ```

  - 执行 `bundle`
  - `git add .` , `git commit -m "xxx"`

  最后`heroku create`，**但在push到heroku之前有一步！！！**

### 6. 将config/application.yml中设定好的机密信息通过figaro同步到heroku

执行 `figaro heroku:set -e production` ，将production环境下的密钥信息同步到heroku上，如果想查看目前heroku上设置的所有密钥信息可以执行`heroku config`

![](/images/post_images/屏幕快照 2017-04-26 下午8.02.33.jpg)


如果以后新增了什么服务，往config/application.yml中添加了新的密钥，记得要再次执行 `figaro heroku:set -e production` 将里面的内容同步到heroku，不然新增的服务会因无法通过验证而无法使用。

### 7. push heroku
记得push完需要`heroku run rake db:migrate`

---

部署成功后可以尝试新建几个product，上传几张图片，首先是看图片在heroku能不能显示，如果能的话那就几乎可以说明操作是成功的。

最后我们来回答开头那个问题，public文件夹里的内容使用aws后是什么样的？

到amazon上找到新建的存储桶，一层一层地点，会发现里面的文件结构和本地是一模一样的，仍然是: uploads ---> photo ---> insert ---> 12345678..... ---> 同一张图的不同尺寸 .

![](/images/post_images/Snip20170426_16.png)

其实可以把你的bucket就看做 public这个文件夹，但存储桶中的情况和我们所见到也许并不一样，[阿里云的一篇说明文](https://help.aliyun.com/document_detail/31827.html?spm=5176.doc31834.6.565.nYvOTf)档很好地解释了关于对象存储的一些基本概念。
