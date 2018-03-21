---
title:  "Command Line Basics"
categories: [Command Line ℗]
tags: [Command Line]
---

**Core conceptions**

- command line interface 命令行接口，即指用文字命令的方式操作计算机（本地 或 远端）。
- 与 CLI 对应的另一种操作计算机的方式是图形用户界面即 Graphical User Interface(GUI)。
- 不同操作系统的 CLI 指令会有所不同。Mac 和 Linux 都是 [Unix-like](https://zh.wikipedia.org/wiki/%E7%B1%BBUnix%E7%B3%BB%E7%BB%9F) 操作系统，所以类似。

---

**Basic behaviors**

Humans use `command line interface` to interact with computers in `terminal` through different `shell`s。


---

**Discrimination of terms**

- Terminal, Console, Shell

Terminal 指 CLI 输入与输出的界面**程序**

[Shell](https://en.wikipedia.org/wiki/Shell_(computing)) 理解为不同的与计算机交互的指令集，有不同的种类如bash zsh， Unix 上常见的是 Bash Shell ， Mac 也默认用 Bash。 Shell 也可以当做是 Shell command 作为一种编程语言使用。

Console 指特定的指令语言环境，如 mysql console, irb console, rails console...

---

**Tips & Shortcuts**

- Terminal 视窗技巧
  - `Control+c` 取消目前指令
  - `Control+z` 中断目前指令
  - `Control+a` 跳到指令最前面
  - `Control+e` 跳到指令最后面
  - `Control+l` 清除画面 (或用 `clear` 指令)


- 常见 CLI 命令
  -  `date` 显示当前时间
  - `uname -a` 显示系统版本信息
  - `uptime` 显示计算机开机运行时长
  - `history` 显示最近执行过的命令清单
  - `history n` 显示最近执行过的 n 条指令，比如history 8
  - `mkdir dirname` make a new directory named 'dirname'
  - `touch filename` 建立名为filename的新文件
  - `cp source target` 复制'source' 到 'target'
  - `mv source target` 将 'source' move 到 'target' （可以同时更名）
  - `pwd` 查看当前所在路径
  - `nano abc.txt` 打开 [nano](https://en.wikipedia.org/wiki/GNU_nano#Control_keys) editor
  - `cat filename` 在terminal 中印出 filename 中的内容
  - `diff file1 file2` 对比两个文件的不同
  - `which something` 查看 something 在系统中的什么位置，比如`which curl`, `which ruby`

---

#### 一些需要展开的command line用法

---

##### 1. cd 相关命令

- `cd` 是 change directory 的缩写
- `.` 代表 current directory
- `..` 代表上一级directory
- `cd directory` 基于当前路径中包含的文件夹切换路径
- `cd /directory` 基于根目录中包含的文件夹切换路径


##### 2. rm 相关命令

- `rm` 是 remove 的缩写
- `rm file` 移除 file 文件
- `rm <directory>`  Remove (empty) dir 移除（空）文件夹
- `rm -rf <directory>` Remove dir & contents 移除文件夹以及里面的所有内容

---

##### 3. ls 相关命令

- `ls` 列出当前路径下的所有文件夹/文件
- `ls -l` 在 ls 基础上列出每个文件夹/文件的 权限，拥有者等详细信息
- `ls -a`	List all (including hidden)
- `ls Jan*` 列出所有以 Jan 开头的文件夹/文件，类似rexp的匹配用法
- `ls -abc` 相当于 `ls -a -b -c` 3个options的效果
- `ls -rtl` list long by reverse modification time

列出文件夹的树状结构 `tree .`

```
 ~/coursera ⮀ tree .
.
└── Introduction\ to\ Mathematical\ Thinking
   ├── assignments
   │   └── _b3b6ca270030af26de6920fedd0c2eb9_Assignment-1.pdf
   └── reading\ material
       ├── 1-background\ reading.pdf
       └── 1-background_reading.md

3 directories, 3 files
```

---

##### 4. curl

From wikipedia:

> [cURL](https://en.wikipedia.org/wiki/CURL) (/kɝl/ or /kə:l/) is a computer software project providing **a library and command-line tool for transferring data using various protocols**. The cURL project produces two products, libcurl and cURL. It was first released in 1997. The name originally stood for "see URL". The original author and lead developer is the Swedish developer Daniel Stenberg.

[Official website](https://curl.haxx.se/)

- curl 是命令行中用来从网络中获取信息的工具(指令)。

Examples:

- 使用 `which curl` 查看curl在系统中的位置，如果没有，需要安装后使用
- `curl www.google.com` 获取 www.google.com 页面的源码信息
- `curl -I www.google.com` 获取 www.google.com 页面的 HTTP header 信息

```ruby
⮀ curl -I www.google.com
HTTP/1.1 200 OK
Date: Mon, 12 Mar 2018 07:49:12 GMT
Expires: -1
Cache-Control: private, max-age=0
Content-Type: text/html; charset=ISO-8859-1
P3P: CP="This is not a P3P policy! See g.co/p3phelp for more info."
Server: gws
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
Set-Cookie: 1P_JAR=2018-03-12-07; expires=Wed, 11-Apr-2018 07:49:12 GMT; path=/; domain=.google.com
Set-Cookie: NID=125=MEEoaa41fWbXKIvt0T-jEuAGPYO7znJLZm2riHMKcRKQyGf99rYFoBXQptSGnMyoZzmWKDMdm2S5DfFIh2E0KzmU41WvAm5w2DV-ix5hxIaqyJksScHIuGxmX0r45P_O; expires=Tue, 11-Sep-2018 07:49:12 GMT; path=/; domain=.google.com; HttpOnly
Transfer-Encoding: chunked
Accept-Ranges: none
Vary: Accept-Encoding
```

---

##### 5. head 与 tail 加上 wc

- `head filename` 显示 filename 的前10行
- `tail filename` 与 head 相对
- `wc` 是 wordcount 的缩写，用来度量给出对象总共有多少行(lines)，多少单词(words)， 多少字节(bytes)。

Examples:

```ruby
⮀ wc sonnets.txt
   2620   17670   95635 sonnets.txt
```
sonnects.txt 这个文件一共有 2620行，17670个单词，95635字节。

```ruby
⮀ head sonnets.txt
Shake-speare's Sonnets

I

From fairest creatures we desire increase,
That thereby beauty's Rose might never die,
But as the riper should by time decease,
His tender heir might bear his memory:
But thou contracted to thine own bright eyes,
Feed'st thy light's flame with self-substantial fuel,

⮀ tail sonnets.txt | wc
     10      77     425
```

`head sonnets.txt` 印出了该文件前10行

`tail sonnets.txt | wc` 是串联了 pipe `|` 的用法，拿到对象的末尾10行，接着进行 `wc` 操作。

---

##### 6. grep

- `grep` 用来查找内容

Examples:
```ruby
⮀ grep rose sonnets.txt
The rose looks fair, but fairer we it deem
As the perfumed tincture of the roses.
Die to themselves. Sweet roses do not so;
Roses of shadow, since his rose is true?
Which, like a canker in the fragrant rose,
Nor praise the deep vermilion in the rose;
The roses fearfully on thorns did stand,
 Save thou, my rose, in it thou art my all.
I have seen roses damask'd, red and white,
But no such roses see I in her cheeks;
```
`grep rose sonnets.txt` 会拿到所有含有 rose 的行，然后印出每一行

```ruby
⮀ grep rose sonnets.txt | wc
     10      82     419
```

pipe `|` 在语法上的含义类似ruby中的点号`.`，可以将命令串起来执行

```ruby
⮀ grep -i rose sonnets.txt | wc
     12      96     508
```

`grep` 还对应有许多options，比如 `-i` 代表 ignore-case 不区分大小写。

查看 `grep` 的其他用法可以先输入 `man grep` 然后 `/case`

```ruby
-i, --ignore-case
        Perform case insensitive matching.  By default...
--include
        If specified, only files matching the given filename...
--include-dir
        If -R is specified, only directories matching the given...
-J, --bz2decompress
        Decompress the bzip2(1) compressed file before looking for...
-L, --files-without-match
        Only the names of files not containing selected lines are ...
```

一个包含pipe的command chain用法

```
⮀ curl -I https://www.google.com
HTTP/2 200
date: Mon, 12 Mar 2018 08:20:27 GMT
expires: -1
cache-control: private, max-age=0
content-type: text/html; charset=ISO-8859-1
p3p: CP="This is not a P3P policy! See g.co/p3phelp for more info."
server: gws
x-xss-protection: 1; mode=block
x-frame-options: SAMEORIGIN
set-cookie: 1P_JAR=2018-03-12-08; expires=Wed, 11-Apr-2018 08:20:27 GMT; path=/; domain=.google.com
set-cookie: NID=125=uunagSTk2dtx9gNecdSyP-3eF6HDUNIsGOtFMIo2oOARSZvO2KDanwo1r2JOsI81qlYUOnDvQ2UoOBDz485Na-Pqa6V-cruuW7mf_kqaiTvhhbkiHYT7DuQ2H4b9y_qZ; expires=Tue, 11-Sep-2018 08:20:27 GMT; path=/; domain=.google.com; HttpOnly
alt-svc: hq=":443"; ma=2592000; quic=51303431; quic=51303339; quic=51303335,quic=":443"; ma=2592000; v="41,39,35"
accept-ranges: none
vary: Accept-Encoding

⮀ curl -I https://www.google.com | grep -i http
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                Dload  Upload   Total   Spent    Left  Speed
 0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
HTTP/2 200
set-cookie: NID=125=Bp-8t_5iO9EIGHK3l_-TezXFQkPveAREfavYWMkYjfORTBv88JOqKyeMl3HyLRtAyHIgLenCCCTCLBsvy3iHqvIcWt68zTIuJ0fjBojF85LK1Qbuon68Dji94R6JHxN0; expires=Tue, 11-Sep-2018 08:20:34 GMT; path=/; domain=.google.com; HttpOnly

⮀ curl -I https://www.google.com | grep -i http | wc
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                Dload  Upload   Total   Spent    Left  Speed
 0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
      2      11     240
⮀
```

注意上面每一步加上的 pipe line 和后续的指令

---



##### 附：

- 常用键盘功能按键的符号表示

|Key	|Symbol|
|:-:|:-:|
|Command	|⌘|
|Control	|⌃|
|Shift	|⇧|
|Option	|⌥|
|Up, down, left, right	|↑ ↓ ← →
|Enter/Return	|↵|
|Tab	|⇥|
|Delete|	⌫|
