---
title:  "Building Ruby on Rails environment on Mac"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails, environment]
---

简单记录下如何在Mac上一步步搭建Ruby on Rails的开发环境，建议随时使用TimeMachine给Mac备份。

先梳理所有步骤的时间线：

> **Xcode ===> Command Line Tools ===> Homebrew ===> Git ===> ImageMagick**

> **===> PostgreSQL ===> RVM ===> Ruby ===> Rails ===> Atom.**

---

### 1 Xcode

[什么是Xcode ?](https://zh.wikipedia.org/wiki/Xcode)

> Xcode是苹果公司向开发人员提供的集成开发环境，用于开发macOS、iOS、WatchOS和tvOS的应用程序。

**安装步骤：**

到 Apple 的 App Store里搜索到 Xcode 然后直接安装。

### 2 Command Line Tools

[什么是Command Line Tools ?](http://railsapps.github.io/xcode-command-line-tools.html)

> Xcode is a large suite of software development tools and libraries from Apple. The Xcode Command Line Tools are part of XCode.
command line tools实际上是Xcode的一部分。

**安装步骤：**

打开终端(terminal)执行 `xcode-select --install`

完成后查看是否安装成功使用 `xcode-select -p`

### 3 Homebrew

[什么是Homebrew ?](https://zh.wikipedia.org/wiki/Homebrew)

> Homebrew是一款自由及开放源代码的软件包管理系统，用以简化Mac OS X系统上的软件安装过程。

**安装步骤：**

[直接到官网首页显眼处就可以看到安装方式](https://brew.sh/index_zh-tw.html)，一般是要求在terminal中执行一串代码。比如：

```ruby
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"   #2017-04-05更新
```

### 4 Git

[什么是Git ?](https://zh.wikipedia.org/wiki/Git)

> 是一个分布式版本控制软件，最初由林纳斯·托瓦兹（Linus Torvalds）创作。

**安装步骤：**

借由上面已经安装的Homebrew可以很简单的在终端执行 `brew install git`

完成后使用 `git --version` 查看版本


### 5 ImageMagick

[什么是ImageMagick ?](https://zh.wikipedia.org/wiki/ImageMagick)

> ImageMagick是一个用于查看、编辑位图文件以及进行图像格式转换的开放源代码软件套装。它可以读取、编辑超过100种图象格式。

**安装步骤：**

终端执行 `brew install imagemagick`

### 6 PostgreSQL

[什么是PostgreSQL ?](https://zh.wikipedia.org/wiki/PostgreSQL)

> PostgreSQL是自由的对象-关系型数据库服务器（数据库管理系统）。

**安装步骤：**

终端执行 `brew install postgresql`

安装完成后执行 `brew services start postgresql ` 设置下次开机时自动启动资料库。

* [官方网址](https://www.postgresql.org)


### 7 RVM

[什么是RVM ?](https://en.wikipedia.org/wiki/Ruby_Version_Manager)

> RVM 是 Ruby Version Manager 的缩写。是一个用来管理Ruby版本的套件。

**[安装步骤](https://rvm.io/rvm/install)：**

终端执行 `\curl -sSL https://get.rvm.io | bash -s stable`

然后执行 `source ~/.rvm/scripts/rvm ` 使其生效

**接着需要安装一个套件叫 libxml2**

[什么是libxml2 ?](https://zh.wikipedia.org/wiki/Libxml2)

> libxml是一个用来解析XML文档的函数库。它用C语言写成，并且能为多种语言所调用。

**安装步骤：**

终端执行 `brew install libxml2`

### 8 Ruby

[什么是Ruby ?](https://zh.wikipedia.org/wiki/Ruby)

> Ruby 是一种面向对象、命令式、函数式、动态的通用编程语言。在20世纪90年代中期由日本人松本行弘（Matz）设计并开发。

**安装步骤：**

终端执行 `rvm install 2.3.1` (可以到[官方网站](https://www.ruby-lang.org/zh_cn/)查看到可使用的版本)

然后 `rvm use 2.3.1 --default` 将2.3.1版本作为预设版本（这个版本号是可以改的）

`rvm list` 可以列出已安装的ruby版本。

### 9 Rails

[什么是Rails ?](https://zh.wikipedia.org/wiki/Ruby_on_Rails)

> Ruby on Rails（官方简称为 Rails。也有人简称为 RoR)是一个使用Ruby语言写的开源Web应用框架，它是严格按照MVC结构开发的。它努力使自身保持简单，来使实际的应用开发时的代码更少，使用最少的配置。

**安装步骤：**

`gem install rails`

* [官方网址](http://rubyonrails.org)
* [官方github地址](https://github.com/rails/rails)

### 10 Atom

[什么是Atom](https://zh.wikipedia.org/wiki/Atom_(文字編輯器))

> Atom是由GitHub开发的自由及开放源代码的文字与代码编辑器，支持OS X、Windows和Linux操作系统，支持Node.js所写的插件，并内置由Github提供的Git版本控制系统。

**安装步骤：**

* 到官网 https://atom.io 下载安装到Mac


* 打开atom并设定可以在terminal中操作atom:

点击atom菜单栏的**Atom**菜单 ===> 点击 **Install Shell Commands**

![](/images/post_images/Snip20170405_1.png)

---

更多可供参考的网站：

- [InstallRails.com](http://installrails.com)

- [GoRails.com](https://gorails.com/setup/osx/10.12-sierra)
