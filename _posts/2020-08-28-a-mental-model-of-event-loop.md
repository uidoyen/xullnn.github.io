---
title: "On exploring Promise 1: thoughts about async and event loop model"
categories: [Programming]
tags: [programming]
---

### 1 From `setTimeout` to `Promise`

I remember vividly when I first stumbled on the term "asynchronous", the first thing jumped into my head was it must have something to do with my mobile phone" since I often *synchronize* my phone with my Mac. And our general notion about [sychronization](https://en.wikipedia.org/wiki/Synchronization) is that is a process that coordinates different parts of something in unison. So it's easy to think `async` as "to make things not to happen at the same time", but this is a bit different from what `async` means in web development.

As I kept learning programming, I had more and more terms about sync/async collected in my head, such as "concurrency", "process", "main thread", "promise", "async function". Then I knew I can't jump on a time machine then travel back to the happy days when I just knew about how to use `setTimeout()` and `setInterval()`. And this feeling culminated when I tried to understand how `Promise` works with JavaScript. And I did spend a lot of time trying to understand how `Promise` works. (`Promise` refers to the general concept of promise in this article).

Most learning materials about `Promise` out there focus on how to use `Promise` and how good it is. For people who just learned some basic use of `setTimeout()` and `setInterval()`, as well as some basic use of [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest), when we just got a bit familiar and comfortable with these things. Then lots of people come to say "you know what, we've gotten a better tool to deal with async tasks, it's called `Promise`". After a while some other people tell you "you should try `async function` and `fetch` API, they are promise-based, they are awesome!". As a diligent student, you paid a lot of time reading through materials and going through the code examples again and again. But, you are still not so sure about how to use `Promise` as well as all the promise-based techniques. Why? I think there are several reasons:

- 1. don't have a decent understanding of "async" in head, or think it as a very complicated concept
- 2. don't know how the browsers coordinate sync and async tasks
- 3. people who write introductions about `Promise` assume that all readers have known `1` and `2` mentioned above,  also some key points about `Promise` are not noticed by beginners, therefore some mental gaps persist in the understanding of `Promise`.

I'll try to write a 2-part article to explore these points. The first part will focus on general idea of `async` and mental model of "event loop", the second part will share some key points I realized very late during my journey of learning `Promise`. I don't write too much about "how to use Promise", because there're many excellent materials about this on the internet, I think they do better than me.

### 2 A simple understanding of "async"

In fact it won't be so simple, otherwise I wouldn't have write these things hah. I have to confest that it needs patience to gain a well enough understanding of `Promise`, because it relates to many other concepts. And it's almost impossible to understand a concept without knowing other related ones. As [Daniel Dennett](https://en.wikipedia.org/wiki/Daniel_Dennett) would say:

> "You can't believe a dog has four legs without believing that legs are limbs and four is greater than three, etc."

#### 2.1 A feeling about async

Some of us make breakfast as a daily routine. Although everyone has his/her own preference, but if you don't change your breakfast too often as I do, you may prepare it in a relatively fixed procedure. As a lazy one, most of the time I have these as my breakfast: a cup of drip coffee, 2 boiled eggs, 1 sweet potato. And here're what I need to do(with time consumptions) every morning:

