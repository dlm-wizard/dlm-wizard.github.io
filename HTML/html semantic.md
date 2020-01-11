## 从 www 说起

> To publish information for global distribution, one needs a universally understood language

构建技术：HTML（超文本标记语言）

- 发布文档（文本、表格、列表）
- `<a>` 超链接
- `<form>` 远程服务
- Web 应用

HTML 最终由机器读取解析，或进行渲染，或挖掘出有用的信息为其他应用提供支持。

<div align="center">  <img src="http://justineo.github.io/slideshows/semantic-html/img/flow.png" width=""/></div><br>

## 什么是语义化

简单说来就是让机器可以读懂内容

随着 Web 规模的不断扩大信息量之大已经不在人肉处理的范围内了。这时人们开始用机器处理 Web 上发布的各种内容，搜索引擎就诞生了。再后来人们又设计了各种智能程序对索引好的内容作各种处理和挖掘。所以 HTML 机器可读变得越来越重要。

## 关于 HTML5 各个元素语义的描述

**HTML 语义：元素 + 属性 + 属性值（文档结构）**

## 全局属性

- id：用于引用，「不应依赖」其语义处理相关元素
- class：描述「内容性质」的值
- title：以下

```
a: 描述目标信息
img: 版权 / 描述
引用：来源信息
交互元素：操作指南
```

- lang：内容语言

## 元数据 (metadata)

被 `<head>` 标签包裹的内容

- title：文档对外标题（窗口、history stack、搜索）
- meta：name / http-equiv / charset

```
name属性：种类
content属性：内容
```

## links

### `<link>`

`外部资源链接`是组成当前文档的外部资源，由 UA 处理。超链接用来导航到其他资源「UA 中打开、下载」

- link：文档本身与其他资源关系，比如 icon、stylesheet。

```
必须包含 rel 及 href 属性
```

### `<a>`

[`<a>` Tag Attributes](https://www.w3schools.com/tags/tag_a.asp)
[当前文档的作者并不推荐超链接指向的文档](http://justineo.github.io/slideshows/semantic-html/#/5/8)

存在 href 属性时为超链接。缺少的话，就是链接占位符了。

## 区块

- `<article>`：独立文档、页面、应用、站点

```
1. 可以单独发布、重用
2. 帖子、文章、用户评论、一个可交互 widget
```

- `<section>`：按主题将内容分组，通常会有 heading

```
当你希望元素的内容体现在`文档结构图 (outline)` 中，用 section 是合适的
```

- `<nav>`：帮助 UA 迅速获得导航内容
- `<aside>`：与周围内容关系不太密切，通常表现为侧边栏、引述内容。
- `<header>`：介绍性描述或导航信息（目录/Search/logo）
- `<footer>`：作者/rel（相关）文档/版权

```
header 与 footer 通常包含 h1~h6，但不影响文档结构图生成。
```

- `<address>`：与父级 article 或与文档关联的联系人信息

## 分组内容

- `<p>`：段落（主题接近的若干句子组成的文本块）
- `<pre>`：已排版内容，或代码片段
- `<blockquote>`：引用的内容

```
cite属性：来源的 URL
```

- `<ol>`, `<ul>`, `<li>`：有序（无）列表，`<ol>` 下 `<li>` value 属性代表列表项序号值。
- `<dl>`, `<dt>`,`<dd>`：名值对集合。FAQ/元数据...

```
dl：define list
dt：title
dd：description
```

- `<figure>`：比较独立的部分，被主要内容引用。图片/代码/图表...

```
通常会有标题（figcaption）
```

- `<div>`：本身无语义

```
1. 可以和 class, lang, title 等属性结合，为一系列连续内容增加语义
2. 最后考虑的选择
```

### 文本级语义

- `<span>`：本身无语义

```
通常和 class、lang 结合增加语义，有更合适元素不应该选择 span
```

- `<em>`：表示侧重点强调（可视化 UA 一般渲染为斜体）
- `<strong>`：表示内容重要性（可视化 UA 一般渲染为粗体）

- `<i>`：不再只是斜体，建议搭配 class / lang 属性

```
常用作小图标，但语义是不正确的，即使 i 标签确实比 span 短
```

- `<b>`：不再只是粗体，简易搭配 class 属性
- `<small>`：不再只是小字
- `<s>`：不再只是「带删除线文字」
- `<u>`：不再只是「带下滑线文字」

[i、b、small、s、u、 元素的语义](http://justineo.github.io/slideshows/semantic-html/#/8/3)

- `<cite>`：引用的作品标题
- `<q>`：引用的内容

```
不用 q 用引号亦正确
```

- `<abbr>`：缩写，一般配合 title（全称） 使用
- `<mark>`：当前文档中需要注意但原文没有强调的含义

```
与用户当前的行为相关的内容，高亮显示搜索关键词
```

## 几个重要元素

### `<input>`

> 为 Web 表单创建交互式控件，以此接受用户数据

- type

```
text：单行文字
search：用于输入搜索字符串
button：无缺省行为按钮
checkbox：复选框（搭配 checked 属性）
radio：单选按钮（checked）
date：日期选择控件
datetime-local：时间选择控件（不含时区）
file：选择文件（搭配 accept 属性）
hidden：隐藏 input
reset：重置所有 input
submit：提交表单
password
```

- accept：文件类型
- checked：pre-selected
- readonly：Boolean
- disabled：Boolean
- required
- maxlength：用户最多输入字符数
- placeholder：提示
- name：具有 name 属性才会被提交（服务器需要对表单数据进行识别）
- value：String
- autocomplete: String
- autofocus：Boolean
- form：属于的 form 表单
- formaction：url submitted
- formenctype：encode form-data
- formtarget：如何展示收到的响应
- max / min：Number / Date

### `<form>`

- action：url submitted
- enctype：encode form-data
- method：
- name

### `<label>`

- for：binding Element
- form：binging form

### `<button>`

- type：button | reset | submit

```
始终为 button 指定类型，不同浏览器可能默认 type 不同
```

## 参考

[如何理解 Web 语义化？](https://www.zhihu.com/question/20455165)
