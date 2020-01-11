[CSS 世界](https://juejin.im/post/5ce607a7e51d454f6f16eb3d#heading-4)

## 块级元素

> every element in web design is a rectangular box

- 块级元素：div p form ul li h1-h6
- 内联元素：span img a i input textarea

需要注意的是："块级元素" 和 "display：block" 并不是一个概念。栗如 `<li>` 元素默认值是 `display：list-item` ，但是符合"块级元素"的基本特征，也就是一个水平 normal flow 上只能显示一个元素，多个块级元素则换行显示。

正是由于"块级元素"具有 `换行特性` ，因此理论上都可以配合 clear 属性清除浮动带来的影响` 。

```css
.clearfix::after {
  context: ".";
  display: block;
  ?不是none吗clear: both; /*clear 合用于块级元素*/
  overflow: hidden;
}
```

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/uesd4otp/1/embedded/js,html,result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

## normal flow

在 HTML 中任意一个元素其实就是一个对象，也就是一个盒子。默认情况下它是按照出现的先后顺序来排列，而这个排列的顺序就是 normal flow。

当浏览器开始渲染 HTML 文档时，从窗口顶端开始分配元素需要的空间。除非文档的尺寸被 CSS 规则限定，否则浏览器垂直扩展文档来容纳全部的内容。每个新的块级元素渲染为新行。行内元素则按照顺序被水平渲染直到当前行遇到边界，然后换到下一行垂直渲染。

通过 CSS 规范，不难发现这样的过程包括了：

- [块格式化(BFC：Block formatting context)](https://www.w3.org/TR/CSS2/visuren.html#block-formatting)
- [行内格式化（IFC：Inline formatting context）](https://www.w3.org/TR/CSS2/visuren.html#inline-formatting)
- [相对定位（Relative positioning）](https://www.w3.org/TR/CSS2/visuren.html#relative-positioning)

不过有一点面要注意，它们只能是其中一者，但不能同时属于两者。

综合上面的描述，也可以理解为格式化上下文对元素盒子做了一定的范围的限制，就是类似 `width` 和 `height` 做了限制一样。按照这方面来进行理解的话 `normal flow` 就是一个这样的过程。

在 CSS 中也可以通过`float`或者`position:absolute`两种方法让元素脱离文档流。而这两者的表现实际上非常相似。简单的可以理解为部分无视和完全无视的区别：

## BFC

在对应的块格式化上下文中，块级元素按照其在 HTML 源码中出现的顺序，在其容器盒子里从左上角开始，从上到下垂直地依次分配空间层叠（Stack），并且独占一行，边界紧贴父盒子边缘。两相邻元素间的距离由 margin 属性决定，在同一个块格式化上下文中的垂直边界将被重叠（Collapse margins）。除非创建一个新的块格式化上下文，否则块级元素的宽度不受浮动元素的影响。

## IFC

在对应的行内格式化上下文中，行内元素从容器的顶端开始，一个接一个地水平排列

由于一个盒子是无法解释 `display: inline-block` 这样的布局现象的，于是又新增了一个盒子，每个元素都有两个盒子。`外在盒子`负责元素是否换行，`容器盒子`负责 wh、内容的呈现等。

| 盒子模型     | 外在盒子 | 容器盒子     |
| ------------ | -------- | ------------ |
| block        | 块级盒子 | 块级容器盒子 |
| inline       | 内联盒子 | 内联盒子     |
| inline-block | 内联盒子 | 块级容器盒子 |

这里再强调一遍：我们通常设置的 `wh` 是作用在 `容器盒子` 上的。

## width 和 height 作用细节

### width

> width 的流特性

```css
width: auto; /*默认值*/
```

1.**包含块宽度** - 块级元素（flow width）

无宽度法则：通过区域自动分配水平空间实现自适应。一旦设置了宽度，flow width 就消失了。

**什么是 flow width？**

> BFC 上下文中，在容器盒子的左上角开始分配空间并独占一行。

流动性并不是看上去的宽度 100% 显示这么简单，而是一种 margin/border/padding 和 content-box 自动分配水平空间的机制。这种特性主要就是体现在里面的容器盒子上的。

在实际开发中，设置 `width: 100%` 可能会出现显示问题，比如尺寸溢出、margin 溢出（三栏布局），下面我们来学习一个例子：

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/uesd4otp/4/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

**格式化宽度**

对于非替换元素并且 `left/top` 或 `top/bottom` 对立方位的属性值同时存在，元素的宽度表现为"格式化宽度"， 其宽度大小相对于最近的定位祖先元素（position 属性值不是 static）计算。

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/uesd4otp/5/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

2.**包裹性** - float、absolute、inline-block、table、内联元素

- 包裹：元素大小由内容决定
- 自适应：永远小于包含块宽度（有一种 `max-width:100%` 的感觉），会在容器宽度处自动换行。

如果外部容器的宽度是 0px，里边 inline-block 元素的宽度是多少？

在 CSS 中，图片和文字的权重要远远大于布局，图文不会再 `wdith：auto` 时宽度变为 0，此时表现为最小宽度。

1.中文单词换行 2.连续英文单词不换行

3.**宽度溢出** - 内联元素 + white-space：no-wrap

### height: auto

height 要比 width 简单很多，因为 CSS 默认 normal flow 是水平方向的，宽度是稀缺的但是高度是无限的，高度的分配显得就很随意了。

高度由内容决定。

### width / height: 100%

> 如果包含块 width:auto，此时子元素 width：100% 有效。

> 如果包含块 height:auto，此时子元素 height：100% 是无效的。默认子元素 `height：auto`

浏览器渲染的基本原理是下载文档内容，加载头部资源，然后从上到下的渲染 DOM 内容。`width：auto` 由包含块或内容决定。渲染到子元素的时候，而渲染到子元素的时候，包含块宽度已经固定。宽度不够的话，overflow 属性就是为此而生的。但 height 是由内容决定的，此时子元素的内容还未渲染。

规范：如果包含块的高度没有显式指定（即高度由内容决定），并且该元素不是绝对定位，则计算值为 auto。auto 和百分比计算肯定是无法计算的。

```css
/* 百分比参照物：定位参照物元素 */
div {
  height: 100%;
  position: absolute;
}
```

## `max-*`和 `min-*`系列

- `max-*`：none（默认）
- `min-*`：auto（默认）

由于 `min-width/height` 属于边界行为的属性，本身就是为 normal flow（自适应或流体布局） 而生的。

**超越 !important**：max-width 会覆盖 width，并且是超级覆盖，栗子：

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/2s5198gp/1/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

## 内联元素

> 内联元素往往具有继承特性，并且一般是多个属性共同作用的结果。

内联元素的"内联"指的是"外在盒子"，如 `display：inline-block`。

### 概念

- `content area`

```
文本：字符盒子，简单来说就是文本选中背景色区域
替换元素：如图片等，content area 为元素自身
```

- inline box：外在盒子
- line box：由 inline box 组成
- containing block：包含块
- 幽灵空白结点：存在于 line box 前，具有该元素字体、line-height 属性，宽度为 0 的 inline box。

> Each line box starts with a zero-width inline box with the element’s font and line height properties. We call that imaginary box a “strut”.

## x && 基线

什么是基线？

参考：[https://en.wikipedia.org/wiki/Baseline](<https://en.wikipedia.org/wiki/Baseline_(typography)>)

> In European and West Asian typography and penmanship, the baseline is the line upon which most letters "sit" and below which descenders extend.

- **对于文本元素**

```
字母 x 的下边缘（线）就是我们的基线，x-height 为字母 x 的高度（同时也是基线与中线之间的距离）。
```

- **替换元素**

```
基线就是他们的下边缘
```

- **`inline-block`**

基线位置：
- margin 的底边：没有包含内联元素 && overflow 非 visible
- 否则就是包含最后一行的内联元素的基线


<div align="center">  <img src="baseline" width="75%" height="75%" /></div><br>

`vertical-align：middle`指的是 inline-box 的垂直中心与 line box 基线往上 1/2 x-height 的高度对齐。

## line-height

**非替换元素**的高度完全由 line-height 决定，padding、border 属性对其来说是不会有任何影响的。

内联元素的高度是由固定高度和不固定高度组成，这个不固定高度就是这里的"行距（leading）"。`leading=line-height-font-size（content area）`

在 CSS 中，leading 分散在文字的上方和下方，half-leading 是 leading 高度的一半。

<div align="center">  <img src="行距" width="75%" height="75%" /></div><br>

但是**替换元素**的高度不会被 line-height 影响，其高度等于 `height+padding+margin` 的总和。

当图片与文字混合在一起时，对于这种替换元素与非替换元素共同形成一个 line box，此时 line-height 只能决定 line box 的最小高度。

### line-height && 垂直居中

因为 leading 的上下等分机制，当 line-height 大于 font-size 时，就会出现单行文本垂直居中的效果了。

如果是多行文本或者图片，我们还需要使用 vertical-align 属性。

1. inline-block 可以让外在盒子形成 line box，同时覆盖继承 line-height 属性保持内容文字垂直居中。
2. line box 前的幽灵空白结点继承包含块 line-hight，与 vertical-align 属性实现"垂直居中"。

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/1e8p0ua9/1/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

line-height 缺省值是 normal，normal 与 font-family 相关。我们来看下 line-height 与继承相关的属性值：

- Number：`Number * font-size`
- 百分比：`percentage * font-size`f
- Length：具体值

需要注意的是，只有 Number 会被子元素作为比例系数继承，百分比与 length 是作为计算属性（具体值）被子元素继承的。我们来看一个具体的栗子：

`line-height：150%` 和 `line-height：1.5em` 计算属性为 21px，而 h3 的 font-size 为 32px，所以 h3 的半行间距就是 -5.5px，所以文字溢出了 inline-box。

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/1e8p0ua9/5/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

对于重图文内容展示的 blog、论坛、公众号，考虑到文章阅读舒适性，一定要使用 Number，范围在 1.6~1.8 之间。

## vertical-align

`vertical-align` 只能应用于内联元素与 `table-cell` 元素。

浮动和绝对定位会让元素块状化，使 vertical-align 不起作用。

vertical-align 属性值可以分为以下 4 类：

- 线类：baseline（默认值）、top、middle、bottom
- 文本类：text-top / bottom
- 上下标类：sub、super
- 数值百分比类：20px、2em、20%

```
根据计算值的不同，相对于基线向上（或向下）偏移。vertical-align: baseline等同于vertical-align: 0。
```

### `vertical-align: middle`

vertical-align 属性的百分比值是相对于 line-height 计算的，不过很少使用。

middle 并不是真正意义上的垂直居中，所以会存在像素级别的误差「误差大小与 `font-size`、`font-family`相关」。

**table-cell**

我们发现，在设置 `vertical-align：middle` 后，table-cell 的子元素垂直对齐了。但是你要注意， `vertical-align` 作用的并不是子元素，而是 table-cell 元素自身。这意味着子元素即使是块级元素，也一样可以垂直居中!

**默认的基线对齐 😈**

```html
<div class="box">x<span>文字 x</span></div>
```

```css
.box {
  line-height: 32px;
}
.box > span {
  font-size: 24px;
}
```

Q1：为什么包含块宽度大于 line-height 呢？

`font-size：24px` 设置在内部 span 元素上（包含块字体和 span 出入较大），其实我们 span 形成的 line-box 之前有一个看不见的幽灵空白节点，一起组成了 line-box。

两个 inline-box 高度都是 32px，但是**对于字符来说，`font-size` 越大基线位置越向下移动。**因为 `vertical-align` 默认是基线对齐，较小的字符会发生向下的位移，从而超出了行高的限制。

A：包含块设置`font-size：0`、`vertical-align：top..`。

知道了文字大小影响高度问题后，对于问题：`常见的任意块级元素，里面若含有图片，则块级元素高度基本上都要比图片高度高`就很好理解了。栗子：

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/r17cnv30/1/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

`line-height`的计算值是 20px，但是 `font-size`只有 14px，幽灵空白元素向下就会出现 3px 的空白。

- 图片块状化（`display：block`）

```
可以消除 line-height、幽灵空白节点、vertical-align 的影响
```

- 包含块 `line-height or font-size` 足够小
- 消除 `vertical-align` 默认的基线对齐

Q2：内联特性导致 margin 无效

A：虽然 200px 远远超出了图片高度，但图片位置被"幽灵空白节点"限制住了。在 CSS 中，非主动触发位移的内联元素不可能跑到计算容器之外。

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/fopcardg/embedded/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

## 盒模型

<div align="center"><img src="" width="75%" height="75%"/></div><br>

- content-box
- padding-box
- border-box

为什么没有 margin-box?

`margin` 的背景永远是透明的，因此不可能作为 `backgound-clip` 或 `background-origin` 属性值出现。也不会影响盒子模型的大小。

> CSS2.1 的规范: The content edge surrounds the rectangle given by the width and height of the box.

简单翻译一下就是 content-box 是 width 和 height 给定矩形框。可是这样的宽度设置真的合理吗？不仅流动性丢失，而且盒子模型包含 padding、margin 会让盒子模型变大，有时候难以理解。

增加一个内部标签实现宽度分离（flow width），栗子如下：

```css
.parent {
  width: 100%;
}
/*内部元素宽度为 flow width*/
.child {
  margin: 0 20px;
  padding: 20px;
  border: 1px solid;
}
```

## box-sizing

box-sizing: border-box（IE 怪异盒模型） | content-box（w3c 标准盒模型）

`border-box`其实就是`IE怪异模式`下的盒子模型。在页面中应该声明文档类型「!DOCTYPE html」，让浏览器以 w3c 的标准解析和渲染。若没有进行文档声明，IE 浏览器会将盒子模型解释为 IE 盒子模型。

#### 改变 width 和 height 的作用细节!

`box-sizing: border-box` 改变 width 和 height 直接作用在 border-box 上。释放了 width 形成局部 flow width，实现自适应布局。

遇到等宽布局时，因为边框外的间距只能是 margin，但 `box-sizing` 并不支持 margin。

`box-sizing` 不支持 margin-box 的原因就是因为没有价值。一个 width 和 height 为 100px 的元素，设置 margin 后，offset 尺寸仍然不会有变化。但是 border 和 margin 就不一样了，设置 20px 的 padding，offsetWidth 就是 140px 了。

还有一个原因就是如果 `box-sizing` 支持了显示的 margin-box，那么 `background-origin` 到底是否应该支持这些属性呢?规范上写的很清楚：margin 的背景永远是透明的。

<iframe width="100%" height="300" src="//jsfiddle.net/dlm_keee/uesd4otp/7/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

#### box-sizing 的初衷

唯一离不开的就是 `box-sizing: border-box` 的就是 `<input>` 和 `<textarea>` 自适应包含块宽度。

`<textarea>` 为替换元素，替换元素特性就是大小由内部元素决定，并且不受 `display` 属性影响。我们只能通过 width 让 `<textarea>` 大小自适应。

如果 `<textarea>` 未设置 margin 和 padding 大小体验会很不好，通过宽度分离的话，需要外部增加 `div` 模拟 border、padding，`<textarea>` 作为子元素自适应，局限性就是无法高亮 border 边框。

box-sizing 真正的使用之道：

```css
input,
textarea,
img,
video,
object {
  box-sizing: border-box;
}
```

padding-box ？ background-clip 支持

## 参考

[聊聊 CSS 中的层叠相关概念](https://zhuanlan.zhihu.com/p/41516699)
