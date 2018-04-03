---
title:  "Testing advices from 《Everyday Rails Testing with Rspec》"
categories: [Test ℗]
tags: [Rails Tests]
---

书目来源：《Everyday Rails Testing with RSpec》

https://leanpub.com/everydayrailsrspec

(这本书有中文版)

## From Chapter 12 Parting advice - 离别建议

You’ve done it! If you’ve been adding tests to your application as you worked through the patterns and techniques outlined in this book, you should have the beginnings of a well-tested Rails application. I’m glad you’ve stuck with it this far, and hope that by now you’re not only comfortable with tests, but maybe even beginning to think like a true test-driven developer, and using your specs to influence your applications’ actual under-the-hood design. And dare I say, you might even find this process fun!

To wrap things up, here are a few things to keep in mind as you continue down this path:

如果你已经依照书中概述的测试模式和技术，将其应用到了你的app中，你应该成为了一个不错的初级测试者。很高兴你坚持到了这里，希望你已经习惯了测试，甚至可能已经开始像一个测试驱动开发的开发人员那样思考，使用测试标准影响你的app， 并且是在底层设计上。 并且我敢说，你也许还发现这很有趣！

作为总结，这里有一些建议可以作为你继续探索这条路的参考：

### Practice testing the small things - 练习测试小的东西

Diving into TDD through complex new features is probably not the best way to get comfortable with the process. Focus instead on some of the lower-hanging fruit in your application. Bug fixes and basic instance methods are often straightforward tests, usually requiring a little bit of setup and a single expectation. Just remember to write the spec before tackling the code!

一开始就通过钻入复杂的新功能来了解 TDD 或许不是一个合适的途径。不如将注意力转移到你app中那些低垂的果实上(容易测试的部分)。修复bug基本的instance methods通常是很直白的测试，测试它们只需要少量的setup和单个的expectation。但要记住要在处理代码之前先写好测试标准。

### Be aware of what you’re doing - 意识到你在做什么

As you’re working, think about the processes you’re using. Take notes. Have you written a spec for what you’re about to do? Does the spec cover edge cases and fail states? Keep a checklist handy as you work, making sure you’re covering what needs to be covered as you go.

工作时，想清楚你正在使用的处理流程。记录下来。你有没有写下你将要完成的事情的指标？ 这些这些指标是否覆盖到了边缘案例和失败的状态？ 手边最好准备一个checklist， 确保你覆盖到了你想要达到的指标。

### Short spikes are OK - 小问题的存在是OK的

Test-driven development doesn’t mean you can only write code once it’s got a test to back it. It means you should only write production code after you’ve got the specs.

TDD 开发并不意味一旦你写好了测试作为基础就可以(为某个功能)只写一次代码。实际意思是你应该在写产品代码前写好你要达到的标准。

Spikes are perfectly fine! Depending on the scope of a feature, I’ll often spin up a new Rails application or create a temporary branch in Git to tinker with an idea. I’ll typically do this when I’m experimenting with a library or some relatively wholesale change.

存在小问题是完全正常的。根据功能大小的不同，我会开一个新的 rails app 或者建立一个临时分支来修补打磨这个想法。尤其是但我在对一个library进行实验或进行某些大规模修改的时候。

For example, I once worked on a data mining application in which I needed to completely overhaul the application’s model layer, without adversely affecting the end user interface. I knew what my basic model structure would look like, but I needed to tinker with some of the finer points to fully understand the problem. By spiking this in a standalone application, I was free to hack and experiment within the scope of the actual problem I’m trying to solve–then, once I’d determined that I understood the problem and have a good solution for it, I opened up my production application, wrote specs based on what I learned in my tests, then wrote code to make those specs pass.

比如，我曾经开发一个数据挖掘的app，当时我需要彻底检修app的model层，但不能影响到用户端的接口功能。我知道基础的model结构应该是什么样的，但我需要一些细小的微调来完全理解问题所在。 于是我将app复制出来作为单独app，然后在需求范围内实验和处理我要解决的问题，一旦我决定我已经理解了问题并有了好的解决方案，我会回到实际产品app中，基于我的之前的测试写下测试标准，接着写代码让测试通过。

