---
title:  "Ruby on Rails 基础问答二"
date:   2017-04-04 01:04:23
categories: [Programming]
tags: [Ruby & Rails]
---

关于ruby on rails 的基础问题二。

#### 1 如何理解Http, Response , Request , Session, Cookie

**HTTP(HyperText Transfer Protocol)超文本传输协议**

http是用户（客户端终端）和网站（服务器端）之间的请求和应答标准。
通常，由HTTP客户端发起一个请求，创建一个到服务器指定端口（默认是80端口）的TCP连接。HTTP服务器则在那个端口监听客户端的请求。一旦收到请求，服务器会向客户端返回一个状态，比如"HTTP/1.1 200 OK"，以及返回的内容，如请求的文件、错误消息、或者其它信息。
[更多信息参考wiki条目](https://zh.wikipedia.org/wiki/超文本传输协议)

**HTTPS(Hypertext Transfer Protocol Secure)超文本传输安全协议**

https是一种网络安全传输协议，其基本运作原理与http类似，但它另外利用SSL/TLS来加密数据包。主要目的是提供对网络服务的身份认证，保护交换数据的隐私与完整性。
[更多信息参考wiki条目](https://zh.wikipedia.org/wiki/超文本传输安全协议)

二者的区别:
> 与HTTP的url由“http://”开头且默认使用端口80不同，HTTPS的url由"https://"开头且默认使用端口443。
HTTP是不安全的，且攻击者通过监听和中间人攻击等手段，可以获取网站账户和敏感信息等。HTTPS可放置前述攻击，并（在没有使用旧版本的SSL时）被认为是安全的。

**Response/Request**

网页访问过程中，request就是用户向服务器发起请求的动作，response是服务器对用户请求的回应。

**Session**

用以记录用户在网站服务器上的相关信息。当用户在网站上完成身份验证后，session会保存用户的资料，基于此产生一组对应的id，存入cookie后回传给用户端。这个id使用[uuid](https://zh.wikipedia.org/wiki/通用唯一识别码)的机制，具有足够的安全性。

**Cookie**

是网站为了辨别用户身份而储存在本地用户端上的数据（通常经过加密），上面提到的session信息也包裹在cookie中。cookie总是保存在用户端，按储存位置可以分为内存cookie和硬盘cookie。内存cookie由浏览器维护，保存在内存中，浏览器关闭后就消失了。硬盘cookie保存再硬盘中，有一个“保质期”，除非手动清除或者过了保质期，硬盘cookie才会被清除。二者从留存时间上存在差异。[更多信息参考wiki条目](https://zh.wikipedia.org/wiki/Cookie)


#### 2 用过哪些Linux指令

文件管理

| 指令       | 简介          | 范例  |
| :-------------: |:-------------:| :-----:|
|touch         |  如果文件不存在，穿件一个空白文件；如果文件存在，更新文件读取和修改时间   |  `touch views/demo.html.erb` 在views路径下新建demo.html.erb  |
|  cd       | 切换目录    |  `cd`切换到根目录， `cd..`退回到上一级目录  |
|   ls      | 列出当前路径下文件目录    | `ls`列出文件夹（文件）名称，`ls -al` 列出所有文件及其详情  |
|   clear      |  清楚屏幕   | 清楚当前屏幕上的信息   |
|   mkdir      | 建立路径下的文件夹    | `mkdir app/views` 在/app下新建/views文件夹   |
|   rmdir      |   删除空目录  |  `rmdir app/views` 删除/app下的空文件夹/views   |
|    mount 选项 参数     |   将指定加载点挂载到文件系统  | `mount`列出当前挂载的所有内容；`mount /mnt/cdrom` 挂载光驱   |
|    unmount     |   卸除挂载  | `unmount /mnt/cdrom` 卸除光驱的挂载   |
|     pwd    |  显示当前所在路径   |  `pwd`  |
|    cp     |  将一个或多个源文件或者目录复制到指定的目的文件或目录  | `cp demo user/caven/demo-1` 将文件demo复制到/user/caven/目录下并将其更名为demo-1 |
|    mv     |   移动/重命名当前文件(文件夹)  |`mv demo user/caven/` 将demo移动到user/caven/路径下，`mv user/demo.text user/demo.md`将 user/目录下的demo.text更名为demo.md    |
|   rm      |  删除文件或者文件夹   |  `rm app/views`删除app/views文件夹  |
|    find     | 寻找文件或文件夹    | `find .`列出当前目录下所有文件（包括子目录）；`find css`找到当前目录下所有包含css的文件夹和文件； `find *.txt` 找到当前目录下所有txt类型的文件； |


系统管理：

| 指令       | 简介          | 范例  |
| :-------------: |:-------------:| :-----:|
|    w    |   显示目前登入账户的使用现状  | `w` 会显示开了几个终端，运行的目录等   |
|    date     |  显示日期信息   |  `date` 2017年 4月 4日 星期二 14时08分47秒 CST  |
|    login     |   登入  | `login` 显示，login:要求输入账户名 Password: 要求输入密码 |
|    logout     |   登出 | `logout` |

[更多命令可以在此查找](/http://man.linuxde.net)

#### 3 git merge 和 git rebase 的区别

* `git merger`是直接合并分支到目标分支，会保留所有commit历史
* `git rebase`是修改选定分支的基准点，这会使commit历史看起来呈清晰的线性，但在一些情况下需要小心使用。

[详细说明参考1](/https://git-scm.com/book/zh/v1/Git-分支-分支的衍合)

[详细说明参考2](/https://www.ibm.com/developerworks/cn/devops/d-learn-workings-git/#authorN10024)

#### 4 是否理解过git的工作原理

git是一个灵活而强大的版本管理工具，它以快照方式处理内容。每提交一个变更便产生一个快照，并且提供覆盖各种情况的应用变更或者回滚变更的方式。[对git原理讲得很透彻的一篇文章](/https://www.ibm.com/developerworks/cn/devops/d-learn-workings-git/#authorN10024)


#### 5 学过哪些部署

heroku

Capristrano


#### 6 用过哪些数据库加速的方法

* n+1 query
* 加入索引
* 使用select
* 计数快取 Counter Cache
* Batch finding

[详细参考](/https://ihower.tw/rails/performance.html)


#### 7 Module 的 `extend` 和 `include` 怎么用

使用`include` 会使该Class下的实例拥有并可以使用Module中的method

使用`extend` 这个method就只能在Class层面使用，如果在此Class的instance中使用则会返回“NoMethod”错误


#### 8 rvm怎么用

RVM 即 **[Ruby Version Manager](/https://rvm.io)**,也就是Ruby版本管理器。

最简单的使用方式是`ruby -v` 查看ruby版本。

`which ruby` 查看ruby在本地的位置

#### 9 以下几个methods的执行顺序是什么？

```ruby
class UsersController < ApplicationController
before_action :method1
before_action :method2
end
```

```ruby
class VIPsController < UsersController
before_action :method3
before_action :method4
end
```

顺序是1234 UsersController继承自ApplicationController,具有更高优先级。
