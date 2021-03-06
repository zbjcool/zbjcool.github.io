---
title: "javascript词法"
subtitle: ""
layout: post
author: "zbj"
header-style: text
tags:
  - js
---
[toc]
# 词法

Javascript 首先转换成一系列输入元素。
Javascript 源码（脚本或者模块）从左往右被扫描，反复的把最长的可能的代码序列作为下一个输入元素。输入元素由token、格式控制字符、行终止符、注释和空白字符组成。如果符合词的规则，就构成词，词法分析技术可以使用状态机或者正则表达式。
JavaScript不但支持除法运算符“ / ”和“ /= ”，还支持用斜杠括起来的正则表达式“ /abc/ ”。除法和正则表达式会产生冲突问题。

## Unicode 格式控制符

格式控制符用于控制对源码文本的解释，但是并不会显示出来。
注意和是ES5新加入的两个格式控制字符，它们都是0宽的。

## 空白字符

空白字符指的是空格、制表符和换行符等。
很多公司的编码规范要求JavaScript源代码控制在ASCII范围内，那么，就只有五种空白可用了。

- (或称)是U+0009，是缩进TAB符，也就是字符串中写的 \t 。
- 是U+000B，也就是垂直方向的TAB符 \v，这个字符在键盘上很难打出来，所以很少用到。
- 是U+000C，Form Feed，分页符，字符串直接量中写作 \f ，现代已经很少有打印源程序的事情发生了，所以这个字符在JavaScript源代码中很少用到。
- 是U+0020，就是最普通的空格了。
- 是U+00A0，非断行空格，它是SP的一个变体，在文字排版中，可以避免因为空格在此处发生断行，其它方面和普通空格完全一样。多数的JavaScript编辑环境都会把它当做普通空格（因为一般源代码编辑环境根本就不会自动折行……）。HTML中，很多人喜欢用的&nbsp  最后生成的就是它了。

除了空白符之外，行终止符也可以提高源码的可读性。不同的是，行终止符可以影响 JavaScript 代码的执行。行终止符也会影响自动分号补全的执行。在正则表达式中，行终止符会被 \s 匹配。

## 行终止符
接下来我们来看看换行符，JavaScript中只提供了4种字符作为换行符。
```
* <LF>
* <CR>
* <LS>
* <PS>

其中，<LF>是U+000A，就是最正常换行符，在字符串中的\n。
是<CR>U+000D，这个字符真正意义上的“回车”，在字符串中是\r，在一部分Windows风格文本编辑器中，换行是两个字符\r\n。
<LS>是U+2028，是Unicode中的行分隔符。<PS>是U+2029，是Unicode中的段落分隔符。
大部分LineTerminator在被词法分析器扫描出之后，会被语法分析器丢弃，但是换行符会影响JavaScript的两个重要语法特性：自动插入分号和“no line terminator”规则。
```
## 注释

### 注释语法

JavaScript的注释分为单行注释和多行注释两种：

```javascript
/* MultiLineCommentChars */ 
// SingleLineCommentChars
```

### 单行注释

除了四种LineTerminator（换行符）之外，所有字符都可以作为单行注释。
这一行中 位于 // 之后直到行尾的所有内容都会被当作注释(因此会被编译器忽略)。单行注释的内容没有限制。

### 多行注释

多行注释中是否包含换行符号，会对JavaScript语法产生影响，对于“no line terminator”规则来说，带换行的多行注释与换行符是等效的。
多行注释 /* .. */ 适用于在注释中需要多行解释的情况。 
多行注释也可以出现在行中的任意位置，甚至可以出现在行中间，因为 */ 会结束注释。

```javascript
var a = /* 任意值 */ 42;
console.log( a ); // 42		 		 	 
```

### 代码注释原则

- 没有注释的代码不是最优的。
- 过多注释(比如每行一个)可能是拙劣代码的征兆。
- 代码应该解释为什么，而非是什么。如果编写的代码特别容易令人迷惑的话，那么注释也可以解释一下实现原理。

## token

词法规定了语言的最小语义单元：token，可以翻译成“标记”或者“词”
只要符合词的规则，就构成词

#### IdentifierName

IdentifierName(标识符名称)可以以美元符$下划线_或者Unicode字母开始。不能以数字开头
Unicode字符全集中的字母和数字(包括中文)。
任意字符可以使用JavaScript的Unicode转义写法，使用Unicode转义写法时，没有任何字符限制。

IdentifierName可以是Identifier、保留字。

IdentifierName可以是属性名。所以可以是保留字

