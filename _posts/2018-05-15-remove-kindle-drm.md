---
title:  "Remove drm from kindle"
categories: [Tools]
tags: [ebook, Mac, Os]
---

记录如何使用 calibre 插件移除从kindle购买的电子图书的加密设置，以便使用 calibre 转换为 epub 等其他格式。

原因：
- 不适应kindle的阅读器界面和操作
- 习惯了系统自带的ibook，自动在不同设备之间同步，增加其他的阅读器只会增加管理书籍的难度

---

#### 1 下载 calibre 安装

https://calibre-ebook.com/

#### 2 将kindle for mac 降级

这一步很有必要，因为amazon经常会更新 kindle 阅读器，带上一些新的图书格式，有时插件进度跟不上就会导致解密失败。

目前是建议降级到 1.17

http://www.mediafire.com/file/8facwgnzbgar55z/KindleForMac-44173.dmg

然后在阅读器设置里关掉自动升级，每次打开app会有提醒让你升级，不要点。

#### 3 给calibre安装解码插件

https://www.epubor.com/3-ways-to-remove-drm-from-kindle-books.html#M1

按照介绍安装。

插件作者的blog, 有很多详细的文字介绍，以及相关主题的其他内容。

https://apprenticealf.wordpress.com/

#### 4 从操作系统中找到图书存储位置

在mac中通常是，/Users/user_name/Library/Application Support/Kindle/My Kindle Content

将图书拖到 calibre 中，处理过程中就自动解密了，之后就可以使用 calibre 的格式转换进行操作。

#### 5 总结

要点是：

- 保持旧版的kindle 阅读器版本。
- 关注插件的更新情况，通常新版会更好用或支持新的文件格式。
- 支持正版，与其google上到处搜罗免费电子书，不如认真选书，买好书，支持作者，把更多精力用在阅读而不是淘免费书上面。
