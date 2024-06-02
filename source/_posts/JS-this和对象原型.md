---
title: JS-this和对象原型
tags:
  - javascript
categories:
  - javascript
keywords: javascript
index_img: /img/2024/js.png
excerpt: 《你不知道的JavaScript》，读书笔记。
abbrlink: "34814581"
date: 2024-06-01 10:35:12
---
>绝大多数Web开发者可能都没有创建过一个JavaScript对象，他们只是把JavaScript当作按钮和AJAX请求之间的事件绑定粘合剂。其实我也曾经是他们中的一员，但是当我学会在JavaScript中使用原型和创建对象之后，整个世界都变样了。

---

# 关于this

>this关键字是JavaScript中最复杂的机制之一。它是一个很特别的关键字，被自动定义在所有函数的作用域中。但是即使是非常有经验的JavaScript开发者也很难说清它到底指向什么。

## 为什么使用this

下面我们来解释一下为什么要使用this

```js
function identify(){
	return this.name.toUpperCase()
}

function speak(){
	var greeting = "hello, I'm" + identify.call(this)
	console.log(greeting)
}

var me = {
	name: 'kyle'
}

var you = {
	name = 'Reader'
}

identify.call(me) // KYLE
identify.call(you) // READER

speak.call(me) // hello, I'm KYLE
speak.call(yout) // hello, I'm READER
```

这段代码可以在不同的上下文对象（me和you）中重复使用函数identify()和speak()，不用针对每个对象编写不同版本的函数。

如果不使用this，那就需要给identify()和speak()显式传入一个上下文对象。

```js
function identify(context){
	return context.name.toUpperCase()
}

function speak(context){
	var greeting = "hello, I'm" + identify(context)
	console.log(greeting)
}

var me = {
	name: 'kyle'
}

var you = {
	name = 'Reader'
}

identify(you) // READER
speak(me) // hello, I'm KYLE

```


>随着你的使用模式越来越复杂，显式传递上下文对象会让代码变得越来越混乱，使用this则不会这样。当我们介绍对象和原型时，你就会明白函数可以自动引用合适的上下文对象有多重要


## 误解

<p class="note note-primary">
之后会解释this到底是如何工作的，但是首先需要消除一些关于this的错误认识。
<br><br>
太拘泥于“this”的字面意思就会产生一些误解。有两种常见的对于this的解释，但是它们都是错误的。
</p>

### 指向自身

人们很容易把this理解成指向函数自身，这个推断从英语的语法角度来说是说得通的。

那么为什么需要从函数内部引用函数自身呢？常见的原因是递归（从函数内部调用这个函数）或者可以写一个在第一次被调用后自己解除绑定的事件处理器。

为了验证this并不像我们所想的那样指向函数本身，思考一下下面的代码：

```js
function foo(num){
	console.log("foo: "+ num)
	//记录foo被调用的次数
	this.count++
}

foo.count = 0

var i

for(int i=0; i < 10; i++){
	if(i > 5){
		foo(i)
	}
}

// foo: 6
// foo: 7
// foo: 8
// foo: 9

// foo被调用了多少次？
console.log(foo.count) // 0 ???为什么
```

<p class="note note-info">
console.log语句产生了4条输出，证明foo(..)确实被调用了4次，但是foo.count仍然是0。显然从字面意思来理解this是错误的。
</p>

>执行foo.count = 0时，的确向函数对象foo添加了一个属性count。但是函数内部代码this.count中的this并不是指向那个函数对象，所以虽然属性名相同，根对象却并不相同，困惑随之产生。

许多开发者并不会深入思考为什么this的行为和预期的不一致，也不会试图回答那些很难解决但却非常重要的问题。他们只会回避这个问题并使用其他方法来达到目的，比如创建另一个带有count属性的对象。

```js
function foo(num){
	console.log("foo: "+ num)
	//记录foo被调用的次数
	data.count++
}

var data = {
	count: 0
}

var i

for(int i=0; i < 10; i++){
	if(i > 5){
		foo(i)
	}
}

// foo: 6
// foo: 7
// foo: 8
// foo: 9

// foo被调用了多少次？
console.log(data.count) // 4
```