```js
PropertyName :
  IdentifierName
  StringLiteral
  NumericLiteral
var obj = {
  null: 10,
  true: 20,
  let: 30
}
obj // {null: 10, true: 20, let: 30}
```

#####  Identifier

```js
Identifier ::
  IdentifierName but not ReservedWord
```

Identifier是IdentifierName，但不是保留字，Identifier可以用在变量名，函数名

非严格模式下，用 var 可以定义关键字变量

##### 保留字ReservedWord

```javascript
ReservedWord :: 
  Keyword
  FutureReservedWord
  NullLiteral
  BooleanLiteral
```

关键字、未来保留字、空字面量和布尔值字面量属于保留字

一些标识符作为关键字

```
await break case catch class const continue debugger default
delete do else export extends finally for function if import
ininstance of new return super switch this
throw try typeof var void while with yield
```

除了上述的内容之外，还有1个为了未来使用而保留的关键字:

```
enum
```

在严格模式下,有一些额外的为未来使用而保留的关键字:

```
implements package protected interface private public
```

除了这些之外，`NullLiteral`（`null`）和`BooleanLiteral`（`true false`）也是保留字，不能用于`Identifier`。

#### PunctuatorNumeric

Punctuator，符号。包括运算符和大括号等符号

```
{ ( ) [ ] . ... ; , < > <= >= == != === !== + - * % ** ++ --
<< >> >>> & | ^ ! ~ && || ? : = += -= *= %= **= <<= >>= >>>= &= |= ^= => / /=
```

#### Literal

##### 空字面量

null

##### 布尔字面量

true false

##### 数字字面量

###### 整数字面量

- 十进制
   12
   1.2
   // 谨慎使用 0 开头的数值：
   0888 // 转换为十进制 888
   0777 // 转换为八进制 777，十进制 511
   十进制数值字面量可以以 0 开头，但是如果 0 以后的最高位比 8 小，数值将会被认为是八进制而不会报错。
- 二进制
   二进制表示为开头是0后接大写或小写的B（0b或者0B）
   b = 0b101010
- 八进制
   八进制表示为开头是0后接大写或小写的O（0o或0O）

```
var n = 0O755; // 493
var m = 0o644; // 420
```
// 用0开头也可以实现（请查看上方十进制有关部分）
0755
0644
十进制的Number可以带小数，小数点前后部分都可以省略，但是不能同时省略，我们看几个例子：
```
.01
12.
12.01
```
这都是合法的数字直接量。这里就有一个问题
```
12.toString() // Invalid or unexpected token
12 .toString()
```
###### 浮点数字面量
浮点数字面值可以有以下的组成部分：
- 一个十进制整数，可以带正负号（即前缀“+”或“ - ”），
- 小数点（“.”），
- 小数部分（由一串十进制数表示），
- 指数部分。
- 指数部分以“e”或“E”开头，后面跟着一个整数，可以有正负号（即前缀“+”或“-”）。浮点数字面量至少有一位数字，而且必须带小数点或者“e”（大写“E”也可）。
```
10.24E+2
10.24e-2
10.24e2
```
##### 字符串字面量
JavaScript中的StringLiteral支持单引号和双引号两种写法。
```
"hello"
'hello'
```
单双引号的区别仅仅在于写法，在双引号字符串直接量中，双引号必须转义，在单引号字符串直接量中，单引号必须转义。字符串中其他必须转义的字符是`\`和所有换行符。
十六进制转义序列
'\xA9' // "©"
Unicode 转义序列
Unicode 转义序列要求在\u之后至少有四个字符。
'\u00A9' // "©"
##### 对象字面量
```
var o = { a: "foo", b: "bar", 1: 42 };
如果对象属性名字不是合法的javascript标识符，它必须用""包裹。
那么便不能用.访问属性值，而是通过类数组标记("[]")访问和赋值。
var o = { '': '空', '!': 'bang' }
o.'' // 错误
o['']
```
##### 增强的对象字面量
```
// ES6中的简略表示方法
var a = "foo", b = "bar", c = 42;
var o = {a, b, c};
// 不需要这样
var o = { a: a, b: b, c: c };
```
##### 数组字面量
[]
##### 正则表达式字面量

```
/ab+c/g
// 一个空的正则表达式字面量
// 必须有一个空的非捕获分组
// 以避免被当成是行注释符号
/(?:)/
```

##### 模板字面量

```js
`string text`
`string text line 1
string text line 2`
`string text ${expression} string text`
tag`string text ${expression} string text`
```


