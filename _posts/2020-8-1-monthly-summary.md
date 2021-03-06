---
title:  "Monthly summary 2020-8"
categories: [Learning]
tags: [Notes]
---

### javascripting.com 一个查询js相关库，框架和插件的网站

同时显示某个库当前的流行程度，在学习相关技术前应该查询哪个框架或者库是当前最流行或广泛使用的，再花时间学习。

- https://www.javascripting.com/

> The definitive source of the best
JavaScript libraries, frameworks, and plugins.


### Modernizr 一个feature detection工具，可以测试用户端是否支持特定功能

- https://modernizr.com/

> Modernizr tells you what HTML, CSS and JavaScript features the user’s browser has to offer.

### draw.io 之前用过的一个在线免费 flowchart 工具，现在推出了desktop版

Github主地址

- https://github.com/jgraph


### Google Developer 上介绍浏览器工作原理的系列文章

- https://developers.google.com/web/updates/2018/09/inside-browser-part1


### 一个介绍javascript中 Module Design Pattern 的视频

这里的 `Module` 指的不是某些语言中引入module的那个module, 指的是利用javascript中的scope规则和closure机制，使用function创造一个相对独立的scope来定义private states和behavior，同时避免与外层scope相互干扰，最后以return object 的方式(maybe with use of IIFE)暴露部分接口来access内部states或behavior.

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures
- https://www.youtube.com/watch?v=SKBmJ9P6OAk

### stackoverflow 上关于如何上手学习 Node.js 的答案

Node.js 为javascript 提供了除浏览器以外的更广阔的应用场景。

- https://stackoverflow.com/questions/2353818/how-do-i-get-started-with-node-js/5511507#5511507

### Mozilla 上一篇详细介绍javascript的object model的文章

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Details_of_the_Object_Model

### raster image 位图 和 vector image 向量图 的区别

- https://www.youtube.com/watch?v=-Fs2t6P5AjY

### 一篇关于在 promise 和 async/await 之间取舍的文章

主旨`async/await`能更容易处理stacktrace而不用像promise chain 那样传递记录stacktrace，因此`async/await`更加节约内存。作者推荐使用 `async/await`

> Compared to using promises directly, not only can async and await make code more readable for developers — they enable some interesting optimizations in JavaScript engines, too!

- https://mathiasbynens.be/notes/async-stack-traces

### 关于 JavaScript 中 `Promise` 的implementation标准

通常称作 `Promise/A+`。 可以看到很多篇幅都用于`then`的部分，其中的逻辑有点绕，需要慢慢看，慢慢想。

- https://promisesaplus.com/

### 一篇关于 `Promise` 这个 Constructor 中所包含的模式的文章 -- The Revealing Constructor Pattern

首先这篇文章是在 [we have a problem with promise](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html) 中被提到的。

文章中提到 `Promise` 的标准构造方式也就是用 Constructor:

```js
let promise = new Promise(function(resolve, reject) => {
  // do something then resolve/reject
})
```

`Promise` constructor 接收一个callback作为argument, 这个callkback function又接收两个arguments通常命名为`resolve` 和 `reject`(也可以使用其他名称)。这两个 callback arguments 可以决定新构建的这个`Promise` object的内部状态(state)，而一旦这个`Promise` object的构造执行完成，我们就无法再使用`resolve`或者`reject`来篡改其状态，只能使用`then`来连接后续操作。

> resolve and reject. These arguments have the capability to manipulate the internal state of the newly-constructed Promise instance p.

作者称这个模式为 "The Revealing Constructor Pattern" 是因为 `Promise` constructor 揭示了其内部功能(capabilities), 但仅限于实例的构造过程。

> I call this the revealing constructor pattern because the Promise constructor is revealing its internal capabilities, but only to the code that constructs the promise in question. The ability to resolve or reject the promise is only revealed to the constructing code, and is crucially not revealed to anyone using the promise.

地址：
- https://blog.domenic.me/the-revealing-constructor-pattern/

```js
Promise.resolve("Ohoooo!").then((a) => return a).then("guess what")
```

### Google 上关于web开发的资源网站

- web fundamentals 部分包含大量web开发的基础以及进阶内容介绍， 文章专业性较强，但慢下来研读收获很多
  - https://developers.google.com/web/fundamentals

- Progressive Web Apps Training 教程，内容覆盖全，但正在改版中
  - https://developers.google.com/web/ilt/pwa
  - This course shows you how to convert web pages to PWAs. A PWA is not an API or a technology, but it is a web development approach that uses a combination of tools and technologies already available to create targeted, ideal user experiences. It shows how to use service workers, APIs, and an application shell architecture for meaningful offline experiences, fast first load, and easy user reengagement upon repeat visits.


### Event, Event Listener, Event Handler 的区别

- Event 比较好理解，直接指代某个事件
- 比较容易弄混的是 Event Handler, 准确的说指的是事件触发的那个 callback
- Event Listener 有时和 Event Handler 互换，但是准确的说 Listener 包含事件监听的那部分，而 Handler 只是callback它不关心是什么事件触发了它。

- https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events

> Each available event has an event handler, which is a block of code (usually a JavaScript function that you as a programmer create) that runs when the event fires. When such a block of code is defined to run in response to an event, we say we are registering an event handler. Note: Event handlers are sometimes called event listeners — they are pretty much interchangeable for our purposes, although strictly speaking, they work together. The listener listens out for the event happening, and the handler is the code that is run in response to it happening.