For smaller-scale problems I’ll work in a feature branch of the application, doing the same type of experimentation and keeping an eye on which files are getting changed. Going back to my data mining project, I also had a feature to add involving how users would view already-harvested data. Since I already had the data in my system, I created a branch in Git and spiked a simple solution to make sure I understood the problem. Once I had my solution, I removed my temporary code, wrote my specs, and then systematically reapplied my work.

对于小规模的问题，我会在app的feature分支中修改，进行同样的试验，并关注动到了哪些文件。回到那个数据挖掘的app，另外我还需要一个了'解用户是如何查看他们收获到的数据'的功能。 由于系统中已经有了相关的数据，我新开了一个git分支，然后写出一个简答的解决方案确保我理解了问题。一旦我有了解决方案，我会移除临时分支，写下我的测试标准，然后修改代码让测试通过，如此往复。

As a general rule, I try to retype my work as opposed to just commenting and uncommenting it (or copying and pasting). I often find ways to refactor or otherwise improve what I did the first time.

作为一个通用的规则，我会尝试重写写代码，而不是将已有代码注释掉或者复制粘贴代码。这么做让我经常找到重构和完善的第一次写的代码的方法。

### Write a little, test a little is also OK - 写一点代码，测试一点也是OK的。

If you’re still struggling with writing specs first, it is acceptable to code, then test; code, then test–as long as the two practices are closely coupled. I’d argue, though, that this approach requires more discipline than just writing tests first. In other words, while I say it’s OK, I don’t think it’s ideal. But if it helps you get used to testing then go for it.

如果你仍然不习惯先写测试指标，那么写一点代码，接着测试一下，然后在写一点代码，再测试一下也是可以接受的--只要这两个步骤是紧密配合的。但我不得不说，这种方式比先写测试需要更强的纪律性（自律）。换言之，我说这么做OK的意思是，我并不认为这是理想的方法。但是如果这么做能让你习惯于测试，也无妨。

### Try to write integration specs first - 试着先写集成测试

Once you get comfortable with the basic process and the different levels at which to test your application, it’s time to turn everything upside down: Instead of building model specs and then working up to controller and feature or request specs, you’ll start with feature or request specs, thinking through the steps an end user will follow to accomplish a given task in your application. This is essentially what’s referred to as outside-in testing, and is the general approach we followed in chapter 11.

如果你已经习惯了不同层级的基础测试流程，那么是时候将整个过程反转过来了： 之前都是先建立model层的测试标准，然后触及controller然后feature或request测试标准，你现在应该从feature或request测试标准开始，思考用户是以什么样的动线使用你的app来完特定功能的。 这本质上就是所谓的 outside-in 测试， 也是我们大体上在第11章中使用的方法。

As you work to make the feature spec pass, you’ll recognize facets that are better- tested at other levels–in the previous chapter, for example, we tested validations at the model level; authorization nuances at the controller level. A good feature spec can serve as an outline for all of the tests pertaining to a given feature, so learning to begin by writing them is a valuable skill to have.

在你让feature测试通过的时候，你会识别到一些你在其他层级已经进行了更好的测试的方面--比如在之前的章节，我们在model层级测试validation，在controller层级测试权限设置。 一个好的feature测试可以视作对一个特定feature相关的所有测试的概括，因此学习这么写测试是很有价值的。


### Make time for testing - 为测试留出时间

Yes, tests are extra code for you to maintain, and that extra care takes time. Plan accordingly, as a feature you may have been able to complete in an hour or two before might take a little longer now. This especially applies when you’re getting started with testing. However, in the long run you’ll recover that time by working from a more trustworthy code base.

不错，测试需要额外花时间来维护。刚开始的时候，为一个功能计划相应的测试或许需要1到2个小时或者更多时间。尤其是你刚接触测试时。不过，长期来看，你将会因为拥有更坚实的代码基础而收回这些时间。

### Keep it simple - 保持简洁

