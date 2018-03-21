---
title:  "MVC-Architecture"
categories: [Translations]
tags: [Ruby & Rails, MVC]
---

视频地址：https://www.youtube.com/watch?v=eTdVkgF_Slo

![](/images/post_images/28.15.png)

In the last movie, I told you that rails is structured in such a way that it helps us to write dry code. Remember don't repeat yourself. In this movie I want to take a closer look at that which is the MVC architecture that rails employs. It's a fundamental aspect of rails and it's important to understand right from the start.

在上一个视频中，我告诉过你们rails是以一种能帮助我们书写干净代码的方式来构建自身。记得不要做重复的事。在这个视频中我想带你们仔细看看rails所采用的MVC构架。它是rails的一个基本特性， 从一开始就正确理解它是很重要的。

The M stands for model, the V stands for view, and the C stands for controller. And the model is our objects, it's the object-oriented approach to design. It actually encapsulates the data in our database as well we can treat those as objects.

M 代表 model, V 代表 view, C 代表 controller 。Model 就是我们的对象， 它是以面向对象的方式来设计的。它实际上封装了数据库中的数据，我们也可以把它们作为对象来处理。

The view is the presentation layer, it's what the users sees and interacts with the web pages HTML, the CSS, the Javascript.

View属于外观层面，就是用户看到并与之交互的Web页面的HTML，CSS，JavaScript。

The controller is going to process and respond to events such as user actions, and it's going to invoke changes to the model in the view. Based on that， it's going to make decisions for us and control what happens.

Controller 将对用户操作等事件进行处理和响应， 它将会在view页面中引发model的变化， 基于此，它帮我们决定要去做什么并控制会发生什么。

![](/images/post_images/89403d0a269857c1f2296e4ea6d0f8304bc8715f.png)

Let's take a look at a couple of diagrams that I think will make it clearer.

让我们来看一些图表来让这些内容更清晰。

In the basic web architecture we have a browser that interacts with a webpage and of course there's a web server sitting in between. But this is a simplified view and this webpage might have lots code that make decisions for us and finally out put something back to the browser, and even might interact with the database right if its database enabled， it can pull things out of the database, and then return that to the browser.

在基本的Web结构中，我们有一个浏览器，它与一个网页交互，当然中间还有一个web服务器。但这是一个简化的视图，这个网页可能有很多代码为我们做决定，最后把一些东西放回浏览器，甚至可以与数据库进行交互，如果它的数据库启用，它可以将数据从数据库中取出，然后返回到浏览器。

![](/images/post_images/4ef5e5ad457518c07ec513899560308e4631ea67.png)

Well the MVC architecture says well instead of having this one page, that's all muddied up with all of this different stuff going on on it. What if we broke it up. We have the browser that communicates to the controller and just the code involved in making those decisions about what should happen based on those actions. That's what's going to be in the controller. And then, if we need to interact with the database or any of our data, we'll have the controller talk to our model and we'll put all of our code relating to the data and to connecting with the database in the model, and the model can return its results back to controller, controller can go back to the model if it needs, the model can go back to the database and so on. But finally when the controller is satisfied that it's ready to return a result the browser. It's going to then send its results to the view, the presentation layer, which will decide what HTML, Javascript, CSS, all of that will get return back to the browser.

那么MVC结构说与其把这些东西放在一个页面里——所有这些不同的东西都搅在一起并且继续这样下去，如果我们将它拆开。我们用浏览器与controller进行通信，只涉及那些决定这些动作应该发生什么的代码。这就是controller要做的。接下来，如果我们需要与数据库进行交互或者需要我们的任何数据，我们将由controller告诉我们的model，并且把所有与数据库相关的代码或者需要连接到数据库的代码都放在model里，随后model将会将结果返回给controller， controller可以根据需要再去到model，model将再次去到database， 如此往复。不过最后当controller满意了，它就准备给浏览器返回一个结果。它接下来就会发送一个结果给view，即显示层面，view会决定浏览器将使用哪些HTML, Javascript, CSS。

![](/images/post_images/cb0116519a958e89035e8e1dc0c7a9ad49387650.png)

So essentially we've just taken that one page that web page and broken it up based on its function to controller, model, and view. And rails is built on the this fundamental principle: the controller handles the decisions, the model handles the data and the view handles the presentation. And we want to try and follow this architecture and keep our code in the right places. Decision code should go to the controller, data code goes to the model, presentation code goes to the view.

因此，本质上，我们只是拿到一个网页，基于其功能将其拆解为controller, model, view。同时rails以这个原则而构建：controller处理决定，model处理数据，view处理显示。所以我们试图跟随这个结构，让我们的代码处于正确的位置。做决定的代码应该进到controller，数据相关的代码进到model，显示层面的代码进到view。

![](/images/post_images/6c5545c20055bcb5b0bccac4d2c902d6fbd85104.png)

Rails actually has names for these. It calls the controller action controller, and the view action view, the model is active record not action record, active record. So those names were going to become more familiar with as we work with rails. But that's what rails calls its pre-built code to deal with these things, so we're gonna be accessing parts of active record when we want to write things that deal with the model.

实际上rails中有对应的名称。它将controller称为action controller， 将view称为action view，将model称为active record，不是action record，是active record。当我们使用rails时将对这些名称更加熟悉。不过这都是rails用来处理这些事情的内建代码，所以当我们需要写代码来处理model时，我们将存取访问部分active record。

![](/images/post_images/d1bb78c7aae9370a62d4f4f994fab76ff81fa67d.png)

Rails also packages together action controller and action view as Action Pack. So here's the action pack, is just action controller action view have been grouped together as one thing called action pack. So keep this architecture in your mind as well continue to work. We'll come back and look at the diagram again, but it'll help you understand how rails structures things and what it's doing, and most importantly, where we should put our code.

Rails还将动作控制器(action controller)和动作视图(action view)打包为动作包(Action Pack)。这里就是action pack, 它就是把action controller和acton view打包成一个叫action pack的东西。那么在继续工作的同时将这个结构记在脑中。我们后面还会再次看到这个图表，但它将会帮助你理解rails如何构建东西以及它在做什么，还有最重要的，我们该如何放置代码。

![](/images/post_images/89c1b52eb1018cce3136eadc8b800b1c2cd9a380.png)
