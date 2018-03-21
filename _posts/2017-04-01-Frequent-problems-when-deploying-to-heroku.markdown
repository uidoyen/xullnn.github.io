---
title:  "Ruby on Rails, 部署到heroku常见问题"
categories: [Error records ℗]
tags: [Heroku, Ruby & Rails, Deploy]
---

这篇文章收录所有在Ruby on Rails下push到heroku过程中的常见问题及处理方法。
这些问题有可能是由开发环境和生产环境的不同引起，或是由操作上的错误引起。

原则上一切问题还是以官方说明文档为准：[heroku官方帮助文档](https://devcenter.heroku.com/articles/getting-started-with-ruby#introduction)

### 1 Gemfile 文件不正确导致的部署失败。

目前在ruby on rails框架下做的专案在部署到heroku前都会修改gemfile:

将文件中第7行左右的 `gem 'sqlite3'` 这一行剪切搬到40行左右的：

```ruby
group :development, :test do
  ...
  ...
  gem 'debug', platform :mri
  gem 'sqlite3'
end
```

此group最后一行，如上所示。

接着需要在末尾新增一个 group :production , 加入 pg 这个 gem :

```ruby
group :production do
  gem 'pg'
end
```

然后  `command S` 保存后执行  `bundle install`

然后 `git add .` 将更改加入暂存区 `git commit -m "alert gemfile, ready to push"` 确认更改

完成后可以再次 `git push heroku master` (要确保进行这一步前你设置好了heroku remote地址，可以通过`git remote -v`来查看当前专案的remote地址)

**很重要的是执行完push后，记得需要`heroku run rake db:migrate`** run一次资料库。

最后可以 `heroku open` 打开网页查看结果。

### 2 远端地址错误引起的push失败

这类型错误对于初学者较为常见，其实理解了git远端地址的设置操作，对于heroku的理解也就容易了。

一种常见的情境是：

* 第一次（或前几次）尝试push heroku 失败了，做了修改之后跑到heroku网页端的dashboard上将之前建立的那个一直push失败的app删掉了。
* 然后又回到终端继续操作push heroku动作。
* 接着再次报错。

这种情况下的报错一般是：

```ruby
  remote: !  No such app as xxxxxx-xxxxx-xxxx.
  fatal: repository 'https://git.heroku.com/xxxxxx-xxxxx-xxxx.git/' not found
  Couldn't find that app
```

上面信息中提到的 xxxxxx-xxxxx-xxxx 这个app以及对应的url地址就之前被删掉的那个，由于服务器端的仓库被删了，但是本地app中的remote没有修改，还是之前那个，所以按这个地址去push就会返回这样的信息。

处理方法是删掉目前的heroku remote地址 （通常情况下是`git remote rm heroku`）
接着新建一个heroku 专案仓库`heroku create`， 这一步将会自动为你添加新的heroku remote地址。可以通过`git remote -v`来查看当前的heroku远端地址和刚刚 create 生成的地址是否一致。
确认无误后可以再次 `git push heroku branch:master`


### 3 本地app文件太大被拒

这是在做carbolowdrate时遇到的问题，由于我们写了很“重”的seed档，里面引用大量的图片导致文件体积剧增达到了458.6 Mb

![](/images/post_images/9pAGUycQm4FBJVdvAQlQ_屏幕快照 2017-03-02 上午9.53.43.png)

解决方法是批量压缩图片或者以其他方式减小体量，然后再尝试push。


### 4 部署后资料库相关操作问题

这是一次我遇到的问题，背景说明：

做一个购物网站，由于每个商品都有很多图片，就将这些图片一并存入了专案文件夹，并将图片一并写入seed，但是忘了将gitignore中存图的路径去掉，所以push到heroku后跑seed发现只有商品信息，没有图。本地修正问题后准备重新push，但由于seed档每跑一次都会生成新资料而不会覆盖之前的，所以在终端执行`heroku run rake db:drop`试图drop掉之前seed生成的资料，然后重新跑seed。运行此命令时报错：

```ruby
ActiveRecord::ProtectedEnvironmentError: You are attempting to run a destructive action against your 'production' database.

If you are sure you want to continue, run the same command with the environment variable:

DISABLE_DATABASE_ENVIRONMENT_CHECK=1
```

返回信息可以看到其实是一种保护机制，避免你在开发环境上随意删掉产品环境中的资料。但按照提示操作了`DISABLE_DATABASE_ENVIRONMENT_CHECK=1`但还是不行。

解决方法是执行：`heroku pg:reset DATABASE`。[更详细的说明可以看一个stackoverflow上的回答](http://stackoverflow.com/questions/4820549/how-to-empty-db-in-heroku)。

然后按提示再次输入app名称就可以执行成功，drop掉之前部署网站的资料。

![](/images/post_images/WyF3qZZZRiznDllaM9dw_屏幕快照 2017-03-02 下午10.55.11.png)

后续再操作 `rake db:migrate`

### 5 本地新建分支push到heroku上后发现什么都没变

这个问题是由于分支操作失误引起的。

背景说明：

做app过程中我们一般都会先将新的分支push到github然后再push到heroku，刚开始的时候我们都是到github上将所有没有问题的分支merge，然后到本地退回master接着pull下新的master，而且前面几次部署都是`git push heroku master` ，然后这次我们跳过了先去merge再pull的步骤，直接`git push heroku newbranch`，接着就发现heroku端没有变化。

仔细看了终端返回的信息：

![](/images/post_images/QOaMolZ7TcuBEFqiM2mD_屏幕快照 2017-03-09 下午2.28.53.png)

倒数第六行：

```ruby
remote: Pushed to non-master branch, skipping build.
```
“推送到无主干分支，跳过建立“ 意味着这次push操作被忽略了，原因是不知道这个新分支push到哪个分支。

正确的操作是：

```ruby
git push heroku newbranchname:master
```

这样告诉系统我的这个 newbranch 是插到 master 上的。

接着才会返回push到heroku成功的信息。


### 6 本地未commit 变更到git导致push到heroku网页报错

发现push到heroku的过程没有报错，但运行migrate时没报错但少了该有的信息反馈。`git status` 发现是变更没有commit 。
下面是空专案在heroku 跑 migrate 返回的信息。

```ruby
yuanningdeMacBook-Pro:suggestotron yuanning$ heroku run rake db:migrate
Running rake db:migrate on ⬢ intense-peak-10910... up, run.2777 (Free)
D, [2017-04-04T12:09:35.811601 #4] DEBUG -- :    (13.3ms)  CREATE TABLE "schema_migrations" ("version" character varying PRIMARY KEY)
D, [2017-04-04T12:09:35.827673 #4] DEBUG -- :    (11.3ms)  CREATE TABLE "ar_internal_metadata" ("key" character varying PRIMARY KEY, "value" character varying, "created_at" timestamp NOT NULL, "updated_at" timestamp NOT NULL)
D, [2017-04-04T12:09:35.829738 #4] DEBUG -- :    (0.8ms)  SELECT pg_try_advisory_lock(557831323824430425);
D, [2017-04-04T12:09:35.841999 #4] DEBUG -- :   ActiveRecord::SchemaMigration Load (1.0ms)  SELECT "schema_migrations".* FROM "schema_migrations"
D, [2017-04-04T12:09:35.856948 #4] DEBUG -- :   ActiveRecord::InternalMetadata Load (1.0ms)  SELECT  "ar_internal_metadata".* FROM "ar_internal_metadata" WHERE "ar_internal_metadata"."key" = $1 LIMIT $2  [["key", :environment], ["LIMIT", 1]]
D, [2017-04-04T12:09:35.873359 #4] DEBUG -- :    (0.8ms)  BEGIN
D, [2017-04-04T12:09:35.876387 #4] DEBUG -- :   SQL (1.0ms)  INSERT INTO "ar_internal_metadata" ("key", "value", "created_at", "updated_at") VALUES ($1, $2, $3, $4) RETURNING "key"  [["key", "environment"], ["value", "production"], ["created_at", 2017-04-04 12:09:35 UTC], ["updated_at", 2017-04-04 12:09:35 UTC]]
D, [2017-04-04T12:09:35.878954 #4] DEBUG -- :    (2.2ms)  COMMIT
D, [2017-04-04T12:09:35.880000 #4] DEBUG -- :    (0.8ms)  SELECT pg_advisory_unlock(557831323824430425)
yuanningdeMacBook-Pro:suggestotron yuanning$ heroku open
yuanningdeMacBook-Pro:suggestotron yuanning$

```

---

### 7 css/js文件格式错误导致的push失败

这种问题在最后提示失败的时候会告诉：

> Precompiling assets failed......Push rejected, failed to compile Ruby app.

这类错误信息，提示说是compile assets过程中的问题，一直往上找heroku的log，找到rake aborted!处：

```ruby

ExecJs::ProgramError: Unexpected character '@' (line: 12830, col: 0, pos: 387012)

```

![](/images/post_images/屏幕快照 2017-05-03 下午2.21.02.png)
google后有人出现过类似问题：

- [类似问题1](http://stackoverflow.com/questions/23438448/unable-to-push-to-heroku-after-being-able-to-do-do)
- [类似问题2](http://stackoverflow.com/questions/25334276/execjsprogramerror-unexpected-character-when-trying-to-precompile-assets)

**大意都是说js文件里出现了一不正确的字符，在这个错误中提示的是 `@` 这个字符**

于是在 app/assets/javascripts/application.js 中去找，原来是将 引入bootstrap的两行代码 `@import "bootstrap"` 放到了js文件里，导致了错误。


---

### 8 随机网络问题

这个问题不仅会出现在push过程中，push成功后续操作`heroku run rake db:drop` `heroku run rake db:migrate` 之类的命令时也会出现。
有时会返回多个错误信息，但有一个标志是终端返回的 app 地址后会有个粗体红色的感叹号❗️

![](/photos//postimages/1juisCYZTOnKM39LqKlg_Snip20170329_122.png)

返回的信息还有可能是：

```ruby
ActionView::Template::Error (PG::UndefinedTable: ERROR: relation "groups" does not exist
```
```ruby
ENOTFOUND: getaddrinfo ENOTFOUND api.heroku.com api.heroku.com:443 Error for heroku create
```
```ruby
FATAL: permission denied for database "postgres"
```
```ruby
ETIMEOUT: connect ETIMEOUT 50.19.103.36....
```


如果是网络问题导致的push或者资料表操作失败，首先是要更换网络或者线路。后续可能会由于前面连续不完整地操作资料库，导致一些错误。需要drop掉重新跑。可以先尝试`heroku restart` 接着 `heroku run rake db:migrate`

如果发现类似4中的情况可以借用相同的解决方式：

`heroku run pg:reset DATABASE`

`heroku run rake db:migrate`

`heroku run rake db:seed`


---

**一个具有迷惑性的网络错误：**

这也是在push到heroku成功后，在运行`heroku run rake db:migrate` 时出错，错误之指向了一个view相关的文件

还有 `ActionView::Template::Error (PG::UndefinedTable: ERROR:  relation "groups" does not exist` 的报错

```ruby
xxxMacBook-Pro:rails102 wangtao$ heroku logs

......


2017-04-03T12:25:46.282886+00:00 heroku[router]: at=info method=GET path="/" host=agile-forest-51820.herokuapp.com request_id=ed6699db-d3b6-471f-8340-bfeb318e534c fwd="42.200.194.245" dyno=web.1 connect=1ms service=119ms status=500 bytes=1669 protocol=https
2017-04-03T12:25:46.837107+00:00 heroku[router]: at=info method=GET path="/favicon.ico" host=agile-forest-51820.herokuapp.com request_id=8f6e9c85-f261-49d5-91aa-e72ae36e6bbd fwd="42.200.194.245" dyno=web.1 connect=1ms service=2ms status=200 bytes=143 protocol=https
2017-04-03T12:27:41.585433+00:00 app[api]: Starting process with command `bundle exec rake db:migrate` by user wangtao14385@gmail.com
2017-04-03T12:27:45.922886+00:00 heroku[run.9695]: State changed from starting to up
2017-04-03T12:27:45.856504+00:00 heroku[run.9695]: Awaiting client
2017-04-03T12:28:15.865296+00:00 heroku[run.9695]: Error R13 (Attach error) -> Failed to attach to process
2017-04-03T12:28:15.868993+00:00 heroku[run.9695]: Process exited with status 128
2017-04-03T12:28:15.923816+00:00 heroku[run.9695]: State changed from up to complete
2017-04-03T12:30:20.331604+00:00 app[web.1]: I, [2017-04-03T12:30:20.331510 #4]  INFO -- : [86c789ff-e828-469e-afd9-177dab0f7c2a] Started GET "/" for 42.200.194.245 at 2017-04-03 12:30:20 +0000
2017-04-03T12:30:20.332618+00:00 app[web.1]: I, [2017-04-03T12:30:20.332564 #4]  INFO -- : [86c789ff-e828-469e-afd9-177dab0f7c2a] Processing by GroupsController#index as HTML
2017-04-03T12:30:20.333860+00:00 app[web.1]: I, [2017-04-03T12:30:20.333797 #4]  INFO -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]   Rendering groups/index.html.erb within layouts/application
2017-04-03T12:30:20.337969+00:00 app[web.1]: D, [2017-04-03T12:30:20.337890 #4] DEBUG -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]   Group Load (1.6ms)  SELECT "groups".* FROM "groups"
2017-04-03T12:30:20.338863+00:00 app[web.1]: I, [2017-04-03T12:30:20.338792 #4]  INFO -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]   Rendered groups/index.html.erb within layouts/application (4.8ms)
2017-04-03T12:30:20.339142+00:00 app[web.1]: I, [2017-04-03T12:30:20.339072 #4]  INFO -- : [86c789ff-e828-469e-afd9-177dab0f7c2a] Completed 500 Internal Server Error in 6ms (ActiveRecord: 1.6ms)
2017-04-03T12:30:20.340379+00:00 app[web.1]: F, [2017-04-03T12:30:20.340307 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]   
2017-04-03T12:30:20.340450+00:00 app[web.1]: F, [2017-04-03T12:30:20.340387 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a] ActionView::Template::Error (PG::UndefinedTable: ERROR:  relation "groups" does not exist
2017-04-03T12:30:20.340452+00:00 app[web.1]: LINE 1: SELECT "groups".* FROM "groups"
2017-04-03T12:30:20.340452+00:00 app[web.1]:                                ^
2017-04-03T12:30:20.340453+00:00 app[web.1]: : SELECT "groups".* FROM "groups"):
2017-04-03T12:30:20.340723+00:00 app[web.1]: F, [2017-04-03T12:30:20.340646 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]     12:       </tr>
2017-04-03T12:30:20.340725+00:00 app[web.1]: [86c789ff-e828-469e-afd9-177dab0f7c2a]     13:     </thead>
2017-04-03T12:30:20.340725+00:00 app[web.1]: [86c789ff-e828-469e-afd9-177dab0f7c2a]     14:     <tbody>
2017-04-03T12:30:20.340726+00:00 app[web.1]: [86c789ff-e828-469e-afd9-177dab0f7c2a]     15:       <%= render :partial => "group_item", :collection => @groups, :as => :group %>
2017-04-03T12:30:20.340726+00:00 app[web.1]: [86c789ff-e828-469e-afd9-177dab0f7c2a]     16:     </tbody>
2017-04-03T12:30:20.340726+00:00 app[web.1]: [86c789ff-e828-469e-afd9-177dab0f7c2a]     17:   </table>
2017-04-03T12:30:20.340727+00:00 app[web.1]: [86c789ff-e828-469e-afd9-177dab0f7c2a]     18: </div>
2017-04-03T12:30:20.340788+00:00 app[web.1]: F, [2017-04-03T12:30:20.340726 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]   
2017-04-03T12:30:20.340900+00:00 app[web.1]: F, [2017-04-03T12:30:20.340803 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a] app/views/groups/index.html.erb:15:in `_app_views_groups_index_html_erb__2670371387529623777_70121649390860'
2017-04-03T12:30:20.364505+00:00 heroku[router]: at=info method=GET path="/" host=agile-forest-51820.herokuapp.com request_id=86c789ff-e828-469e-afd9-177dab0f7c2a fwd="42.200.194.245" dyno=web.1 connect=1ms service=11ms status=500 bytes=1669 protocol=https
2017-04-03T12:30:20.938892+00:00 heroku[router]: at=info method=GET path="/favicon.ico" host=agile-forest-51820.herokuapp.com request_id=5224cd33-46ed-4ad2-b3d5-4b39f9c61a6e fwd="42.200.194.245" dyno=web.1 connect=1ms service=2ms

......

2017-04-03T12:31:13.973915+00:00 heroku[router]: at=info method=GET path="/" host=agile-forest-51820.herokuapp.com request_id=b76e81e3-a22b-402f-9b31-0d5fb223b99b fwd="42.200.194.245" dyno=web.1 connect=1ms service=10ms status=500 bytes=1669 protocol=https

```

很具迷惑性的是这一段关于view的报错：

```ruby
 F, [2017-04-03T12:30:20.340646 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]     12:       </tr>
......    13:     </thead>
......    14:     <tbody>
......    15:       <%= render :partial => "group_item", :collection => @groups, :as => :group %>
......    16:     </tbody>
......    17:   </table>
......    18: </div>
 F, [2017-04-03T12:30:20.340726 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a]   
F, [2017-04-03T12:30:20.340803 #4] FATAL -- : [86c789ff-e828-469e-afd9-177dab0f7c2a] app/views/groups/index.html.erb:15:in `_app_views_groups_index_html_erb__2670371387529623777_70121649390860'
```
