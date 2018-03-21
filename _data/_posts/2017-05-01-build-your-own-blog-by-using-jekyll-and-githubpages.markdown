---
title:  "如何用jekyll和github pages搭建自己的blog"
date:   2017-04-30 15:04:23
categories: [jekyll]
tags: [Writing, jekyll, github-pages]
---

如何写作才不背离写作的初衷？—— 只管写而不用担心太多其他问题，或者被写作以外无关的事物分心。

blog平台的种类很多，但我们不知道它们能存在多久，如果资料丢失了怎么办？我想自定义文章页面样式可以吗？为什么不能很好地支持markdown语法？

**jekyll较好地处理了这些问题。**

### 什么是[jekyll](https://github.com/jekyll)?

- [中文说明](http://jekyllcn.com)
- [英文说明](https://jekyllrb.com)

简单的说就是一个博客的构架，他可以帮你把文字转换为html页面，有css文件夹让你自定义页面样式，可以调整layout等等，感觉上和一个rails的专案有几分相似，但它要简单得多。看不太明白没关系，用起来之后不用解释都会明白的。

### Step 1 到你的Github上建立一个用于Github Pages的仓库

你可以在上[Github Pages的主页](https://pages.github.com)根据提示操作，**要注意第一步里面仓库的命名规定！仓库名称必须是：username.github.io**。

> Head over to GitHub and create a new repository named username.github.io, where username is your username (or organization name) on GitHub.

>>If the first part of the repository doesn’t exactly match your username, it won’t work, so make sure to get it right.

### Step 2 在本地用jekyll建立你的blog文件夹

1.选择好你本地的文件夹路径。

2.在terminal中执行 `gem install jekyll bundler` ，会出现类似画面：

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo ⮀ gem install jekyll bundler
Successfully installed jekyll-3.4.3
Parsing documentation for jekyll-3.4.3
Done installing documentation for jekyll after 1 seconds
Successfully installed bundler-1.14.6
Parsing documentation for bundler-1.14.6
Done installing documentation for bundler after 3 seconds
2 gems installed
```

3.用jekyll生成你的blog文件夹，执行 `jekyll new my-blog`

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo ⮀ jekyll new my-blog
Running bundle install in /Users/caven/demo/my-blog...
 Bundler: The dependency tzinfo-data (>= 0) will be......
 .
 .
 .
New jekyll site installed in /Users/caven/demo/my-blog.
```

4.进入你的blog文件夹， `cd my-blog`

5.执行 `bundle install` 安装gem

6.`git init` 接着 `git add .` 存入本地git进度 `git commit -m "Successfully build my-blog folder"`

**这样就在本地建立好了blog专案文件夹。**

### Step 3 本地预览你的blog页面

在终端执行 `jekyll s` 就可以让你的blog在本地跑起来了，下面会给你地址（一般会是：http://localhost:4000）。点进去就可以看到blog的主页了！默认的原始页面是这样的：

![](/photos/postimages/屏幕快照 2017-05-02 下午1.36.34.png)

### Step 4 将进度push到Git pages的仓库并查看blog网址
先去你刚刚新建的Git pages的仓库取得仓库地址，然后 `git remote add origin xxxxxxx仓库地址xxxxxxx.git` 将远端地址设置好，接着`git push origin master`

完成后到https://username.github.io/ (username就是你的git用户名) 就可以进入你的blog主页。

以后你写了新文章也是这样操作就可以更新你的blog

### Step 5 如何写文章

终端`atom .` 打开文件夹可以看到这样的结构

![](/photos/postimages/Snip20170502_6.png)

**格式要求：**

**_posts/** 目录下用于存放文章，文章格式将是 **markdown**,每篇文章会有一个这样格式的 head :

```ruby
---
layout: post
title:  "Welcome to Jekyll!"
date:   2017-05-01 09:24:46 +0800
categories: jekyll update
---
```

这些内容会自动以一定格式在文章页面或者文章列表页面显示。不一定只是这四项内容，可能还有tags等内容，也可能不会写layout，这取决于你的需求，后面会提到。但格式都是上下三个短破折号包裹。

**文章文件命名方式：**

文章应该以 **2017-05-01-post-title.markdown** 这样的方式命名，才能正常地生成文章网址。

**用markdown语法写文章：**

我们在_posts/路径下写的文章会被自动转译成html文件并放到_site/文件路径下的某个地方，然后再呈现在网页上。

![](/photos/postimages/Snip20170502_9.png)

atom里保存后可以在本地看到主页已经有了新文章的链接。

![](/photos/postimages/Snip20170502_10.png)

点进去看：

![](/photos/postimages/Snip20170502_11.png)


**到这里，你应该大概知道jekyll和Github pages是怎么配合工作来生成blog的了。简而言之就是jekyll以一个构架来帮你转译文章，Github pages为你提供服务器支持。**

### Step 6 配置你的blog

**配置文件是_config.yml**，你可以[按照说明](http://jekyllcn.com/docs/configuration/)在这里修改blog的配置。

---

**到这里已经说完了jekyll的基本用法，不过这个blog的结构只是最基本的。呈现出来的也是很基础的版面，你肯定会想要一些自定义的主题或者加入自己想要的某些效果。如果你很生猛的话，可以找到放css的文件（此例中是my-blog/_site/assets/main.css），然后开始一个个改。还记得前面说的jekyll会帮我们把markdown文档转换成html页面吗？转译后的html样式就是由这里来控制的，如果你想修改某一部分的样式，可以先到转译的html文件中定位到具体的tag或者class,然后到css文件中改。除非你对这部分内容很熟，而且设计功底不错，想做一个独一无二的blog，如果不是的话我认为去套用一个现有的theme然后再在css中进行部分修改是更加可行的办法。**

**我们理解了jekyll的基本运作原理之后，再来套用theme就容易多了。**

---

### 套用主题theme

> 先说说theme如何起作用。既然基本原理还是用jekyll和Github pages配合，那么框架上就会基本一致。所有的主题都是建立起一个jekyll能够运作的文件结构，然后在 css（甚至加入js） 文件中勾勒出页面样式。也许从文件结构上会多出一些文件夹/文件，但只要jekyll能够识别并使用就没有问题。

**使用一个theme就是从网上把他下下来，直接把他作为你blog的文件夹，你仍然也是在/_posts/路径下写文章，任然是push到你建立的github pages的仓库地址。唯一可能多做的就是也许你想去改他的css样式。其他完全一样。**

#### 1. 下载主题

可以找到[一个提供jekyll theme的网站](https://jekyllthemes.io)，有的主题是收费的，有的是免费的。接着下载这个theme,比如：

![](/photos/postimages/Snip20170502_19.png)


下载到本地的可能是个压缩文件，也可能直接是个文件夹，总之打开后会发现里面的文件结构跟之前我们  `jekyll new` 建立起来的文件夹很像，但通常带主题的文件夹里面内容会多一些。

比如上面那个theme下载到本地直接是个文件夹：
![](/photos/postimages/屏幕快照 2017-05-02 下午8.23.33.png)

对比下文件结构：

![](/photos/postimages/Snip20170502_21.png)


#### 2. 使用主题

首先你可以给这个文件夹改个名称，放到你电脑中合适的位置。**写文章仍然是在/_posts/路径下，文件格式命名要求也一样。**

记得`git init`， 然后到你的github上取得blog的git地址，然后加到这个主题文件夹中，写好文章后，push到GitHub就可以了。

#### 3. 调整css样式

首先找到放css的文件,可能不同主题会有所不同。以这个主题为例：

![](/photos/postimages/Snip20170502_22.png)

找到后就可以按照自己的需要进行修改了。

#### 4. 调整整体布局

**可以看到有个layout文件夹，里面会对不同页面的整体排版进行控制，通常layout里面的文件还会使用 include xxx.html 这样的语句来引用 _includes/路径下的html文件，这些可以慢慢探索，关系也不复杂。**

![](/photos/postimages/Snip20170502_24.png)

#### 5. 修改配置

仍然是在 _ config.yml 中修改配置。具体各项配置的作用可以查询[jekyll主页](http://jekyllcn.com/docs/configuration/)或者[GitHub Pages 上对jekyll的补充说明。](https://help.github.com/categories/customizing-github-pages/)

#### 6. push到你的github pages的仓库

在push之前，可以先在 terminal 中跑 `jekyll s` 然后在 http://localhost:4000 预览下页面， 修改完成后再进行push操作。

---

### 使用jekyll主题的其他方式

1.主题作为gem安装（不建议）

> 因为这个方式是在已有的blog构架上新增，可能会出现新theme和原来的构架有冲突的情况，这里只是作为介绍，但不建议使用。

上面是一种比较手动的方式，还有一种将主题作为gem来进行“安装”的方式。

这种方式先要建立起一个基本的jekyll blog构架，也就是一开始说的使用 `jekyll new` 生成的构架，然后找到gemfile, 在里面加入主题的gem 接着安装使用。

举个简单的例子，还是在最先的那个 my-blog 文件夹中，我们想使用这个主题：https://mmistakes.github.io/minimal-mistakes/

[按照作者在这个页面的引导](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)，进行后续操作。

2.直接去github上拉取主题

其实这种方式跟下载的方式本质上相同，也是拿到文件夹，然后自己修改，push到自己的仓库。

---

#### 总结：

只要理解了jekyll怎么工作，套用主题，修改样式和布局就比较容易了。不过这套系统还是只适用于有一点编程基础的朋友使用，假如连markdown是什么，html，css是什么，本地跑服务器这些基础概念都不明白的话，使用起来可能还是比较困难。