<p class="note note-info">
从某种角度来说这个方法确实“解决”了问题，但可惜它忽略了真正的问题——无法理解this的含义和工作原理——而是返回舒适区，使用了一种更熟悉的技术：词法作用域。
</p>

<p class="note note-primary">
词法作用域是一种非常优秀并且有用的技术。我丝毫没有贬低它的意思（可以参考本书第一部分“作用域和闭包”）。但是如果你仅仅是因为无法猜对this的用法，就放弃学习this而去使用词法作用域，就不能算是一种很好的解决办法了
</p>

>如果要从函数对象内部引用它自身，那只使用this是不够的。一般来说你需要通过一个指向函数对象的词法标识符（变量）来引用它。

思考一下下面这两个函数：

```js
function foo(){
	foo.count = 4 // foo指向它自身
}
```

```js
setTimeout(function(){
	// 匿名(没有名字的)函数无法指向自身
}, 10)
```

第一个函数称为具名函数，在它内部可以使用foo来引用自身。

第二个，传入setTimeout的回调函数没有名称标识符称为匿名函数，无法从函数内部引用自身。

>对于我们的例子来说，另一种解决方法是使用foo标识符替代this来引用函数对象：

```js
function foo(num){
	console.log("foo: "+ num)
	//记录foo被调用的次数
	foo.count++
}

foo.count = 0

var i

for(int i=0; i < 10; i++){
	if(i > 5){
		foo(i)
	}
}

// foo: 6
// foo: 7
// foo: 8
// foo: 9

// foo被调用了多少次？
console.log(foo.count) // 4

```

>然而，这种方法同样回避了this的问题，并且完全依赖于变量foo的词法作用域

>另一种方法是强制this指向foo函数对象：

```js
function foo(num){
	console.log("foo: "+ num)
	//记录foo被调用的次数
	//注意：在当前的调用方式下（参考下方代码），确保this指向foo
	this.count++
}

foo.count = 0

var i

for(int i=0; i < 10; i++){
	if(i > 5){
		//使用call(..)可以确保this指向函数对象foo本身
		foo.call(foo, i)
	}
}

// foo: 6
// foo: 7
// foo: 8
// foo: 9

// foo被调用了多少次？
console.log(foo.count) // 4

```

这次我们接受了this，没有回避它。仍然感到困惑的话，不用担心，之后我们会详细解释具体的原理。

### 它的作用域

>第二种常见的误解是，this指向函数的作用域。

需要明确的是，this在任何情况下都不指向函数的词法作用域。在JavaScript内部，作用域确实和对象类似，可见的标识符都是它的属性。但是作用域“对象”无法通过JavaScript代码访问，它存在于JavaScript引擎内部。

思考一下下面的代码，它试图（但是没有成功）跨越边界，使用this来隐式引用函数的词法作用域：

```js
function foo(){
	var a = 2
	this.bar()
}

function bar(){
	console.log(this.a)
}

foo() // ReferenceError: a is not defined
```

首先，这段代码试图通过this.bar()来引用bar()函数。这样调用能成功纯属意外，我们之后会解释原因。调用bar()最自然的方法是省略前面的this，直接使用词法引用标识符。

此外，编写这段代码的开发者还试图使用this联通foo()和bar()的词法作用域，从而让bar()可以访问foo()作用域里的变量a。这是不可能实现的，使用this不可能在词法作用域中查到什么。

<p class="note note-info">
每当你想要把this和词法作用域的查找混合使用时，一定要提醒自己，这是无法实现的。
</p>

## this到底是什么

>排除了一些错误理解之后，我们来看看this到底是一种什么样的机制。

<p class="note note-success">
this是在运行时进行绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件。this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。
</p>

<p class="note note-success">
当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方式、传入的参数等信息。this就是这个记录的一个属性，会在函数执行的过程中用到。（函数活动记录的一个属性~）
</p>

>接下来会探究如何寻找函数的调用位置，从而判断函数在执行过程中会如何绑定this。

## 小结

<p class="note note-info">
学习this的第一步是明白this既不指向函数自身也不指向函数的词法作用域，抛开以前错误的假设和理解。<br><br>
this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。
</p>


---

# this全面解析

## 调用位置

