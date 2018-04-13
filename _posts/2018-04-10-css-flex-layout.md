---
title:  "CSS flexbox layout system"
categories: [Front end ℗]
tags: [Front end]
---

### 1 What is flex layout system?

> Flexbox provides tools to allow rapid creation of complex, flexible layouts, and features that historically proved difficult with CSS.  --from MDN

Flexbox 提供工具让我们能快速建立起复杂而灵活的排版布局，而实现这一目的对以往的css来说是困难的。

### 2 Why Flexbox?

https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox#Why_Flexbox
>For a long time, the only reliable cross browser-compatible tools available for creating CSS layouts were things like floats and positioning. These are fine and they work, but in some ways they are also rather limiting and frustrating.  
长久以来，能够跨浏览器兼容的，用来建立css排版的工具只有两个： `floats` 和 `positioning`。它们可以奏效但在某些地方他们会收到限制或让使用者感到挫败。

>The following simple layout requirements are either difficult or impossible to achieve with such tools, in any kind of convenient, flexible way:   
以灵活的方式实现下面这些简单的排版需求对于传统css来说要么是不可能的，要么是很难实现的：       

>- Vertically centering a block of content inside its parent.    
让一个块级元素在它的parent容器中垂直居中。
- Making all the children of a container take up an equal amount of the available - width/height, regardless of how much width/height is available.       
让一个容器中的所有子元素占据同等大小的长度或宽度，同时不用理会具体的 width/height 数值。
- Making all columns in a multiple column layout adopt the same height even if they contain a different amount of content.   
当有多个columns存在，并且每个column中的内容长度不相等时，让所有columns保持等高。

**css flex layout system 最小构成单位**

- 1 flex container 即flex外层容器
- 2 flex item 容器内部可应用规则的子元素

1包含2， flex container 并不是一个 `.class`，他是 `display`这个property的value--`display: flex;`。 他可以是任意 block level 或者 inline level 的元素

一个 flex container 中可以包含无数个 flex items

**css flex layout system 计算基础**

- 外层容器的总宽度
- 内层子元素的自身宽度，设置`flex-basic`属性后的基础宽度

二者的差值用来分配 flex-grow 和 flex-shrink 的比例


 ### How it works

 Basic Model

 ![](https://mdn.mozillademos.org/files/3739/flex_terms.png)

 - flex 容器对内部元素的控制分两个方向，横向 **Main Axis** 和 纵向 **Cross Axis**
 - 容器内壁上边缘定义为 cross start 点， 对应的下边缘定义为 cross end 点。横向上则是左边为 main start， 右边是 main end。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2017-11-01+%E4%B8%8B%E5%8D%883.29.17.png
)

---


### 3 具体使用


案例准备：

案例在Rails中展示，所以没有引入stylesheets的代码。

html页面由一个简单的div包裹6个子元素

```html
<div class="container">
  <div class="item-1">Item1</div>
  <div class="item-2">Item2</div>
  <div class="item-3">Item3</div>
  <div class="item-4">Item4</div>
  <div class="item-5">Item5</div>
  <div class="item-6">Item6</div>
</div>
```

css 加上了简单的样式

```css
html, body {
  margin: 15px;
}

.container {
  padding: 10px;
  text-align: center;
  font-size: 1.5em;
  background-color: #ffeead;
  border: 5px solid #ffcc5c;
}

.item {
  color: white;
  background-color: tomato;
  margin: 5px;
  padding: 8px 12px;
  border-radius: 5px;
}
```

初始效果:

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%888.43.10.png
)

*所有截图演示基于 iPad Pro 分辨率， 1024 x 1366*

---

#### 3.1 首先要指定外层容器

外层容器是 `<div class="container">`

现在给 `.container` 加上新的 property/value 对： `display: flex;`

```css
.container {
  /* other properties */
  display: flex;
}
```

container中的子元素排布立即由垂直堆叠变为横向排布，并且默认居左对齐。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%888.49.58.png
)

另一种情况是将外层容器设为 `display:inline-flex;`

这会使外层容器具有inline特性，横向上，只在视窗中占据适应内容宽度的空间。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%888.56.07.png
)

#### 3.2 确定堆叠轴向

默认情况下是横向堆叠，如果写出来就是 `flex-direction: row;`。如果想要纵向堆叠则是 `flex-direction: column;` 效果会和初始效果相同，所有子元素纵向堆叠占满容器。

#### 3.3 确定堆叠顺序

可以使用 `column-reverse` 或 `row-reverse` 来反转排列顺序。

---

#### 4 横向排布时内部元素的操控

##### 4.1 使用 `flex-wrap: wrap;` 确保过多的子元素不会撑破外层容器。

首先尝试加更多的子元素。目前有6个，比如加到12个。从9开始的元素就撑破了外层flex容器。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%889.10.16.png
)

加上`flex-wrap: wrap;`后

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%889.13.16.png
)

默认情况下，flexbox 是不允许内部元素进行换行的。除非加上了 `flex-wrap: wrap;`。后面的例子没有特殊说明都会是带有`flex-wrap: wrap;`设置的。

