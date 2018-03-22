---
title:  "Rubyist notes readme"
categories: [Ruby/Rails ℗]
tags: [Ruby & Rails, Notes of Rubyist]
---

#### 这是我读《Rubyist》时做的笔记的整理稿。

原本只是想借这本书熟悉 ruby 中 regular expression 的用法，结果竟读完了整本书，也意外地成为了我读的第一本英文原版书。得益于作者优秀的写作水平和他对Ruby深厚的理解，这本书读起来并不会很吃力。

书中所有代码示例我都跟着做了一遍，有一些内容由于 Ruby 版本更新（看这本书的时候已经是2.5.0）会有所出入。起初所有代码示例都是截图，整理过程中会借 markdown 语法把示例尽量还原成文字。笔记并不会按照目录分级严格展开并覆盖到每一个末级标题，只会在大体内容上保持顺序的一致。

**原书名：The Well-Grounded Rubyist - second edition**

**原书作者：David A. Black**

书的封面：

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/rubyist_cover.png
)

---

书的目录：

## 1. Ruby foundations

### Chapter 1. Bootstrapping your Ruby literacy

### Chapter 2. Objects, methods, and local variables

### Chapter 3. Organizing objects with classes

### Chapter 4. Modules and program organization

### Chapter 5. The default object (self), scope, and visibility

### Chapter 6. Control-flow techniques

## 2. Built-in classes and modules

### Chapter 7. Built-in essentials

### Chapter 8. Strings, symbols, and other scalar objects

### Chapter 9. Collection and container objects

### Chapter 10. Collections central: Enumerable and Enumerator

### Chapter 11. Regular expressions and regexp-based string operations

### Chapter 12. File and I/O operations

## 3. Ruby dynamics

### Chapter 13. Object individuation

### Chapter 14. Callable and runnable objects

### Chapter 15. Callbacks, hooks, and runtime introspection

---

作者在一次演讲中总结出了7个关于ruby的核心内容

https://www.youtube.com/watch?v=Xt6c6r4fbBs

- Every expression evaluates to an object

- It's all about sending message to objects

- Objects resolve message into methods

- Classes and modules are objects

- There's always `self`

- Variables contain reference to objects

- `true` and `false` are objects; `true` and `false` are states

这些核心观念在书中随处可见，也是理解Ruby的重要锚点。