>在理解this的绑定过程之前，首先要理解调用位置：调用位置就是函数在代码中被调用的位置（而不是声明的位置）。只有仔细分析调用位置才能回答这个问题：这个this到底引用的是什么？

通常来说，寻找调用位置就是寻找“函数被调用的位置”，但是做起来并没有这么简单，因为某些编程模式可能会隐藏真正的调用位置。

<p class="note note-success">
最重要的是要分析调用栈（就是为了到达当前执行位置所调用的所有函数）。我们关心的调用位置就在当前正在执行的函数的前一个调用中。
</p>

下面我们来看看到底什么是调用栈和调用位置：

```js
function baz(){
	//当前调用栈是: baz
	//因此，当前调用位置是全局作用域

	console.log("baz")
	bar() // <--bar的调用位置
}

function bar(){
	// 当前的调用栈是baz -> bar
	// 因此，当前调用位置在baz中

	console.log("bar")
	foo() // <-- foo的调用位置
}

function foo(){
	// 当前的调用栈是baz -> bar -> foo
	// 因此，当前的调用位置在bar中

	console.log('foo')
}

baz() // <-- baz的调用位置
```

>你可以把调用栈想象成一个函数调用链，就像我们在前面代码段的注释中所写的一样。但是这种方法非常麻烦并且容易出错。另一个查看调用栈的方法是使用浏览器的调试工具。绝大多数现代桌面浏览器都内置了开发者工具，其中包含JavaScript调试器。就本例来说，你可以在工具中给foo()函数的第一行代码设置一个断点，或者直接在第一行代码之前插入一条debugger;语句。运行代码时，调试器会在那个位置暂停，同时会展示当前位置的函数调用列表，这就是你的调用栈。因此，如果你想要分析this的绑定，使用开发者工具得到调用栈，然后找到栈中第二个元素，这就是真正的调用位置。

## 绑定规则


>来看看在函数的执行过程中调用位置如何决定this的绑定对象。
>
>必须找到调用位置，然后判断需要应用下面四条规则中的哪一条。

### 默认绑定

最常用的函数调用类型：**独立函数调用**。可以把这条规则看作是无法应用其他规则时的默认规则。

思考一下下面的代码：

```js
function foo(){
	console.log(this.a)
}

var a = 2

foo() // 2
```

<p class="note note-info">
应该注意到的第一件事是，声明在全局作用域中的变量（比如var a = 2）就是全局对象的一个同名属性。
<br><br>
接下来可以看到当调用foo()时，this.a被解析成了全局变量a。为什么？因为在本例中，函数调用时应用了this的默认绑定，因此this指向全局对象。
</p>

>那么我们怎么知道这里应用了默认绑定呢？可以通过分析调用位置来看看foo()是如何调用的。在代码中，foo()是直接使用不带任何修饰的函数引用进行调用的，因此只能使用默认绑定，无法应用其他规则。

如果使用**严格模式（strict mode）**，则不能将全局对象用于默认绑定，因此this会绑定到undefined：

`foo() // TypeError: this is undefined`

<p class="note note-primary">
这里有一个微妙但是非常重要的细节，虽然this的绑定规则完全取决于调用位置，但是只有foo()运行在非strict mode下时，默认绑定才能绑定到全局对象；在严格模式下调用foo()则不影响默认绑定：
</p>
```js
function foo(){
	console.log(this.a)
}

var a = 2

(function(){
	"use strict"
	foo() // 2
})()
```

>通常来说你不应该在代码中混合使用strict模式和非strict模式。整个程序要么严格要么非严格。然而，有时候你可能会用到第三方库，其严格程度和你的代码有所不同，因此一定要注意这类兼容性细节。

### 隐式绑定

>另一条需要考虑的规则是调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含，不过这种说法可能会造成一些误导。

思考下面代码：

```js
function foo(){
	console.log(this.a)
}

var obj = {
	a: 2,
	foo: foo
}

obj.foo() // 2
```

首先需要注意的是foo()的声明方式，及其之后是如何被当作引用属性添加到obj中的。但是无论是直接在obj中定义还是先定义再添加为引用属性，这个函数严格来说都不属于obj对象。

然而，调用位置会使用obj上下文来引用函数，因此你可以说函数被调用时obj对象“拥有”或者“包含”它。