- heat water for brewing coffee (4 mins
- brew drip coffee (3 mins
- boil eggs (10 mins
- heat potato(pre-boiled and frozen) with microwave oven (2 mins

Intuitively we probably won't do these tasks sequentially. For example, when we are boiling eggs, we won't stare at the pot seeing the water boiling gradually and doing nothing else. Thus it may take us `3 + 4 + 10 + 2 = 19` minutes to make the breakfast. We all know we can do something else while we have started some previous tasks, especially some time-consuming tasks. Or we can say some tasks can be handled in parallel. One way to do things in this style may be like this:

```
|-------------- boil eggs --------------|
 |- heat potato -|
  |--- heat water ---|
                      |- brew coffee -|
```

By doing this we can have our breakfast only after 10 minutes. And the purpose of rearranging these tasks is similar to the purpose of "async" in programming.

Let's look 2 description about async:

["asynchronous"](https://developer.mozilla.org/en-US/docs/Glossary/Asynchronous) from MDN:

> Asynchronous software design expands upon the concept by building code that allows a program to ask that a task be performed alongside the original task (or tasks), without stopping to wait for the task to complete.

The definition of "asynchrony" on [wikipedia](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming)):

> Asynchrony, in computer programming, refers to the occurrence of events independent of the main program flow and ways to deal with such events.

No matter it's the "main/original" task or the "other/independent" tasks, they are just some tasks. *And "async" is just the way to coordinate these tasks so that they can be executed correctly and effectively.*

But what's the difference between "sync" and "async"? They are often involved with each other. When we say some code is synchronous we often mean that code is executed immediately and sequentially, without the need for too much coordination. And when we mention "async", it often implies that some "async" tasks deviate from the "sync" part to be execute somewhere else without disturbing "sync" part. But on the other side, there may be "sync" part resides in the "async".

We can first apply an oversimplified view of how sync and async are coordinated. That is, first handle the the sync part, then the async part.

#### 2.2 an oversimplified solution: async goes after sync

A good starting point to develop a realization about the existence of sync and async in JavaScript is the "zero-delay" example with `setTimeout`:

```js
setTimeout(() => {
  console.log("I am the first line.");
}, 0);

// this line is only added for increase the time consumption in between
for(let i = 0; i < 1000000000; i += 1 ) {};

console.log("I am the last line.");
```

If we don't have any notion about async with JavaScript we may expect the two messages to be printed out sequentially, just as the order they were written in code. However in this case, though the time delay of `setTimeout` is set to `0`, plus that we add a time-consuming operation inbetween, but the message in `setTimeout` callback always goes after the `"I am the last line."`. This is a typical example to prove the existence of sync and async parts.

An oversimplified description of how this works is: the async part is executed after the sync part has finished executing. The async part here is the callback passed to `setTimeout`, everything else is the sync part. But who makes the callback asynchronous? It's the `setTimeout` API. `Promise` also, has similar feature. Let's change the code example to use `Promise`:

```js
Promise.resolve("").then(() => console.log("I am the first line."));      // 1

// this line is only added for increase the time consuption in between
for(let i = 0; i < 1000000000; i += 1 ) {};                               // 2

console.log("I am the last line."); // this always gets printed out first // 3
```

The only change made here is the way we wrap the callback. And we get the messages printed out in the same order as the zero-delay one. For now we don't have to worry about what does the `Promise.resolve("")` do, just try to realize there is a distinction between sync and async, and the execution of sync and async code is coordinated in a certain way by the browser. It can be oversimplified as "async goes after sync".

#### 2.3 why the separation of sync and async makes sense

Let's recall the line `for(let i = 0; i < 1000000000; i += 1 ) {};`. This line may take several or more seconds to run in browser, that's why the two messages are both printed out with a short delay. Since sync code is executed sequentially which means lines of code are executed one after another, if there's some code that may take a very long time to finish running, all the code after that will wait for it. If we apply this scenario to the script behind a webpage(or say a tab of the browser), when some sync code is continuously executing, the page just gets stuck and you'll find that you can do nothing with the page, it's just blocked. As we add more `0`s to `i < 1000000000`, the blocking time would increase at a substantial rate. It's just like the example of making breakfast, if all things have to be done one after another, if boiling eggs needed 2 hours, a lot of time could be wasted.

A sensible way is to start off all tasks as soon as possible, then outsource tasks that are time-consuming to somewhere else, just like how we change the way we make breakfast. Now take the code of incrementing `i` from `0` to `1000000000`, we can move it from the sync part to async part to eliminate the blocking experience. We can use `setTimeout` or `Promise` to do this:

```js
Promise.resolve("").then(() => console.log("I am the first line."));      // 1

// we can also do Promise.resolve("").then(() => for(let i = 0; i < 1000000000; i += 1 ) {});
setTimeout(() => { for(let i = 0; i < 1000000000; i += 1 ) {}}, 0);       // 2

console.log("I am the last line."); // this always gets printed out first // 3
```

Now the messages' printing order doesn't change, but the short period time of blocking disappears. Actually it doesn't disappear, it's just moved to the end of the execution. We can prove this by adding 2 or 3 more `0`s to the number then see if the browser is blocked after printing out the two messages.

Many tasks can be time-consuming like the counting number one, others like retrieving data from remote server, processing large amount of data. The separation of sync and async is just really a way to optimize the coordination of different tasks to provide user a smoother experience.

Actually, the separation of sync and async are only made by humans conceptually, in fact they both are just code, a time-consuming calculation can be set to sync, a `console.log()` task can be set to async, it all depends on you, the person who writes the code.

Async code goes after sync code, but how these two parts are coordinated in the browser, how this task is achieved? The answer is the [event loop model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop).

### 3 Mental model of Event Loop -- the mechanism to coordinate sync and async tasks

Imagine `"async code goes after sync code"` is a chunk of code in a function block, such as `function solution() { async code goes after sync code }`. We put this code into a loop, then we get the "Event Loop" such as `while(true) { solution() }`. Of course things are not so simple but it's also not so complicated.

First let's check 2 descriptions about event loop.

According to [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop):

> JavaScript has a concurrency model based on an event loop, which is responsible for executing the code, collecting and processing events, and executing queued sub-tasks. This model is quite different from models in other languages like C and Java.

According to [whatwg](https://html.spec.whatwg.org/dev/webappapis.html#event-loops):

> To coordinate events, user interaction, scripts, rendering, networking, and so forth, user agents must use event loops as described in this section. Each agent has an associated event loop, which is unique to that agent.

Wow these terms are intimidating. I prefer understanding event loop from a more general sense, a good explanation is the video [What the heck is the event loop anyway?](https://www.youtube.com/watch?v=8aGhZQkoFbQ) by Philip Roberts.

Remeber that we can turn a piece of sync code to async by using APIs such as `setTimeout`, often "a piece of code" refers to a callback, which is essentially a function, and inside that function, are the tasks we want run asynchronously. There are different APIs or ways to set tasks as async. Once we know which tasks should be asynchronous, there must be a way to set aside them for later execution.

Different materials about event loop may mention different terms like "stack", "heap", "main thread", "queue", "task queue", "micro-task queue", "macro-task queue", some topics are lower level knowledge in computer science, and it's tempting to dig deeper on these things. But we should realize the "Event Loop" is not some concrete code, it's a mental model, an abstract model, it's written [in the standard](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops), but there doesn't have a single right way to implement it. Of course you can learn about every implementation detail of event loop in one browser like say chrome, but things may be so different when you look into another browser. So what's in common -- is the event loop model. Despite all the different implementation details, the event loop model resides in various browsers operates the same way at a high level. If we understand event loop correctly at a high level, we can confidently predict how the sync and async code will be operated within an app, and write code with confidence.

#### 3.1 Components of event loop

One important thing we need to clarify is the relationship among JavaScript language, the browser, and the event loop. The browser is more than JavaScript language. JavaScript is just a core component of the browser, it's like an engine, actually JavaScript only does synchronous things. The browser actually provides a whole suite of components to maintain an environment for the event loop model to be implemented. Let's zoom in to look at the event loop model.

There are certain components in event loop:

- the main thread/stack: as the word "main" indicates, that's where we run our main tasks, or we can think of it as a place to run sync code
- a task queue: it's a place queued with tasks that are waiting to be executed in the main thread.
- web apis: the tools provided by the browser to schedule tasks sent from the main thread to the task queue

To put these components in operation, the event loop acts as an observer. It keeps an eye on the main thread, if all the tasks there are finished running, it let the oldest(the one that got queued earliest) task in the task queue pop out into the main thread, and then execute it, then the second earliest one, so on and so forth.

Let's review the code example:

```js
setTimeout(() => { console.log("I am the first line.") }, 0);          // 1

for(let i = 0; i < 1000000000; i += 1 ) {};                            // 2

console.log("I am the last line.");                                    // 3
```

Except for the callback passed to `setTimeout` at line 1, all other code is synchronous, which means they be executed first, line by line from top to bottom.

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx9nj4n95j319009mwfu.jpg)

#### 3.2 run, event loop

Imagine that all code, sync and async, is first put in the main thread. When code goes to line 1, `setTimeout` will set the callback aside to a scheduler or say timer, then the code in the main thread goes on executing, when code in the main thread has finished running, the scheduler(timer) starts counting for `0` second, then the callback will be put in the task queue. The work of event loop is to look at the main thread, if all sync code has finished running there, the first(oldest) task got queued in the task queue will be popped out then pushed in the main thread and be executed. And this process keeps running as if it's a "loop".

The event loop model explains why zero-delay callback doesn't have a real zero-delay. Because based on how the event loop operates, the real time delay is the never shorter than the `execution time of the main thread`.

### 4 Summary

Now we know the distinction between sync or async is not made by the code itself. It doesn't make too much sense to say things like "this code is async". "Async" is more of ways to coordinate various tasks, it's more of choices made by programmer.  

Event loop is the model that is used to coordinate sync and async code in browsers. Although we abandoned most implementation details to only keep an abstract mental model, but this model is quite reliable at this stage for us to set off the joureny towards `Promise`. And above the concept of async, Promises are all about making asynchronous code more readable and behave like synchronous code.


































---
title: "On exploring Promise 2: some not-so-obvious but important things about promise"
categories: [Programming]
tags: [programming]
---



### Why promise?

If we have mental model of event loop in our minds, it would be easier to understand promise. But promise brings something new, such as the 3 states of promise, the promise chain, and [a whole set of rules describing how a promise resolves](https://promisesaplus.com/#the-promise-resolution-procedure). And some new techniques such as `async function`, `fetch` API are "promise-based" or at least "promise-related". So if we can't understand promise well enough , we would probably leave a big mental gap there.

I am not trying to write a thorough section about how to use promise, since there're a few excellent articles about this topic. I just focus on some seemingly not-so-important points that may impede the understanding of promise.

### About terms in this article

Based on different context, the word "promise" has different meanings, most of the difference can be distinguished with different writing forms but there're a few subtle ones may not be easily distinguished. In this post "promise" may in the forms of:

- plain lowercase "promise": the general concept of promise
- code quoted lowercase `promise`: an instance of a promise
- code quoted uppercase `Promise`: the `Promise` [constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/Promise)

### use of promise

As I said, this part of work is excellently done by some pros, thank them a lot! Inevitably you may come across some unacquainted terms. You can simply check their definitions on wiki if you want to, but don't go too deep, focus on "how to use promise".

- https://web.dev/promises/
- https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html

### Sense of promise:

I want to start with several different definitions of promise. For now we don't have to understand all the terms before we can continue. Here comes the definitions:

- [Promise/A+](https://promisesaplus.com/): A promise represents the eventual result of an asynchronous operation. The primary way of interacting with a promise is through its then method, which registers callbacks to receive either a promise's eventual value or the reason why the promise cannot be fulfilled.
- [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise): A Promise is a proxy for a value not necessarily known when the promise is created. It allows you to associate handlers with an asynchronous action's eventual success value or failure reason.
- [wikipedia](https://en.wikipedia.org/wiki/Futures_and_promises): In computer science, future, promise, delay, and deferred refer to constructs used for synchronizing program execution in some concurrent programming languages. They describe an object that acts as a proxy for a result that is initially unknown, usually because the computation of its value is not yet complete.

So promise must have something to do with "async", and it's a representation/proxy for a future result. Bringing this high level sense into the exploring of the use of promise is very helpful.

### States of promise

Promise is like a wrapper for asynchronous operations(tasks), and it holds the result of the task and based on how things are going, it stipulates the result can be in one of three states:

- pending: the initial state, means the task is still processing and we don't know how things are going so far
- resolved: means the task is successfully fulfilled, and it may give us something we want such as data or just a message that indicates the task has succeeded.
- rejected: means the task failed, and reasonably a reason(often an error object) should be given to tell what was wrong

Generally speaking, `pending` is when the async operation is still processing, `resolved(fulfilled)` and `rejected` are when the async operation is completed whether succeeded or failed, when a `promise`'s state is `resolved(fulfilled)` or `rejected`, we also say it's settled.

### Some key points that may not be so obvious

#### `Promise()` constructor is used to 'promisifying' something

There's a concise description about the purpose of `Promise` constructor.

> [The Promise constructor is primarily used to wrap functions that do not already support promises.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/Promise)

After reading a lot about how to use promise, we know that `Promise()` can create a `promise` and `then` is the way to chain subsequent operations. But being aware of the original designing purpose is also important, especially when you ask question like "Since `Promise()` and `then()` both return a promise, so what's the difference?"

When you want to create a `promise`, the `Promise` constructor is the first choice, not `then()`.

#### Code in `Promise` executes as soon as the promise is created

I think this is an important fact but most intro level materials don't mention. And this trapped me for a long time when I was trying to figure out how to use promise. "Code in `Promise` executes as soon as the promise is created" is not so obvious for a person who just switches from callback style to promise style.

**the beginning of creation is the beginning of executing**

If we have a function that returns a `promise`:

```js
function makePromise() {
  new Promise((resolve, reject) => {
    // do sync thing one
    // do sync thing two
    // resolve or reject at a certain point
  })
};
```

When you execute `makePromise()`, `thing one` and `thing two` in the callback are beginning execution and are done synchronously immediately. I don't know why I had a tendency(don't know if others have too) to think all the code within the `Promise` constructor only begins executing at the settling point, the point when the `resolve` or `reject` are called. Realiazing this is important for us to manage the sequence of tasks while thinking about possible performance considerations.

**order of creation is not the guarantee of order of completion**

If we have a list of urls `[u1, u2, u3]` that don't depend on each other, means they can be loaded in parallel. But we want to get things from the 3 urls one after another, in the order of `1,2,3` we may write something like this:

```js
function requestURL(url) {
  return new Promise((resolve, reject) => {
    let xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.addEventListener('load', () => {
      let result = xhr.response;
      resolve(result);
    })
  });
};

[u1, u2, u3].forEach(url => {
  requestURL(url)
})
```

Although all the requests may succeed but the order is not guaranteed. Why? Because `forEach` is sync and what we actually did can be seen as:

```js
requestURL(u1);
requestURL(u2);
requestURL(u3);
```

All `promise`s begin creating almost at the same time, meanwhile all code within `Promise` constructor begins executing.

`requestURL` returns a `promise`, but code written in `Promise` constructor won't pause executing. Since a promise chain will be paused by `pending` promises, it's easy to transfer this fact(feeling) to a `Promise` constructor, thinking that the code within the constructor will somehow be paused till the previously created promises is fulfilled. But:

- waiting happens when there's `pending` promise in a chain. You can't just make a `promise` then "pause" it there.
- a `pending` promise never pauses itself. When a promise is created, its original state is `pending`, but from an internal view, `pending` doesn't mean waiting. As long as there are call for `resolve` or `reject` thereafter, a `pending` promise is approaching the state of `fulfilled` or `rejected`.

So creating a bunch of `promise`s doesn't mean the latter ones will wait for the earlier ones, doesn't mean they be executed in the order of creation. Unless you wrap the process of creating promise inside a function(a function returns a `promise`), then arrange them in a chain. There is a big difference between "creating a promise" and "a function that creates a promise". Because when we pass "a function that creates a promise" to `then()`, the creation of promise won't start before the chain advances to the current `then`.

**how to maintain sequence**

How to streamline the requests in a wanted sequence? Also with `forEach`, but this time a bit different.

```js
let chain = Promise.resovle('');
[u1, u2, u3].forEach(url => {
  chain = chain.then(() => requestURL(url));
})
```

Notice `chain.then(() => requestURL(url))` is different from `chain.then(requestURL(url))`,  `requestURL(url)` is a function invocation that will create a `promise` immediately, you should always pass a *function* to `then()`.

#### `resolve` happens immediately

Also this example code:

```js
function fetchURL(url) { // returns promise
  return new Promise((resolve, reject) => {
    let xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.addEventListener('load', () => {
      let suburl = xhr.response[0];
      resolve(suburl);
    })
  });
};
```

This is a tricky point. The `resolve` method don't know how much time a request would take. We call `resolve` in `Promise` constructor, and that happens inside the `'load'` event listener. Here `resolve(suburl)` has no notion about `sync/async` it's called immediately when the request is `'load'`ed, and calling `resolve(suburl)` grants the state `fulfilled` to the promise with `suburl` as its value to prepare for possible future operations. And resolving of a promise is synchronous or say happens instantly.

This may seem obvious after you've noticed it. But realizing this fact can fill some mental gaps while trying to understand the using of promise. Since promise is heavily about "async", it's easy to forget that there're also "sync" things there. It's easy to grumble questions like "how does the promise know when to resolve itself", the answer is it doesn't know. Because the "resolving" moment depends on something else such as explicit writing sync code to resolve `Promise`, like `Promise.resolve()` or call `resolve` in a `Promise` constructor.

To me "`resolve` happens immediately" is a very useful nonsense.

#### Always pass function to `then`

This may seem obvious after we learned about the definition and use of promise. But as days roll on, we may want to stuff anything inside that pair of `parentheses` followed by `then`.One common mistake is forget to pass function to `then()`. Say if we have a function:

```js
function onFulfilled(pre) {
  // do something with `pre`
}
```

And we may write things like:

```js
resolvedPromise.then(onFulfilled("salt"))
```

Inside `( )` the `onFulfilled("salt")` is a function invocation, it's not a function, so what we pass to `then()` is the return value of executing `onFulfilled("salt")`. If `onFulfilled("salt")` returns string "salty cake" it's just like we are doing `resolvedPromise.then("salty cake")`.

[Promise/A+ spec](https://github.com/promises-aplus/promises-spec) also mentions that `then` must return a promise and if `onFulfilled` *is not a function*, a `then` called on a resolved promise must return a new promise resolved with the value of the previous promise. It's better to be expressed by code:

```js
let resolvedPromise = Promise.resolve("One"); // we get a promise resolved with "One"
resolvedPromise.then("two"); // this returns a new promise: `Promise {<fulfilled>: "One"}` resolved with "One" NOT "Two".
```

In fact here what you actually want to do is `resolvedPromise.then(() => onFulfilled("salt"))`.If `onFulfilled` is a function that catches the previous promise' resolved value as its argument, we can write `resolvedPromise.then((pre) => onFulfilled(pre))` or `resolvedPromise.then(onFulfilled)`.

If we make a promise chain with several non-functions inserted like:

```js
resolvedPromise.then(func).then(non-func).then(func).then(non-func)
```

You can imagine that you strikethrough the `.then(non-func)` parts in you mind like:  "promise.then(func)~~.then(non-func)~~.then(func)~~.then(non-func)~~". This is called promise fall through.


#### wait on a promise

Personally I prefer to understand that there're actually two kinds of waiting for a `pending` promise. One is wait from outside, the other is wait from inside". Wait from inside" means inside a `Promise` constructor, after a `promise` is created, it's initially set to `pending`, and then it's waiting to be either fulfilled or rejected. This kind of waiting is often neglected. On the contrary, the waiting made by `then()` is mentioned a lot. And this is "wait from outside". Both kinds of waitings wait a `promise` to transit from `pending` to `fulfilled/rejected`, but they are different. Having a notion of this helped me better understand the states of promise as well as the behavior of a promise chain.

##### how to make a `pending` promise?

This is fun and easy. Remeber I said when trying to create a `promise` always consider `Promise` constructor? So the answer of this is "just make it but don't resolve it". That is:

```js
let pendingPromise = new Promise((resolve, reject) => {}); // Promise {<pending>}
```

##### the pause is "visisble"

Now we have a `pending` promise, let's see how the chain will pause:

```js
pendingPromise.then(() => console.log("Hello World.")); // Not words printed out
            //  ^
            //paused
```

Since `pendingPromise` is at the state of `pending`, the next `then` will wait on it. I often see words like "waiting on a promise", though this is not wrong, but this gives us a sense that where there's is a promise there's is a waiting. But waiting only happens on `pending` promise.

##### `then` only waits on `pending` promises doesn't mean settled ones are skipped

```js
Promise.resolve("one").then(() => Promise.resolve("two")); // Promise {<fulfilled>: "two"}
Promise.resolve("one").then(() => Promise.resolve("two")).then(() => Promise.resolve("three")); // Promise {<fulfilled>: "three"}
```

Here we start with a `promise` resolved with `"one"`. By the chainning of `then()`, resolved values are updated sequentially. Although none of the `promise`s went through the transition from `pending` to `resolved`, no `promise` is skipped.

##### Nurture intimacy with standard

If you've ever explored some articles about promise, you may have been introduced with the [Promise/A+](https://promisesaplus.com/) standard, as it states, it's:

> An open standard for sound, interoperable JavaScript promises—by implementers, for implementers.

And as we roll down the page, there are just several sections of structured rules. So promise is more of a high level model, it's not a package of code. The rules describe how to implement promise, but there doesn't exist a single right way to implement it. This is very similar to what we talk about the mental model of event loop. Next time when we are confused with code with promise, we can check the standard. Actually if you have known the 3 states of promise and how these states are achieved, reading the standard is easier than going through a bunch of code examples. It's really boring, but it's also very reliable.

// ---------------------------------------------------------------------------------------------------------------------------------

### Summary

The purpose of this article is just to point out some points that may impede our understanding of promise. These points may first sound so obvious, but they may be obvious to pros. This is more of a communication gap or mismatch. Hope this can have a little help along your journey with promise.

// ---------------------------------------------------------------------------------------------------------------------------------

*References:*

https://web.dev/promises/

https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html

https://en.wikipedia.org/wiki/Futures_and_promises

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/Promise

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

https://promisesaplus.com/

https://stackoverflow.com/questions/31324110/why-does-the-promise-constructor-require-a-function-that-calls-resolve-when-co

https://stackoverflow.com/questions/22519784/how-do-i-convert-an-existing-callback-api-to-promises

> The Promise constructor is only used for promisifying1 asynchronous functions.

> then basically offers the ability to attach onFulfilled/onRejected callbacks to an existing promise