##### 4.2 统一内部子元素的宽度基数

上面例子中由于内容更多的子元素会比前面的宽，会导致宽度的不一致。

可以使用 `flex: 100px;` 或 `flex: 15%;` 指定子元素的宽度基数。注意这个propertie是用在子元素身上的。**准确的写法是 `flex-basic: x`。这个基数的作用不仅仅是用来指定宽度，也是为后面计算剩余空间或不足空间的时候的基础。**

**如果把flex设为 200px**

```css
.container > * {
/* other properties */
  flex: 200px;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%889.35.04.png
)

如果给 `150px`

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%889.36.20.png
)

如果给 `1px`！这里并没有出现特别的情况，如果给出的最小尺寸小于了元素默认的尺寸，那么子元素也仍然会以最小适应宽度自动排列整齐。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%8810.50.09.png
)

这里的基本规则是，尽量先约束靠前的子元素，让其接近给出的最小宽度值。如果后面留有少量元素会自动拉伸填满一行。

##### 4.3 `flex-direction` + `flex-wrap` = `flex-row`

 `flex-direction` 和 `flex-wrap` 可以合并为一个 `flex-row` 属性，给出值的顺序是先 direction 后 wrap

 `flex-flow: row wrap;`

##### 4.4 改变元素占领剩余行宽的比例

先将例子中的后面6个子元素剔除，恢复到左对齐的状态。

并去掉css中子元素的 flex 属性。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%888.49.58.png
)

前面给子元素加上的 `flex:` 属性的值是带有单位的，指定他们的最小宽度。`flex:` 属性还有另外一个用法，那就是不带单位的。**更精确的写法是`flex-grow: x`**

比如 `flex: 1;` `flex: 3` 等。

**不带单位的值代表的是比例。这个比例是针对容器中剩余空间的，在这个案例中就是右边的空位。来看下不同单位针对不同元素的含义。**

**对所有元素统一设置：**

- 给所有元素设置 'flex: 1;'，含义是，每一个元素占领剩余空间的比例是`1:1:1:1:1:1`实际效果就是每个元素宽度一样，并占满容器宽度。
- 给所有元素设置 'flex: 2;'，含义是，每一个元素占领剩余空间的比例是`2:2:2:2:2:2` 实际效果还是每个元素宽度一样，并占满容器宽度。

**对单个元素进行设置：**

比如对 item6 设置 `flex: 1;`

```css
.item-6 {
  flex: 1;
}
```
![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%8811.04.54.png)

可以看到前面5个没变，item6拿到了所有剩余空间。语义上就是每一个元素占领剩余空间的比例是`0:0:0:0:0:1` ，也就是 item6 没有竞争对手，拿到所有剩余空间。那么这里单个元素无论是多少其实是没有影响的，因为其他元素都不参与。

**分别对多个元素设置**

```css
.item-1 {
  flex: 1;
}

.item-2 {
  flex: 2;
}

.item-3 {
  flex: 3;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%8811.16.51.png
)

对item1,2,3分别设置了 `flex:` 1,2,3。

那么就是 item1, item2, item3 分别拿到剩余空间的比例是 1:2:3

注意这里每个元素拿到的都是增量，不一定他们的宽度比会刚好是 123。

可以用一个极端的例子说明，比如给 item3 设置改为 `flex: 9999;`。

```css
.item-1 {
  flex: 1;
}

.item-2 {
  flex: 2;
}

.item-3 {
  flex: 9999;
}
```
![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%8811.21.32.png
)

item1和item2并没有因为比例小而压缩。因为是比例切分的是剩余空间，只是额外加给子元素的，这里的效果就是 item3几乎占领了所有剩余空间。另外两个元素则几乎和原来的尺寸一样，至少用肉眼是看不出差别的，item1 只分到剩余空间的 1/1002， 而item2 只分到剩余空间的 2/1002，都几乎可以忽略。

##### 4.5 剩余行宽的比例 + 最小宽度

再给 item1 的 flex 属性后加上一个最小宽度值，可以约束其最小宽度，优先级高于比例分配。

```css
.item-1 {
  flex: 1 200px;
}

.item-2 {
  flex: 2;
}

.item-3 {
  flex: 9999;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%8811.30.11.png
)

可以看到由于 item1 变宽了，整行容器剩余宽度也少了，所以 item3 也变窄了，但它拿到的比例是不变的。如果将 item1 设置得很宽，那么其他元素就可以被挤到下一行，如果item3也被挤下去的话，那么它仍会拿到下一行几乎所有的剩余空间。(会换行的前提是父容器添加有`flex-wrap: wrap;`属性)

```css
.item-1 {
  flex: 1 1200px;
}

.item-2 {
  flex: 2;
}

.item-3 {
  flex: 9999;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-12+%E4%B8%8B%E5%8D%8811.34.24.png
)

##### 4.6 flex-shrink

定义当容器空间被压缩时，某个元素减少宽度的比例。比例的基础则是

所有子元素最小宽度的和 - 当前容器的宽度