<p class="note note-info">
无论你如何称呼这个模式，当foo()被调用时，它的前面确实加上了对obj的引用。当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。因为调用foo()时this被绑定到obj，因此this.a和obj.a是一样的。
</p>

>对象属性引用链中只有上一层或者说最后一层在调用位置中起作用。举例来说

```js
function foo(){
	console.log(this.a)
}

var obj2 = {
	a: 42,
	foo: foo
}

var obj1 = {
	a: 2,
	obj2: obj2
}

obj1.obj2.foo() // 42
```

**隐式丢失**

一个最常见的this绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把this绑定到全局对象或者undefined上，取决于是否是严格模式。思考下面的代码：

```js
function foo(){
	console.log(this.a)
}

var obj = {
	a: 2,
	foo: foo
}

var bar = obj.foo // 函数别名！！！

var a = "oops, global" // a是全局对象的属性

bar() // "oops. global"
```

虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身，因此此时的bar()其实是一个不带任何修饰的函数调用，因此应用了默认绑定。

一种更微妙、更常见并且更出乎意料的情况发生在传入回调函数时：

```js
function foo(){
	console.log(this.a)
}

function doFoo(fn){
	// fn其实引用的是foo
	fn() // <--调用位置！！！
}

var obj = {
	a: 2,
	foo: foo
}

var a = "oops, global" // a是全局对象的属性

doFoo(obj.foo) // "oops, global"
```

**参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值，所以结果和上一个例子一样。**

如果把函数传入语言内置的函数而不是传入你自己声明的函数，会发生什么呢？结果是一样的，没有区别：

```js
function foo(){
	console.log(this.a)
}

var obj = {
	a: 2,
	foo: foo
}

var a = "oops, global" //a是全局对象的属性

setTimeout(obj.foo, 100) // "oops, global"
```

JavaScript环境中内置的setTimeout()函数实现和下面的伪代码类似：

```js
function setTimeout(fn, delay){
	// 等待delay毫秒
	fn() // <--调用位置！！！
}
```

>就像我们看到的那样，回调函数丢失this绑定是非常常见的。除此之外，还有一种情况this的行为会出乎我们意料：调用回调函数的函数可能会修改this。在一些流行的JavaScript库中事件处理器常会把回调函数的this强制绑定到触发事件的DOM元素上。这在一些情况下可能很有用，但是有时它可能会让你感到非常郁闷。遗憾的是，这些工具通常无法选择是否启用这个行为。

无论是哪种情况，this的改变都是意想不到的，实际上你无法控制回调函数的执行方式，因此就没有办法控制调用位置以得到期望的绑定。之后我们会介绍如何通过固定this来修复这个问题。

### 显示绑定

#### 硬绑定

#### API调用的上下文

### new绑定


## 优先级

**判断this**


## 绑定例外


### 被忽略的this


**更安全的this**

### 间接引用



### 软绑定


## this词法

## 小结


---


# 对象


## 语法


## 类型


**内置对象**


## 内容


### 可计算属性名

### 属性与方法


### 数组

### 赋值对象


### 属性描述符

#### Writable

#### Configurable


#### Enumerable


### 不变性


#### 对象常量


#### 禁止扩展


#### 密封


#### 冻结


### \[\[PUT]]

### Getter和Setter


### 存在性


#### 枚举



## 遍历


## 小结


---


# 混合对象"类"


## 类理论


### "类"设计模式


### JavaScript中的"类"

## 类的机制


### 建造

### 构造函数


## 类的继承


### 多态


### 多重继承


## 混入

## 显示混入


#### 再说多态

#### 混合复制


#### 寄生继承


### 隐式混入

## 小结

---

# 原型

## \[\[Prototype]]

### Object.prototype


### 属性设置和屏蔽


## "类"


### "类"函数

**关于名称**


### "构造函数"


#### 构造函数还是调用


### 技术


## (原型)继承

## 对象关联


### 创建关联

### 关联关系是备用


## 小结

---

# 行为委托

## 面向委托的设计

### 类理论


### 委托理论

#### 互相委托(禁止)

### 比较思维模型


## 类与对象

### 控件"类"

**ES6的class语法糖**

### 委托控件对象


## 更简介的设计


## 更好的语法

## 内省

## 小结

---




