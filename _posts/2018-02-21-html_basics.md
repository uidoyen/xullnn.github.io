---
title:  "HTML Basics"
categories: [Front end ℗]
tags: [Front end]
---

**Core conceptions**

- HTML(Hypertext Markup Language) is a markup language the browser uses to present information to users, like text, links, images, and videos. It's the basic component from which all websites and applications on the web are built.

HTML 是__浏览器__用来标记和呈现内容给用户的一种标记语言(markup language)。

 - 浏览器网页可拆分出的三个层面
   - content layer - HTML标记的内容层面
   - presentation layer - CSS修饰的表现层面
   - behavior layer - Js控制的行为层，也可以理解为interactive层级


 ![](https://ws4.sinaimg.cn/large/006tKfTcgy1fpax2jgesdj30l50bkdj4.jpg)

- HTML elements - 被 html tag 对标记的一段内容，被视作一个element
  - [Inline elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements)
  - [Block-level elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements)


---

**Basic behaviors**

HTML 使用 tag 标记特定内容，并以结构化方式搭建起网页的DOM(Document Object Model)结构，成为css和js工作的基础。

**Discrimination of terms**

- tags 指所有tags
- [semantic](https://developer.mozilla.org/en-US/docs/Glossary/Semantics) tags 指那些并不具有实际(或明显)修改内容形式的标签，但这些标签在语义上很直白，他们的主要功能就是以更容易识别(understandable,logical)和记忆的方式标记某些内容。比如 `<header>, <footer>, <section>, <aside>...`

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fpayqps47dj30ky0bqdhc.jpg)
- attributes - 注意属性和 css 是不同的概念
  - All HTML elements can have attributes 所有元素都可以有属性
  - Attributes provide additional information about an element 属性在html层面提供一些关于该元素的额外信息，也可能部分改变元素的呈现形式
  - Attributes are always specified in the start tag 属性都在起始标签里
  - Attributes usually come in name/value pairs like: name="value" 属性通常以键值对方式出现

---

**HTML 页面的 global structure**

这是一个典型的 html 页面宏观结构

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My test page</title>
  </head>
  <body>
    <p>This is my page</p>
  </body>
</html>
```

从内部看分成了 `<head></head>` 和 `<body></body>` 两部分

[What’s in the head? Metadata in HTML](What’s in the head? Metadata in HTML)

- head 标签

  The head of an HTML document is the part that is not displayed in the web browser when the page is loaded. It contains information such as the page `<title>`, links to CSS (if you want to style your HTML content with CSS), links to custom favicons, and other metadata (data about the HTML, such as who wrote it, and important keywords that describe the document.)

  head 标签并不会实际出现在网页上。他内部包含的是页面的标题 `<title>`， CSS 和 js 文件的来源以及 metadata 等信息。

- metadata

  Data about the HTML, such as who wrote it, and important keywords that describe the document.

  关于HTML页面的一些配置信息，或理解为元信息。比如这个页面谁写的，描述这个页面的关键词（搜索引擎可能会用到），页面字符使用的编码标准（比如`<meta charset="utf-8">`）等。

---

**[main 标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main)**

`<main></main>` 用来标记一个网页中的主要内容，如果需要，可以在一个页面中使用多个 `<main>` 元素。

---

**[div 标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div)**

`<div></div>` 可以视作一个纯净的容器，在添加任何css样式前他不会给内容带来任何形式的改变。

---

**[header](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/header) 和 [footer](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/footer) 标签**

header 和 footer 的作用是用来包裹一节内容的题目部分信息和注脚部分信息。他们可以在一个页面中出现多次，并不限于标记整个页面。

---

**页面内分段导航**

- 用id属性跳转
 - 1 给指定内容主体设置id作为锚点
 - 2 给跳转链接设置 `href` 指向目标id元素

example:

```html
<p>This is a <a href="#sample">sample</a> paragraph.</p>
.
.
.
<section id="sample">
  <div>...</div>
</section>
```

点击段落中的 sample 链接就或跳转到对应id的section

**Back to top**

使用 `#` 作为目标id

```html
<p><a href="#">Back to top</a></p>
```

---

**HTML内容中的特殊字符溢出**

通常使用 `&commat;` 这样的语法来表示

[特殊字符对照表](https://dev.w3.org/html5/html-author/charref)

![](https://ws1.sinaimg.cn/large/006tKfTcgy1fpb2lwo32aj30vx0g0764.jpg)

---

**[pre 标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/pre)**

The HTML `<pre>` element represents preformatted text which is to be presented exactly as written in the HTML file. The text is typically rendered using a non-proportional ("monospace") font. Whitespace inside this element is displayed as written.

`<pre></pre>` 容器内写什么就呈现什么，不受html规则约束，比如空格的数量不会被合并，html tag 也不会解析。


---


**[form 标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)**

from MDN:
> The HTML `<form>` element represents a document section that contains interactive controls for submitting information to a web server.

from w3s:
>The HTML `<form>` element defines a form that is used to collect user input.

`<form></form>` 用来标记会提交数据给 server 端的小节。

- form 中的 action 属性规定了数据提交到服务器端的具体地址
- form 中的 method 属性规定了数据提交到服务器端使用的是 get 还是

-

**[The `<input>` Element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)**

The `<input>` element is the most important form element.

The `<input>` element can be displayed in several ways, depending on the type attribute.

在 `<form></form>` 中，最重要的是 input 标签，这是定义输入区域的元素，其中 type 属性可以约束提交信息的形式，比如输入框，下拉选单，勾选选项，文件上传等

- input 标签的attributes定义了数据输入的形式

`<input>` type 属性
```html
<input type="button">
<input type="checkbox">
<input type="color">
<input type="date">
<input type="datetime">
<input type="datetime-local">
<input type="email">
<input type="file">
<input type="hidden">
<input type="image">
<input type="month">
<input type="number">
<input type="password">
<input type="radio">
<input type="range">
<input type="reset">
<input type="search">
<input type="submit">
<input type="tel">
<input type="text">
<input type="time">
<input type="url">
<input type="week">
```

默认情况 input 会是一个空白输入框 <input>

- input 必须有一个 name 属性，比如 name="age"，用来指明这个输入框提交数据的key，类似hash中的key

- input 的value属性可以设置提交数据的默认值（可选）

综合来看，form标签中的action属性可以视作rails中的route指明数据送到哪个controller的哪个method， method 属性指明method的htttp verb类型，一个 input 中的name对应提交数据中的一个 key，一个 value 则是对应key的值，注意value可以是默认的或是用户填写后submit的值。
