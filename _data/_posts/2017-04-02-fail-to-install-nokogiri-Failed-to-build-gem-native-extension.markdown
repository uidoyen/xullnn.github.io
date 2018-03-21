---
title:  "Bundle install 过程中的nokogiri错误"
date:   2017-04-02 01:04:23
categories: [Programming]
tags: [Ruby on Rails, nokogiri]
---

关于nokigiri的错误此前都是在执行`bundle install`过程中遇到的，一些报错信息有：

```ruby
An error occurred while installing nokogiri (1.7.0.1), and Bundler cannot continue.
Make sure that `gem install nokogiri -v '1.7.0.1'` succeeds before bundling
```
或是

```ruby
fail to install nokogiri  Failed to build gem native extension
```

stackoverflow上的一个回答：
![](/photos/postimages/Snip20170402_15.png)



这类错误可能是由于Mac的系统升级破坏了xCod CLI。解决方法是重新安装xcode，步骤：

`gem uninstall nokogiri`(可选)

`gem update --system`

`xcode-select --install`

`gem install nokogiri`

---

详细的说明可以参考：
* [stackoverflow上的回答](http://stackoverflow.com/questions/33996523/error-installing-nokogiri-failed-to-build-gem-native-extension-libiconv-is-mi)
* [Nokogiri官方还提供了更多可能的后续错误的的处理方式](http://www.nokogiri.org/tutorials/installing_nokogiri.html)