flex-grow 拿到的是剩余空间的指定比例进行增量处理。

flex-shrink 则拿到的是不足的那部分空间的指定比例进行减量处理，也就是分配到的比例越大，被压缩至临界点(当所有子元素到达指定的最小宽度)之后，被缩小的速度也就越快。

首先去掉 父容器的   `flex-wrap: wrap;` 让所有元素集中在一行


只保留item1和item2，并分别加上 `flex-basic: 500px;`。指定宽度基数，也就是用来计算剩余空间或者不足空间的基数。

```css
.item-1 {
  flex-basis: 500px;
  flex-shrink: 5;
}

.item-2 {
  flex-basis: 500px;
  flex-shrink: 1;
}
```

这会让外层容器留给内部子元素的宽度小于 1000px(500+500) 时，将不够的那部分宽度按比例从 item1 和 item2 宽度中扣除，扣除比例是 5:1 也就是从 item1中扣除 5/6， item2 扣除 1/6

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/shrink.gif
)

##### 4.7 flex 是一个快捷属性，可以拆开用。

`flex-grow: 1` == `flex: 1`

`flex-basis: 200px` == `flex: 200px`

`flex: 1 200px` == `flex-grow: 1` + `flex-basis: 200px`

`flex: 1 1 200px` == `flex-grow: 1` + `flex-shrink: 1`+ `flex-basis: 200px`

#### 5 内部元素的对齐方式

##### 5.1 父容器对内部元素的控制

有两个可用的属性(start在横向上和纵向上分别代表左边和上面)

- `justify-content` 控制纵向 main-axis 方向上的排布
  - flex-start （默认）
  - flex-end
  - center
  - space-around
  - spece-between

- `align-items` 控制纵向 cross-axis 方向上的排布
  - stretch （默认）
  - center
  - flex-start
  - flex-end

##### 5.1.1 垂直方向上的控制 align-items

重新准备案例，回到最初的只有 `display: flex;` 的状态，只保留4个item

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%881.23.08.png
)

然后给 body 和 外层container 都设置 `height: 100%;`让容器在垂直方向上撑开：

```css
html, body {
  margin: 15px;
  height: 100%;
}

.container {
/* other properties */
  display: flex;
  height: 100%;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%881.27.51.png
)

所有子元素在cross-axis方向上被一直拉伸到了父容器底部。这是由于默认情况下父容器带有 `align-items: stretch` 属性。在容器没有被撑开且子元素很小时，这个情况不明显。

验证这一点也可以去掉 body 和 container 的 `height: 100%` 。直接在某个item内加上大段文字：

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%881.34.42.png
)

容器被撑开的同时，所有的子元素也随着拉伸。

回到第一个例子，如果要修复，可以给父容器设置 `align-items: flex-start;`

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%881.39.16.png
)

align-items: 的另外几个value的效果则很清楚了

center 垂直居中居左

flex-end 底部居左

如果要单独指定某个item在垂直方向上的位置，可以使用 `align-self: center` 类似的方法。

##### 5.1.2 横轴方向上的控制

- `justify-content` 控制纵向 main-axis 方向上的排布
  - flex-start （默认）
  - flex-end
  - center
  - space-around
  - spece-between

默认情况下是居左。需要说的是后两种 spece-around 和 space-between

`space-between` 让所有子元素之间留有相同的空位。

接上上面的例子

```css
.container {
/* other properties */
  height: 100%;
  align-items: flex-start;
  justify-content: space-between;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%881.50.21.png
)

`space-around` 让每一个元素的左右两边都拿到相等的空间，这会使边缘处的空间看起来只有中间的一半。

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%881.50.42.png
)

##### 5.1.3 使用`margin-left/right: auto` 来调整元素排布

还是回到初始的靠上靠左排布。

现在如果给 item1 单独指定 `margin-right: auto;`

```css
.item-1 {
  margin-right: auto;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%882.00.17.png
)

item1 右边的所有 sibling 元素被排到了容器右边

如果给 item3 指定 `margin-left: auto;`

```css
.item-3 {
  margin-left: auto;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%882.04.46.png
)

item3 左边所有的sibling元素被排到了左边。

原理是被指定 margin为auto一侧的其他所有元素会被排开要容器边缘。


##### 5.1.4 使用 `order` 来调整元素排布

先回到初始状态，全部靠上居左。

现在给 item1 指定 `order: 1`

```css
.item-1 {
  order: 1;
}
```

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7+2018-04-13+%E4%B8%8B%E5%8D%882.09.20.png
)

item1 被排到了最后。

order 的工作原理是：

- 默认值是0
- 数值越小越靠前
- 数值越大越靠后

数值具体大小不重要，相对于其他元素的order值决定了排布顺序。比如 -1，0，1，2 和 9，99，999，9999 效果相同。

#### 6 flex box 规则的嵌套使用

flexbox 内部子元素也可以用作 flexbox 再对内部元素进行相同的规则应用。



---

参考资料：

https://scrimba.com/g/gflexbox

https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox
