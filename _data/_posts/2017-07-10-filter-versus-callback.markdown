---
title:  "Filter 和 Callback"
date:   2017-07-10 01:04:23
categories: [Programming]
tags: [Ruby & Rails]
---


此问题源自对 :before_action 和 :before_create 二者区别的查究。

**简言之 `:before_action` 这类被称为 [Filter](http://guides.rubyonrails.org/action_controller_overview.html#filters)。 `:before_create` 这类被称为 [Callback](http://guides.rubyonrails.org/active_record_callbacks.html)。**

Rails Guide 中的说明：

* Filter
> Filters are methods that are run "before", "after" or "around" a controller action.

* Callback
> Callbacks are methods that get called at certain moments of an object's life cycle. With callbacks it is possible to write code that will run whenever an Active Record object is created, saved, updated, deleted, validated, or loaded from the database.

Filter 强调的是在调用一个 method 之前或之后使用某个 method, 使用上偏“外围”。

Callbacks 强调的是 在对特定对象进行 create, save, update ... 等操作的生命周期（或理解为完成过程）中，在某一特定步骤(at certain moments) 进行某个操作。

一个不精确但易于从概念上理解的说法是：filter 发生在 method 外，callback 发生在 method 内。

Filter 最常见的就是 before_action, Callback的分类和形式更多：

**[Available Callbacks](http://guides.rubyonrails.org/active_record_callbacks.html#available-callbacks)** <br>
Here is a list with all the available Active Record callbacks, listed in the same order in which they will get called during the respective operations.注意这里列出的callbacks考虑了实际情况中可以被唤起的时间点或说具体步骤。

#### 1 Creating an Object
* before_validation
* after_validation
* before_save
* around_save
* before_create
* around_create
* after_create
* after_save
* after_commit/after_rollback

#### 2 Updating an Object
* before_validation
* after_validation
* before_save
* around_save
* before_update
* around_update
* after_update
* after_save
* after_commit/after_rollback

#### 3 Destroying an Object
* before_destroy
* around_destroy
* after_destroy
* after_commit/after_rollback

#### 4 after_initialize and after_find

#### 5 after_touch

以上具体用法参考rails guide中的说明，guide中也列出了[哪些 method 中可以触发或说接入 callback](http://guides.rubyonrails.org/active_record_callbacks.html#running-callbacks) ，或说提供了使用 callback 的接口。
