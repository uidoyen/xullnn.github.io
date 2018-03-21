---
title:  "git reset 与 git revert 用于版本回退"
categories: [Version Control ℗]
tags: [Github]
---

git revert 和 git reset 都可以用来撤销更改，二者间最重要的区别在于：**git reset 会更改 commit 历史， git revert 不更改之前的commit 历史， 而是向前新增一个commit 点来记录此次revert的操作。**

介绍二者用法前提到这个重要区别的原因是，在多人协作中，一定要考虑好是否要去更改commit历史。如果本地更改了 commit 历史，而其他人pull的是更改之前的版本，之后的操作会造成许多冲突的情况。

### git reset

`git reset` 可以退回到指定 commit 的版本，如果指定了文件，可以更加精确地限制更改的目标。**注意使用 git commit 会改写 commit 历史，操作前一定确认是否有其他人同样在此分支上工作。**

`git reset` 有三个设置选项：

- --soft 只撤销 commit 快照区staging area 会处于 ready to commit 状态

- --mixed 撤销 commit 和 快照区staging area 的变更，本地变更会变成未加入快照区的状态

- --hard 同时撤销 commit , 快照区 ，本地文件。

还是用checkout 那篇中4次commit的例子来示范：

#### 1 `git reset HEAD~2 --soft`

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo/demo ⮀ ⭠ branch01 ⮀ git reset HEAD~2 --soft
 caven@CavendeMacBook-Pro ⮀ ~/demo/demo ⮀ ⭠ branch01± ⮀ git status
On branch branch01
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   file1.html.erb  # 绿色
	modified:   file2.html.erb  # 绿色

 caven@CavendeMacBook-Pro ⮀ ~/demo/demo ⮀ ⭠ branch01± ⮀ git log

 commit 6f4624d3e676d815af52b818061ff97f5bbb01a1
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:01:19 2017 +0800

    #2

commit 76c535efd499d40185d1a191f80267f03aa4a4f3
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:01:00 2017 +0800

    #1

commit d35ab1c888eacc44f296e83526353052d48c49bf
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 09:07:24 2017 +0800

    clean


```

可以看到 commit #3 和 #4 都从commit历史中消失了，`git status`可以看到 最后这两次commit修改的文件处于 ready to commit 的状态， ternimal 中呈绿色。如果此时输入一条commti 那么这个新的 commit 将取代 #3 和 #4 。**但本地文件并没有更改，这里变化的只是 commit 历史**

```ruby

commit 91cc8b0b5ae62288c57bbcba2addcacdd8942559
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:09:25 2017 +0800

    after reset

commit 6f4624d3e676d815af52b818061ff97f5bbb01a1
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:01:19 2017 +0800

    #2

commit 76c535efd499d40185d1a191f80267f03aa4a4f3
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:01:00 2017 +0800

    #1


```

#### 2 git reset HEAD~2 (--mixed)
--mixed 是 git reset 的默认设置，不用写直接 git reset HEAD~2 也可以。

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo/demo1 ⮀ ⭠ branch01 ⮀ git reset HEAD~2
Unstaged changes after reset:
M	file1.html.erb
M	file2.html.erb
caven@CavendeMacBook-Pro ⮀ ~/demo/demo1 ⮀ ⭠ branch01± ⮀ git status
On branch branch01
Changes not staged for commit:
 (use "git add <file>..." to update what will be committed)
 (use "git checkout -- <file>..." to discard changes in working directory)

 modified:   file1.html.erb  # 红色
 modified:   file2.html.erb  # 红色

no changes added to commit (use "git add" and/or "git commit -a")
caven@CavendeMacBook-Pro ⮀ ~/demo/demo1 ⮀ ⭠ branch01± ⮀ git log

commit e4c12df6449b01ae6ee96cf87657e16ccb0e3d40
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:16:56 2017 +0800

    #2

commit 24cd68d37934b5dac15bdcf52cf21eed5332165f
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:16:23 2017 +0800

    #1

commit 515326d25a6ac0aabeb5aa8d7f80f29d676648ec
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:15:51 2017 +0800

    clean

```

**与--soft 一样， --mixed 并没有改变本地历史，它比 soft 多做的只是把最后两次commit 的变更也从 快照区 staging area 移除了。** 所以可以终端可以看到：<br>
Unstaged changes after reset:<br>
M	file1.html.erb<br>
M	file2.html.erb”

#### 3 git reset HEAD~2 --hard

这个命令会直接修改 commit 历史， staging area 以及 本地文件。 它相当于直接删掉了 #3 和 #4 这两个 commit历史，并更改本地文件。你也不需要再进行 commit 操作， staging area 也会是干净的。

