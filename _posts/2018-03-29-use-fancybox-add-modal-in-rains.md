---
title:  "Modal effect with fancybox"
categories: [Front end ℗]
tags: [Front end]
---

### Why need modal effect?

Rails 单纯使用 `image_tag` 的图片只能在页面显示，不能点击放大。这种情况不太符合今天多数用户的使用习惯，各类社交网站以及购物网站中的图片都是能够点击放大显示。

### What is fancybox?

[fancybox](http://fancyapps.com/fancybox/3/)

github: https://github.com/fancyapps/fancybox

> (fancybox is)jQuery lightbox script for displaying images, videos and more. Touch enabled, responsive and fully customizable.

也就是说 fancybox 的功能是建立在 jquery 基础上的。

### How to use?

准备工作：

引入 css 和 js 文档，官方给出的方法：

```html
<script src="//code.jquery.com/jquery-3.2.1.min.js"></script>

<link  href="/path/to/jquery.fancybox.min.css" rel="stylesheet">
<script src="/path/to/jquery.fancybox.min.js"></script>
```

第一个是jquery库，后面两个是 fancybox 的css和js

在rails中可以下载文件放到 assets/ 路径下

- app/assets/javascripts/jquery.fancybox.min.js
- app/assets/stylesheets/jquery.fancybox.min.css

不需要再到 layout 页面中写引入链接。assets pipeline 自动加载到 assets 目录下的文件。

基本结构：

fancybox 实现modal放大图片的方式并不是将现有图片放大，然后浮到页面表层。

**它实际要求你提供同一张图的两个尺寸版本，一个是缩略图版本，一个是放大后的尺寸版本。然后会将两张图包在一个 `<a></a>` 标签中，让图片可以点击。**

```html
<a data-fancybox="gallery" href="big_1.jpg">
    <img src="small_1.jpg">  # 这是缩略图版本，也就是没点击之前看到的小图
</a>
```

#### example:


假设有一个 Post model, picture 栏位存储有图片。

Rails 中通常是

```ruby
<%= image_tag post.picture, size: '100x100' %>
```
size: 选项限制图片在页面上显示的尺寸，当并不是图片的真实尺寸，比如可以将一张 600x400 的图以 100x100的尺寸在页面显示。这么做是为了保持页面排版的一致性与整洁。那么外层大图就可以选择显示用原尺寸的图片，只需要去掉 `size:` 选项即可。

```html
<%= link_to path_to_image(post.picture), data: { fancybox: "gallery", caption: "#{post.content}" } do %>
  <%= image_tag post.picture, size: '150x150' %>
<% end %>
```

注意 `data:` 中的  `fancybox: "gallery"` 不能省。后面的 `caption: "#{post.content}"` 只是为了在modal下方显示 post 的内容。

`path_to_image` 是rails的一个helper 能以string形式显示 图片路径。这样就生成了实现modal所需要的html结构。
