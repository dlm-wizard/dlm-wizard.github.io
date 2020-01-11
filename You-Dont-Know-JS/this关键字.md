## 快速开始

- [`[基础]` this 到底是什么](#this到底是什么)
- [`[Global]` Global context](#Global_context)
- [`[Function]` Function context](#Function_context)
- [`[Function]` Simple call](#Simple_call)
- [`[Function]` As an object method](#As_an_object_method)
- [Arrow functions](#Arrow_functions)
- [this on the object's prototype chain](#this_on_the_object's_prototype_chain)
- [getter/setter](#getter/setter)
- [As a DOM event handler](#As_a_DOM_event_handler)
- [call、apply 和 bind](#call、apply和bind)
- [参考](#参考)

`this` 提供了一种更优雅的方式"隐式"传递一个对象引用，API 可以被设计的更加简洁并且易于复用。
当我们介绍对象和原型时，你就会明白函数可以自动引用合适的上下文对象有多重要。

## this 到底是什么？

`this` 是个非常特殊的关键词标识符，在每个函数的作用域中自动被创建。

当函数被调用时，一个 activation record（即执行上下文）被创建。这个 record 信息包括：`call-stack`（函数的调用栈）、函数的调用方法、变量对象等等。record 的一个属性就是 `this`，指向函数执行期间 `this` 对象。

- `this` 是 runtime binding
- `this` 的上下文基于函数调用的情况，与函数定义在哪无关，和怎么调用有关

## Global_context

在全局上下文中（任何函数之外），`this`指向全局对象。

```
console.log(this === window); // true
```

## Function_context

## Simple_call

在非严格模式，简单调用（独立函数调用）。由于 `this` 未通过 call 指定，且 `this` 必须指向对象，默认就指向全局对象。

在严格模式 "use strict" 无法使用默认绑定，因此 `this` 为 undefined。

## As_an_object_method

在严格模式下，this 保持进入 execution context 时被设置的值。那么 this 是如何被设置的呢？

首先，我们需要先了解一下 [ECMAScript 5.1 规范](https://yanhaijing.com/es5/#304)

### MemberExpression（左值表达式）

表达式（expression）JavaScript 中的一个短语，通过运算符（operator）组合，JavaScript 引擎会将其计算（evaluate）出一个结果。

lval（left-value）：左值，指的是表达式只能出现在赋值运算符的左侧。

- PrimaryExpression

```
原始表达式：Literal、Identifier「关键字」和变量
```

- FunctionExpression

```
函数定义表达式：函数直接量
```

- MemberExpression [ Expression ] / . IdentifierName

```
属性访问表达式：一个对象属性 / 数组元素的值
```

- new MemberExpression Arguments

```
对象创建表达式
```

<div align="center">  <img src="https://github.com/dlm-wizard/dlm-wizard.github.io/blob/master/You-Dont-Know-JS/assets/11.2.3%20%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8.png?raw=true" width=""/></div><br>

### foo()

#### 1. 根据规范 [11.2.3 函数调用](https://yanhaijing.com/es5/#164)，在 foo() 函数调用时，step1 会首先对函数名部分进行计算[10.3.1 标识符解析](https://yanhaijing.com/es5/#144)并赋值给 ref。

```
foo 是标识符，在执行的时候回进行标识符解析，返回 GetIdentifierReference() 的结果
```

**[10.2.2 GetIdentifierReference](https://yanhaijing.com/es5/#139)**

<div align="center">  <img src="https://github.com/dlm-wizard/dlm-wizard.github.io/blob/master/You-Dont-Know-JS/assets/10.2.2.1%20GetIdentifierReference.png?raw=true" width=""/></div><br>

<div align="center">  <img src="https://github.com/dlm-wizard/dlm-wizard.github.io/blob/master/You-Dont-Know-JS/assets/10.2.1.1.1%20HasBinding(N).png?raw=true(N)" width=""/></div><br>

我们看此处返回了一个**Reference** ， **base value 是** envRec 也就是 [10.3.1](https://yanhaijing.com/es5/#144) 中**传入的 execution context’s LexicalEnvironment**
（词法环境为环境记录项 Environment Record 的组成，它是规范用来管理当前作用域下面变量的类型）

其抽象的数据结构大概为：

```js
fooReference = {
  base: LexicalEnvironment,
  name: "foo",
  strict: false
};
```

到这里为止，第一步执行完毕，ref = fooReference。

**Reference**

> The Reference type is used to explain the behaviour of such operators as delete, typeof, and the assignment operators.

Reference 表示的是对某个变量、数组元素或对象属性所在内存地址的引用（引擎需要按这类结构设计`left-hand`等可获取数据内容），而非对其所在内存地址所保存值的引用。ECMAScript 有内部的 GetValue 函数，用于从 Reference 类型值中获取该内存地址所保存的值；此外还有 PutValue 函数，用于将一个值绑定到一个 Reference 类型值所引用的内存地址。

> 在 ECMAScript 中，赋值运算符的左侧只能是一个内存地址，而不能是一个值。（其实很好理解，你可以把值保存在某个内存地址，而不能把值赋给另一个值）

> A Reference is a resolved name binding.

> A Reference consists of three components, the base value, the referenced name and the Boolean valued strict reference flag.

> The base value is either undefined, an Object, a Boolean, a String, a Number, or an environment record (10.2.1).

- base value

```
指向引用的原值，只可能是 undefined, an Object, a Boolean, a String, a Number, or an environment record 其中的一种
```

- referenced name

```
引用的名称
```

- strict`标识是否严格模式`

参考[10.3.1 标识符解析](https://yanhaijing.com/es5/#144)，栗子如下：

```js
var foo = 1;

// 对应的Reference是
var fooReference = {
  base: EnvironmentRecord,
  name: "foo",
  strict: false
};
```

再来一个：

```js
var foo = {
  bar: function() {
    return this;
  }
};

// 对应的barReference是
var barReference = {
  base: foo,
  name: "bar",
  strict: false
};
```

**获取 Reference 组成部分**

- GetBase

```
返回 reference 的 base value
```

- IsPropertyReference

```
如果 base value 是一个对象，就返回true
```

**从 Reference 获取对应值**

- GetValue

是规范中非常重要的一个方法。暂时记住他的结果一般为：如果传入的是 reference，就会返回该内存地址所保存的值（Js 数据类型）。

```js
var foo = 1;
var fooReference = {
  base: EnvironmentRecord,
  name: "foo",
  strict: false
};

GetValue(fooReference) = 1;
```

> 6.If Type(ref) is Reference, then

>       a.If IsPropertyReference(ref) is true, then

>           i.Let thisValue be GetBase(ref).

>       b.Else, the base of ref is an Environment Record

>           i.Let thisValue be the result of calling the ImplicitThisValue concrete method of GetBase(ref).

> 7.Else, Type(ref) is not Reference.

>       a. Let thisValue be undefined.

#### 2. 简单来说就是判断 `ref` 是否为 `Reference` 类型

```
1. 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)

2. 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)

3. 如果 ref 不是 Reference，那么 this 的值为 undefined

```

#### 3. 因为是 Environment Record，执行 2 后，this 的值就是 undefined。

> [10.2.1.1.6 ImplicitThisValue()](https://yanhaijing.com/es5/#128)
> 声明式环境记录项永远将 undefined 作为其 ImplicitThisValue 返回。

### 如何解释 foo.bar()

> MemberExpression [ Expression ] is evaluated as follows「注：MemberExpression 与 Expression 分开进行计算」

> 规范解析形式为 MemberExpression [ Expression ]，所以 this 只会指向当前一层的对象。

#### 1. 根据规范 [11.2.3 函数调用](https://yanhaijing.com/es5/#164)，在 foo() 函数调用时，step1 会首先对`MemberExpression`进行计算[11.2.1 属性访问](https://yanhaijing.com/es5/#163)并赋值给 ref，步骤同 foo() 解析。

```js
fooReference = {
  base: LexicalEnvironment,
  name: "foo",
  strict: false
};
barReference = {
  base: LexicalEnvironment,
  name: "bar",
  strict: false
};

baseValue = GetValue(fooBarReference) = foo;
propertyNameValue = GetValue(barReference) = bar;
```

```js
fooBarReference = {
  base: foo,
  name: "bar",
  strict: false
};
```

#### 2. 因为 fooBarReference 是 reference，IsPropertyReference(foo) 返回 true「foo 是一个对象」。所以 this 的值为 GetBase(ref)。

#### 3. GetBase(ref) === fooBarReference.base === foo

## Arrow_functions

箭头函数中，this 由词法作用域设置（set lexically）。被设置为包含他的 execution context 的 this，并且不再被调用方式影响（call/apply/bind）。

## this_on_the_object's_prototype_chain

`this` 指向调用方法的对象。

## getter/setter

`this` 指向设置 / 获取属性的对象。

## As_a_DOM_event_handler

`this` 自动设置为触发事件的 dom 元素。

## call、apply 和 bind

null 或者 undefined 作为 this 的绑定对象传入 call、apply 或 bind，在调用时会被忽略，实际应用的是`独立函数调用（Simple_call）`。

## 参考

[whats-a-valid-left-hand-side-expression-in-javascript-grammar](https://stackoverflow.com/questions/3709866/whats-a-valid-left-hand-side-expression-in-javascript-grammar)

[深入理解 JS 中声明提升、作用域（链）和`this`关键字](https://github.com/creeperyang/blog/issues/16)

[JavaScript 深入之从 ECMAScript 规范解读 this](https://github.com/mqyqingfeng/Blog/issues/7)

[根治 JavaScript 中的 this-ECMAScript 规范解读](https://zhuanlan.zhihu.com/p/21805749)