```ruby

caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01 ⮀ git reset HEAD~2 --hard
HEAD is now at e4c12df #2
caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01 ⮀ atom .
caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01 ⮀ git status
On branch branch01
nothing to commit, working tree clean
caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01 ⮀ git log


commit e4c12df6449b01ae6ee96cf87657e16ccb0e3d40
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:16:56 2017 +0800

    #2

commit 24cd68d37934b5dac15bdcf52cf21eed5332165f
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:16:23 2017 +0800

    #1

commit 515326d25a6ac0aabeb5aa8d7f80f29d676648ec
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:15:51 2017 +0800

    clean

```

可以看到 `git status` 显示 nothing to commit, working tree clean 。 此时查看本地文件，#3 和 #4 两次commit 修改的内容已经被撤销。




### git revert

与 reset 不同的是:<br>
a.revert 改变的不是从当前HEAD到指定commit的所有内容，而只是尝试撤销具体指定的那个commit 的变更。<br>
b.revert 不改变之前的 commit 历史，而是向前再commit 一次，用一个新的commit 来保存这次撤销操作。
c.默认情况下 revert 会尝试自动帮你 commit（如果没有冲突）, commit 信息会是 "Revert "specified commit message"" 这样。

因此，执行 git revert 撤销某一个或多个 commit 的更改后，会容易引起 conflict。 唯一不会引起 conflict 的操作是 `git revert HEAD` 执行这个指令后会跳出 commit 信息编辑器，让你修改 commit 信息，如果默认给出的message 就可以的话键入 `:wq` 保存退出commit 编辑视窗，此时查看 `git log` 就可以看到刚刚的 commit，而之前的commit历史没有任何改变。

![](/images/post_images/Snip20170608_9.png)


#### 1 git revert HEAD~2

执行这个命令后 revert 会处于 processing 状态， 提示让解决 conflict 才能继续 revert 进程：

```ruby
caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01 ⮀ git revert HEAD~2
error: could not revert e4c12df... #2
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'
✘ caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01± ⮀

```

此时查看 本地文件：

![](/images/post_images/Snip20170608_10.png)

git 标注出了file1中冲突部分，需要手动调整代码。但注意一下 file2 并没有变化，因为HEAD~2 指定的实际上是倒数第三个commit，这个commit中没有包含修改 file2 代码的操作（file2的代码是在最后一次commit 时提交的，也就是 HEAD）。所以并不会影响file2中的内容。

注：关于git 对冲突部分的标注还存在疑问，git 标注的范围是 包含指定 commit 在内，直至最后一次 commit 的内容，而不是单独那一次 commit 的内容。

`git status` 查看修改到的文件：

```ruby
✘ caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01± ⮀ git status
On branch branch01
You are currently reverting commit e4c12df.
 (fix conflicts and run "git revert --continue")
 (use "git revert --abort" to cancel the revert operation)

Unmerged paths:
 (use "git reset HEAD <file>..." to unstage)
 (use "git add <file>..." to mark resolution)

 both modified:   file1.html.erb  # 红色， 前面提到 both, 指的应该是 revert 后的状态与当前 HEAD 所在commit 的状态

no changes added to commit (use "git add" and/or "git commit -a")
caven@CavendeMacBook-Pro ⮀ ~/demo/demo2 ⮀ ⭠ branch01± ⮀

```

去到存在冲突的文件，手动保留需要的代码，然后 commit 保存。之后再查看 commit 历史会发现之前的commit 历史没变， 新增了一个commit 用来记录刚刚的revert操作：

```ruby
commit 64a8a9b3cb4290d3498782203b244f415a2e9200
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 16:36:04 2017 +0800

    revert done

commit 2232024b2103ffb2cb60de5213c1a52ba8300736
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:17:43 2017 +0800

    #4

commit 6efebe087a7985a6a274d14e4d29064c69f32bf6
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:17:09 2017 +0800

    #3

commit e4c12df6449b01ae6ee96cf87657e16ccb0e3d40
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:16:56 2017 +0800

    #2

commit 24cd68d37934b5dac15bdcf52cf21eed5332165f
Author: Caven <caven@CavendeMacBook-Pro.local>
Date:   Thu Jun 8 11:16:23 2017 +0800

    #1

```

#### 2 git revert HEAD~2 <path>

类似 `git revert HEAD~2 app/views/user/index.html` 这样的 命令，在之前的基础上进一步精确了范围, 指定了需要撤销的文件。除了被指定的文件，其他文件都不会被动到。