If you don’t get some aspects of testing right away–whether it’s integration testing, or mocking and stubbing–don’t worry about it. They require some additional setup and thinking to not just work, but actually test what you need to test. Don’t stop testing the simpler parts of your app, though–building skills at that level will help you grasp more complicated specs sooner rather than later.

如果你现在还没能理解测试的某些方面--不管是集成测试还是mocking或stubing--不要担心。 这些内容需要一些额外的setup以及更多思考，而不只是做过就可以掌握的。不要停止测试app中那些简单的部分--充分理解基础的测试会让你更快地掌握复杂的测试。

### Don’t revert to old habits! - 不要掉回到旧的习惯中

It’s easy to get stuck on a failing test that shouldn’t be failing. If you can’t get a test to pass, make a note to come back to it–and then come back to it. Remember, point- and-click testing in your browser will only get slower and more tedious as your application grows. Why not use the time you’ll save on getting better at writing specs?

有时我们会卡在一个不应该失败的测试中。如果你现在无法让一个测试通过，做好标记之后再回过头来处理。 记住，在浏览器中点点写写只拖慢你的开发进度和让你感到越来越无聊。 为什么不用这些浪费掉的时间来写更好的测试标准。

### Use your tests to make your code better - 使用测试优化你的代码

Don’t neglect the Refactor stage of Red-Green-Refactor. Learn to listen to your tests– they’ll let you know when something isn’t quite right in your code, and help you clean house without breaking important functionality.

不要忘记 Red - Green - Refactor 循环中的重构环节。 听从你的测试--它会让你意识到代码中某些不对的地方，然后帮助你清扫代码而不破坏重要的功能。

### Sell others on the benefits of automated testing - 向其他人兜售自动化测试的好处

I still know far too many developers who don’t think they have time to write test suites for their applications. (I even know a few who think that being the only person in the world who understands how a brittle, spaghetti-coded application works is actually a form of job security–but I know you’re smarter than that.) Or maybe your boss doesn’t understand why it’s going to take a little longer to get that next feature out the door.

我仍然知道有太多的开发者认为他们没有时间写测试。（我甚至知道有些人认为，成为世界上唯一一个能理解某些冗长复杂代码人，是对自己工作的保障）或者你的老板不明白为什么要多花一点时间来完成一个功能。

Take a little time to educate your colleagues. Tell them that tests aren’t just for development; they’re for your applications’ long-term stability and everyone’s long- term sanity. Show them how the tests work–I’ve found that showing off a feature spec with JavaScript dependencies, as we put together in chapter 7, provides a wow factor to help these people understand how the extra time involved in writing these specs is time well-spent.

花一点时间来教育你的同事。测试并不只是针对程式开发，测试是为了app的长期稳定性以及所有人的长期精神健康的。向他们展示测试是如何工作的--我曾发现展示一个对js附件的feature测试，如我们在第7章所做的，帮助这些人突然意识到这些用来写测试标准的额外时间是多么的有价值。

### Keep practicing - 坚持练习

Finally, it might go without saying, but you’ll get better at the process with lots of practice. Again, I find toy Rails applications to be great for this purpose–create a new app (blogging app and to-do lists are always popular), and practice TDD as you build a feature set. What determines your features? Whatever testing skill you’re building. Want to get better at specs for email? Make a blog that sends an email digest version to subscribers. If you want to hone your skills at testing external APIs, many services provide free or cheap developer tiers, perfect for practice. Don’t wait for a feature request to arise in a production project–make up your own!

最后，不言自明的是，大量的练习会让你更好地掌握这些流程。再一次的，我发现Rails玩具应用能够很好地达到这个目的--新建一个rails app(常见的是blog或to-do-list)，用TDD的方式逐步完成功能集。什么决定了你的功能？ 不管你在构建哪方面的测试技能。想要提高email的测试功能？写一个会发送订阅digest链接给用户的app.如果想要打磨测试外部API的技能，许多免费的提供API接口的服务，是很好的练习资源。 不要等着别人提出对产品新功能的要求--自己做一个。
