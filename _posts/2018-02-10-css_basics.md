---
title:  "Cascading Style Sheets - CSS Basics"
categories: [Front end ℗]
tags: [Front end]
---

**Core conceptions**

- selector
  - type(tag,element) selector
  - class selector
  - id selector
  - attribute select
    - exmaple: `img[src]` Selects `<img src="myimage.png">` but not all `<img>`
  - pseudo-class selector
    - example: `a:hover` Selects `<a>`, but only when the mouse pointer is hovering over the link.


![](https://mdn.mozillademos.org/files/9461/css-declaration-small.png)

image from [MDN](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)


- properties & property values


- CSS box model
  - padding
  - border
  - margin

![](https://mdn.mozillademos.org/files/9443/box-model.png)

image from [MDN](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)

- media queries


- priority level

---

**Basic behaviors**

- selector 选中具体的(一个或多个)元素，通过property(和 property value)给元素添加样式或改变元素在页面中的布局。

- media query 用来区分不同屏幕大小的设备上，具体应用哪一部分的css样式。

---

#### 关于value单位之间的区分


##### px

pixel 作为单位从语义上理解是绝对的，但实际并非如此

Pixels (px) are **relative to the viewing device**. For low-dpi devices, 1px is one device pixel (dot) of the display. For printers and high resolution screens 1px implies multiple device pixels.

However, for printersand high-resolution screens, one CSS pixel implies multiple device pixels, so that the number of pixels per inch stays around 96.

当在 低分辨率设备上是 1 px 对等 1 pixel 但是在高分辨率设备中 1px 会对应更多的物理像素。总的原则是使得 1 英寸屏幕长度上 大约分配给 96 px (css unit)
反过来这样理解  1 inch(2.54cm) 长度的屏幕上的总实际像素  除以  96  等于 css中1px所占用的实际物理像素点数

应用总应该避免使用 px 这类绝对（虽然也算不上绝对的绝对）单位，因为这会导致用户无法放大字体

Defining font sizes in pixel is **not accessible, because the user cannot change the font size from the browser**. For example, users with limited vision may wish to set the font size much larger than the size chosen by a web designer. Avoid using pixels for font sizes if you wish to create an inclusive design.

##### 百分号 `%`

用百分比作为 css 的单位时， 这里的百分比是相对于  parent 而言的，而不是整个 <body> 或者是浏览器宽度，这样在层级上分得更清楚

在设置字体大小时 若用 150% 也是相对 上一级元素字体大小而言，通常不会给每个元素都指定字体，那么就一直向上追溯 直到 body 层

##### em

1 em 并不一定指代多少 px ，同样是相对于 parent 的字体而言， parent 字体是 25px 那么 children 的 1em 就是 25px , 如果parent 是 48px 那么 children 的 1em 就是 48px

对于多数 浏览器 默认字体是 16px

Another way of setting the font size is with em values. The size of an em value is dynamic. When defining the font-sizeproperty, an em is **equal to the size of the font that applies to the parent of the element in question.** If you haven't set the font size anywhere on the page, then it is the browser default, which is often 16px. So, by default 1em = 16px, and 2em = 32px. If you set a font-size of 20px on the body element, then 1em = 20px and 2em = 40px. Note that the value 2 is essentially a multiplier of the current em size.

##### rem

正因为 em 参照 parent 的字体大小，所以改变字体前一定要看好 元素的层级关系
如果想要所有字体都参照 最顶层字体大小 使用 rem  r 指代 root
当然也可以两种混起来使用

rem values were invented in order to sidestep the compounding problem. rem values are **relative to the root html element, not the parent element.** In other words, it lets you specify a font size in a relative fashion without being affected by the size of the parent, thereby eliminating compounding.

##### vh 和其他单位

 `vh`（vertical height）作为尺度单位

Equal to 1% of the height of the viewport's initial containing block.

1 vh 视窗初始高度的百分之一

[MDN列出的其他一些常用单位](https://developer.mozilla.org/en-US/docs/Web/CSS/length)


---

#### 关于 media query

`@media query`中的
- min-width: n px 指定当宽度 大于n px 时的情况

- max-width: n px 指定当宽度小于 n px 时的情况

功用 和 语义 相反

---

### Tips

---

#### 关于 字体行高 line-height

设置行高 line-height 的时候最好使用 没有单位的 **纯数字** 比如 1.5

line-height: 1.5;

这样行高会被设置为 该元素 的 字体大小的 1.5倍， 在跨设备显示时，这可以更好的保持一致性

---

#### 关于 padding 和 margin 的百分值

在设置 padding 或者 margin 时 如果使用百分比，比如 `padding: 10% 30%;`

这里的10% 30%是相对于该元素的 **水平宽度** 的百分比

---

#### 关于 border

border 有三个维度的属性

- width 宽度
- style 风格
- color 颜色

由于 width 也属于边框属性的一类，它同样适用于 margin 或 padding 中使用的 顺时针 设置规则
比如 border-width: 10px 18px；会指定上下边10px 左右边 18px

可以使用 border: 15px dotted orange; 这种简洁的方式来设置边框属性

---

#### 关于 box-sizing 属性

当使用 width 或 height 属性的时候 指定的仅仅是 content area 的尺寸，也就是box-model最内部范围。而不是包含 padding + border + margin 的尺寸，要注意这一点，比如指定某个元素 width 为100%（content eara的宽度会是父元素宽度的100%），而后再加上20px的 padding，那么加上的这20px不是向内扣除 content-box 尺寸的，而是基于content-box向外加。

这会导致其宽度超过父元素宽度而冲出边界

此时一般可以配合使用 box-sizing: border-box 属性 使你指定的 width 或者 height 包含 padding 和 border

实际使用中可以使用 universal selector 将这个属性设置给所有元素

```css
  * {
	box-sizing: border-box;
}
```

[参考 MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)

border-box 意思是指将 border 以内的内容（包含 border, padding , content area）作为边界

**默认情况下 这个值被设置为 content-box 也就是基于最内层**

The CSS box-sizing property is used to alter the default CSS box model used to calculate width and height of the elements.
Some experts recommend that web developers should consider routinely applying box-sizing: border-box to all elements.

---

#### 关于 max-width 属性

注意和 media query 中的 max-width 区别。这里提到的是针对特定element的属性。

- [element 的max-width属性](https://developer.mozilla.org/en-US/docs/Web/CSS/max-width)
- [media query中的max-width](https://developer.mozilla.org/en-US/docs/Web/CSS/@viewport/max-width
)

如果某个元素的宽度被设置为 80%(假设相对于`<body>`宽度)

在横向拉伸浏览器时 此元素会一直保持指定的80% 宽度最后变得很宽 最终破坏视觉上的美感
这便是 `max-width: 960px` 发挥用处的地方

当屏幕被缩小的时候 此元素会保持80%的比例，但是当屏幕横向拉伸直至它的宽度到达960px后，这个元素的宽度将不再变化

---

#### 关于 image 的尺寸


对于 图片 `<img>`来说，如果不指定宽度那么它一般保持其原本的尺寸，

在宽屏幕上他可能会没能占满容器宽度而导致视觉上的“缺损”

在窄屏幕上他有可能由于太宽而撑开太多空间导致出现“横向的滑动条”

这种情况下一个很好的实践是给 img 设置 `width: 100%`

```css
.some-image {
  width: 100%;
}
```

注意这个**比例是针对父元素而言的**，而不是整个视窗宽度，这样无论如何调整视窗大小，图片大小都会与容器宽度保持一致

设置背景图片的时候，可以先在下面铺一层 background-color **作为保护层**，这样当图片坏掉或者被禁用的时候页面不会太丑

设置 `background-size: cover;` 使背景图片自动调整以覆盖指定范围

在设置封面或者，指定容器的背景图片时可以使用，如果调整视窗过程中垂直方向出现空白，可以给 img 加上 height: 100% 属性

`background-size: cover;` Scales the image as large as possible without stretching the image. If the proportions of the image differ from the element, it is cropped either vertically or horizontally so that no empty space remains.

[MDN关于background-size的内容](https://developer.mozilla.org/en-US/docs/Web/CSS/background-size)

---

#### 关于 列表list 的表现形式

- `<ul>` 前面是小点
- `<ol>` 前面是数字`1.` `2.`

- list-style-type 可以改变列表item前的标记符号，disc是黑色小圆点，circle是空心小圆点，square是黑色小方块，decimal-leading-zero是带0的数字，lower-roman是小写罗马数字...

https://developer.mozilla.org/en-US/docs/Web/CSS/list-style-type

—

list-style-position 改变前缀标记的**从属范围** 是属于li的一部分还是独立于li item之外。

```css
ul {
	padding-left: 0;
	margin-left: 0;
}
```

可以除去列表左边的空白

---

#### 关于 text-shadow 文字阴影

文字阴影 text-shadow 可以接受4个参数  

- 第一个是 水平方向的阴影偏移距离
- 第二个是 垂直方向的阴影偏移距离
- 第三个是 阴影边缘的模糊程度
- 第四个是 阴影颜色

```css
/* offset-x | offset-y | blur-radius | color */
#some_text {
text-shadow: 1px 1px 2px rgb(0,0,0, .8);
}
```

---

#### 关于 [border-radius](https://developer.mozilla.org/en-US/docs/Web/CSS/border-radius) 设置圆角

The border-radius CSS property lets you **round the corners of an element's outer border edge**. You can specify a single radius to make circular corners, or two radii to make elliptical corners.

- 使用 border-radius 设置圆角 使用极端的参数可能会有戏剧性的效果

下面就是将 top-left 和 bottom-right 的圆角长设置为 100% 的效果 这里的100%是相对于该元素的 box model 的宽度而言的

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fpbalhnm0dj31j40kqh26.jpg)

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fpbalwn459j31gk0kbdyw.jpg)


- 当 box model 的高度和宽度一致时  50% 的 border-radius 则呈现圆形，可以用来显示头像

---

#### 关于 [gradient](https://developer.mozilla.org/en-US/docs/Web/CSS/gradient) 渐变属性

指定两个端点颜色，以及渐变 位置 或者 方向，可以创造出不同的渐变效果 具体参考 api

- example 1
```css
.some-image {
  background-image: linear-gradient(45deg, #ffa949, firebrick);
}
```
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fpbao03gq9j31kw0mi4qp.jpg)

- example 2
```css
.some-image {
  background-image: radial-gradient(circle, #ffa949, firebrick);
}
```
![](https://ws4.sinaimg.cn/large/006tKfTcgy1fpbaq32esdj31kw0m77wh.jpg)

**甚至可以设置多个 色彩锚点，css 中叫 color stops**

- example 3

```css
.some-image {
  background-image: radial-gradient(circle at top right, #ffa949, firebrick, dodgerblue);
}
```

上面从给出了三个颜色`#ffa949, firebrick, dodgerblue`

![](https://ws4.sinaimg.cn/large/006tKfTcgy1fpbat1ezu2j31kw0ks7wh.jpg)

**可以通过给每个颜色设置相对位置 来约束每种颜色的延伸度和不同色彩的占比**

example 4

```css
.some-image {
  background-image: radial-gradient(circle at top right, #ffa949 0%, firebrick 30%, dodgerblue 100%);
}
```

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fpbav8r0wkj31kw0jv4qp.jpg)

---

#### 关于图片的垂直层级

跟 ps 的图层一样 css 的 background-image 也是可以多层堆叠的

图层的显示顺序和字面上的顺序一致，压在最下面的一层写在最后

不同层之间使用 **逗号** 分隔

```css
.some-image {
  background:
              linear-gradient(#ffa949, transparent 90%),
              #ffa949 url('../img/mountains.jpg') no-repeat center;
}
```

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fpbaxgxx6tj31kw0mhkjl.jpg)

---
