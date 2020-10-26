### 前言

2020.9.27收到js宝书第四版，再一次打算跟着新版本系统地学习JS，并将里面的难点和重点做好总结。

### 第一章 什么是JavaScript

#### 1.1 简短的历史回顾（95、96、97）

为了实现客户端来处理表单验证的功能，1995年网景公司的工程师Brendan Eich开发了Mocha（后改为LiveScript）语言。在网景浏览器2发布前又借着Java语言的热度改名为JavaScript。

1996年，微软开发了JScript并发布了IE3，成为网景的竞争对手

两个不同的JavaScript实现显然对于语言本身的发展和开发者都是不利的，所以1997年，ECMA(欧洲计算机制造商协会)打造了ECMAScript这个js语言标准（也可理解为js语言规范）

#### 1.2 JavaScript实现

JavaScript 不等于 ECMAScript ,完整的JavaScript包含了以下三部分：

* 核心（ECMAScript）
* 文档对象模型（DOM）
* 浏览器对象模型（BOM）

所以，每个浏览器厂家会尽可能的以ECMAScript作为自己JavaScript实现的依据和标准，又会在DOM和BOM上设计些自己的API。

##### 1.2.1 ECMAScript（了解）

ECMA-262（ECMAScript）定义了什么？

* 语法
* 类型
* 语句
* 关键字
* 保留字
* 操作符
* 全局对象

**1.ECMAScript 版本**

关于ES版本只需要了解下ES5和ES6的发布时间点即可，其他版本不做过多介绍，可看原文了解。

* ECMA-262 的第 5 版，俗称ES5，于 2009 年 12 月 3 日正式发布。
* ECMA-262 的第 6 版，俗称 ES6、ES2015 或 ES Harmony（和谐版），于 2015年 6月发布。这一版包含了大概这个规范有史以来最重要的一批增强特性。

！所以今年的ES2020，俗称ES11

**2.ECMAScript 符合性是什么意思（略）**

**3. 浏览器对 ECMAScript 的支持（略）**

##### 1.2.1 DOM



##### 1.2.1 BOM

IE3 和 Netscape Navigator 3 提供了浏览器对象模型（BOM） API，用于支持访问和操作浏览器的窗口（即浏览器显示页面之外的部分）。

**BOM的问题**

BOM是唯一一个没有相关标准的 JavaScript 实现，所以每个浏览器厂家实现的都是自己的 BOM。但是随着H5的出现，BOM的实现细节应该会日趋一致。

**BOM的扩展(在BOM篇章中专门介绍)**

* navigator 对象，提供关于浏览器的详尽信息
* location 对象，提供浏览器加载页面的详尽信息
* screen 对象，提供关于用户屏幕分辨率的详尽信息
* performance 对象，提供浏览器内存占用、导航行为和时间统计的详尽信息；
* history对象...

#### 1.3 JavaScript版本（了解）

### 第二章 HTML中的JavaScript

本章主要介绍如何用正确的姿势在HTML中引入javaScript，

#### 2.1\<script>元素

script是由网景公司创造出来的，它有8个属性：（通常最常用的只有src，需理解掌握src、type、async、defer即可，现如今除了src其他属性都用的非常少）

* src：可选，表示包含要执行的代码的外部文件

* async：可选，表示应该立即开始下载脚本，但不能阻止其他页面动作，比如下载资源或等待其他脚本加载，一旦脚本下载完会立即执行脚本，所以会阻塞文档的解析。
* defer：可选。表示脚本可以延迟到文档完全被解析和显示之后再执行。只对外部脚本文件有效。
* language：废弃。最初用于表示代码块中的脚本语言（如 "JavaScript" 、 "JavaScript 1.2"
  或 "VBScript" ）。大多数浏览器都会忽略这个属性，不应该再使用它。
* type：可选。代替 language ，表示代码块中脚本语言的内容类型（也称 MIME 类型）。按照惯例，这个值始终都是"text/javascript" ，尽管 "text/javascript" 和 "text/ecmascript"都已经废弃了。JavaScript 文件的 MIME 类型通常是 "application/x-javascript" ，不过给type 属性这个值有可能导致脚本被忽略。在非 IE 的浏览器中有效的其他值还有"application/javascript" 和 "application/ecmascript" 。如果这个值是 module ，则代码会被当成 ES6 模块，而且只有这时候代码中才能出现 import 和 export 关键字。
* charset：可选。使用 src 属性指定的代码字符集。这个属性很少使用，因为大多数浏览器不在乎它的值。

* crossorigin：可选。配置相关请求的CORS（跨源资源共享）设置。默认不使用CORS。 crossorigin=
  "anonymous" 配置文件请求不必设置凭据标志。 crossorigin="use-credentials" 设置凭据标志，意味着出站请求会包含凭据。
* integrity：可选。允许比对接收到的资源和指定的加密签名以验证子资源完整性（SRI，
  Subresource Integrity）。如果接收到的资源的签名与这个属性指定的签名不匹配，则页面会报错，脚本不会执行。这个属性可以用于确保内容分发网络（CDN，Content Delivery Network）不会提供恶意内容。

**\<script>两种使用方式**

1. 直接在script中写代码

```
<script>
function sayHi() {
	console.log("Hi!");
}
</script>
```

>注意：
>
>* 这种方式在\<script>元素中代码执行完成之前，页面的剩余内容不会被加载，也不会显示
>
>* 代码中不能出现字符串 \</script>，想避免这个问题，只需要转义字符“\” 即可：
>
>```
><script>
>function sayScript() {
>console.log("<\/script>");
>}
></script>
>```

2. 引人外部文件中的 JavaScript

```
<script src="example.js"></script>
```

>注意：
>
>* 在解释外部 JavaScript 文件时，页面也会阻塞
>
>* HTML中不能使用单标签形式（XHTML 文档中，可以忽略结束标签）
>* 不应该再在 \<script> 和 \</script> 标签中再包含其他
>  JavaScript 代码。如果两者都提供的话，则浏览器只会下载并执行脚本文件，从而忽略行内代码
>* \<script> 元素可以包含来自外部域的 JavaScript文件（跨域原理），在使用外部js文件时，应确保其安全性及可靠性。
>* 在不使用defer 和 async 属性的情况下，浏览器都会按照 \<script> 在页面中出现的顺序依次解释它们

##### 2.1.1标签位置

1. 放在页面的 \<head> 标签内

```
<!DOCTYPE html>
<html>
    <head>
    	<title>Example HTML Page</title>
        <script src="example1.js"></script>
        <script src="example2.js"></script>
    </head>
    <body>
    <!-- 这里是页面内容 -->
    </body>
</html>
```

* 这种方式意味着必须把所有 JavaScript 代码都下载、解析和解释完成后，才能开始渲染页面（页面在浏览器解析到 \<body> 的起始标签时开始渲染）。对于需要很多 JavaScript 的页面，这会导致页面渲染的明显延迟，在此期间浏览器窗口完全空白

2. 放在页面的body元素中页面内容的后面

```
<!DOCTYPE html>
<html>
    <head>
    	<title>Example HTML Page</title>
    </head>
    <body>
    <!-- 这里是页面内容 -->
    <script src="example1.js"></script>
    <script src="example2.js"></script>
    </body>
</html>
```

* 这种方式页面会在处理 JavaScript 代码之前完全渲染页面。用户会感觉页面加载更快了，因为浏
  览器显示空白页面的时间短了

##### 2.1.2推迟执行脚本（了解）

在script标签添加defer属性，脚本会立即下载，延迟到整个页面都解析完毕后再运行。

```
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
        <script defer src="example1.js"></script>
        <script defer src="example2.js"></script>
    </head>
    <body>
    <!-- 这里是页面内容 -->
    </body>
</html>
```

* 在实际当中，推迟执行的脚本不一定总会按顺序执行或者在 DOMContentLoaded事件之前执行，因此最好只包含一个这样的脚本
* 对 defer 属性的支持是从 IE4、Firefox 3.5、Safari 5和 Chrome 7开始的，所以还是把要推迟执行的脚本放在页面底部比较好

##### 2.1.3异步执行脚本（了解）

在script标签添加async属性，脚本会立即下载，在脚本下载完成后会立即执行，阻塞页面的解析。

```
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
        <script async src="example1.js"></script>
        <script async src="example2.js"></script>
    </head>
    <body>
    <!-- 这里是页面内容 -->
    </body>
</html>
```

* 异步脚本保证会在页面的 load 事件前执行，但可能会在 DOMContentLoaded （参见第 17 章）之
  前或之后
* 与 defer 不同的是，标记为 async 的脚本并不保证能按照它们出现的次序执行（虽然defer也不保证哈）

##### 2.1.4动态加载脚本（了解）

因为 JavaScript 可以使用 DOM API，所以通过向 DOM 中动态添加 script 元素同样可以加载指定的脚本。只要创建一个 script 元素并将其添加到
DOM 即可。

```
let script = document.createElement('script');
script.src = 'gibberish.js';
document.head.appendChild(script);
```

* 以这种方式创建的 \<script> 元素是以异步方式加载的，相当于添加了 async 属性

如果要统一动态脚本的加载行为，可以明确将其设置为同步加载：

```
let script = document.createElement('script');
script.src = 'gibberish.js';
script.async = false;
document.head.appendChild(script);
```

* 使用动态创建script 元素并执行脚本这种方式对浏览器预加载器是不可见的，可能会严重影响性能

要想让预加载器知道这些动态请求文件的存在，可以在文档头部显式声明它们：

```
<link rel="preload" href="gibberish.js">
```

##### 2.1.5XHTML 中的变化（了解）

##### 2.1.6 废弃的语法（了解）

#### 2.2 行内代码与外部文件

虽然可以直接在 HTML 文件中嵌入 JavaScript 代码，但最佳实践应尽可能将 JavaScript 代码放在外部文件中。原因如下：

* 可维护性
* 缓存
* 适应未来

#### 2.3文档模式（了解）

文档模式决定了浏览器渲染页面的方式，有两种文档模式：混杂模式（quirks mode）和标准模式（standards mode）

* 可以通过doctype切换文档模式
* 混杂模式会使页面模仿老式浏览器的行为
* 标准模式会以W3C标准来渲染页面

#### 2.4\<noscript> 元素

早期有些浏览器不支持javascript，可以使用这个元素提示用户:

```
<body>
    <noscript>
    	<p>This page requires a JavaScript-enabled browser.</p>
    </noscript>
</body>
```

也可以手动关闭浏览器对js的支持，也会触发这个元素生效。

>* 如果浏览器支持js，浏览器不会渲染 <noscript>中的内容
>
>* 这个标签对于一些旧版浏览器还是有作用的，但是兼容主流浏览器可不使用

### 第三章 语言基础

#### 3.1语法

##### 3.1.1 区分大小写

##### 3.1.2 标识符

所谓标识符，就是变量、函数、属性或函数参数的名称

* 第一个字符必须是一个字母、下划线（ _ ）或美元符号（ $ ）；
* 剩下的其他字符可以是字母、下划线、美元符号或数字
* 使用驼峰大小写形式
* 关键字、保留字不能作为标识符

##### 3.1.3 注释

js中注释包含两种：单行注释和多行注释：

```
// 单行注释
/*
 多行注释
*/
```

##### 3.1.4 严格模式

严格模式是ECMAScript 5中提出的一种更严格的语法条件下运行的一种模式。

严格模式的使用：

- 在全局或函数的第一条语句定义为: 'use strict';
- 如果浏览器不支持, 只解析为一条简单的语句, 没有任何副作用

##### 3.1.5 语句

#### 3.2 关键字与保留字（了解）

有空看看即可，没必要背下来，自然而然后面就会避免使用这些关键字了。

* 关键字

```
break do in typeof
case else instanceof var
catch export new void
class extends return while
const finally super with
continue for switch yield
debugger function this
default if throw
delete import try
```

* 保留字：保留给将来做关键字用的

```
始终保留:
enum
严格模式下保留:
implements package public
interface protected static
let private
模块代码中保留:
await
```

#### 3.3 变量

有 3 个关键字可以声明变量： var 、 const 和 let。其中， var 在ECMAScript 的所有版本中都可以使用，而 const 和 let 只能在 ECMAScript 6及更晚的版本中使用。

##### 3.3.1  var 关键字

##### 3.3.2  let 声明

let 跟 var 的作用差不多，但有着非常重要的区别：

* let 声明的范围是块作用域
* let 声明的变量不会在作用域中被提升
* let 声明之前的执行瞬间被称为“暂时性死区”（temporal dead zone），var没有这个瞬间
* let 在全局作用域中声明的变量不会成为 window 对象的属性（ var 声
  明的变量则会）
* let不能重复声明，而var可以

##### 3.3.3  const 声明

const 的行为与 let 基本相同，唯一一个重要的区别是：

* 用它声明变量时必须同时初始化变量
* const声明的是常量，尝试修改 const 声明的变量会导致运行时错误。

##### 3.3.4 声明风格及最佳实践

1. 不使用 var
2.  const 优先， let 次之

#### 3.4 数据类型

ECMAScript 有 6 种简单数据类型（也称为原始类型）： Undefined 、 Null 、 Boolean 、 Number 、
String 和 Symbol 。 Symbol （符号）是 ECMAScript 6 新增的。还有一种复杂数据类型叫 Object （对
象）。

##### 3.4.1  typeof 操作符

使用typeof操作符可以检测数据的数据类型，会返回下列字符串之一

* "undefined" 表示值未定义；

* "boolean" 表示值为布尔值；
*  "string" 表示值为字符串；
*  "number" 表示值为数值；
* "object" 表示值为对象（而不是函数）或 null ；
* "function" 表示值为函数；
* "symbol" 表示值为符号。

所以，typeof对于基本数据类型，除了null都能返回正确的类型，对于引用数据类型，除了function都会返回"object"！

>注意：
>
>* typeof 是一个操作符而不是函数
>* typeof的返回值是字符串
>
>为什么调用 typeofnull 返回的是 "object"？
>
>* 因为特殊值 null 被认为是一个对空对象的引用

##### 3.4.2  Undefined 类型

**知识点1：**Undefined 类型只有一个值，就是特殊值 undefined

**知识点2:**Undefined表示未初始化，当使用 var 或 let 声明了变量但没有初始化时，就相当于给变量赋予了 undefined 值：

```
let message;
console.log(message === undefined); // true
```

这段代码相当于

```
let message = undefined;
console.log(message === undefined); // true
```

**知识点3:**但实际上我们不会给变量赋值undefined，因为没这个必要，未初始化的变量都会取得undefined值。

>undefined在 ECMA-262 第 3 版之前是不存在的。增加这个特殊值的目的就是为
>了正式明确空对象指针（ null ）和未初始化变量的区别

**知识点4:**包含 undefined 值的变量跟未定义变量是有区别的：

```
let message; // 这个变量被声明了，只是值为 undefined
// 确保没有声明过这个变量
// let age
console.log(message); // "undefined"
console.log(age); // 报错
```

>对于一些名词作一下解释：未声明，未定义，未初始化...
>
>* 未声明 = 未定义，表示压根没声明/定义这个变量
>* 未初始化 = 为赋值，表示声明了但是没有初始化/赋值

**知识点5:**对未声明的变量，可以对它调用 typeof 。（对未声明的变量调用 delete 也不会报错，但这个操作没什么用，
实际上在严格模式下会抛出错误。）

```
let message; // 这个变量被声明了，只是值为 undefined
// 确保没有声明过这个变量
// let age
console.log(typeof message); // "undefined"
console.log(typeof age); // "undefined"
```

**知识点6:**undefined 是一个假值（转换布尔值为false）

```
let message; // 这个变量被声明了，只是值为 undefined
// age 没有声明
if (message) {
// 这个块不会执行
}
if (!message) {
// 这个块会执行
}
```

* if语句括号内会自动对非布尔值进行类型转换

##### 3.4.3  Null 类型

**知识点1：**Null 类型同样只有一个值，即特殊值 null

**知识点2：** null 值表示一个空对象指针，这也是给typeof 传一个 null 会返回 "object" 的原因：

```
let car = null;
console.log(typeof car); // "object"
```

**知识点3：**在定义将来要保存对象值的变量时，建议使用 null 来初始化，不要使用其他值。（其实很多人也喜欢直接用空对象{}初始化对象）

```
let car = null // 初始化对象
car = {
 ...
}
```

**知识点4：**用等于操作符（ == ）比较 null 和 undefined 始终返回 true

**知识点5：** null 和 undefined 有关系，但是它们的用途也是完全不一样的

* 不必显式地将变量值设置为 undefined，但null不是这样

**知识点6：**null 是一个假值（同undefined）

##### 3.4.4  Boolean 类型

**知识点1：**Boolean （布尔值）类型是 ECMAScript 中使用最频繁的类型之一，有两个字面值： true 和 false 

**知识点2：**布尔值字面量 true 和 false 是区分大小写的，因此 True 和 False （及其他大小混写形式）
是有效的标识符，但不是布尔值

**知识点3：**可以通过 Boolean()将其他的数据类型装换为布尔值，总结下其转换规则

| 数据类型  | 转换为 true 的值       | 转换为 false 的值           |
| --------- | ---------------------- | --------------------------- |
| Boolean   | true                   | false                       |
| String    | 非空字符串             | "" （空字符串，有空格不算） |
| Number    | 非零数值（包括无穷值） | 0 （包括+0、-0）、 NaN      |
| Object    | 任意对象               | null                        |
| Undefined | N/A （不存在）         | undefined                   |

##### 3.4.5  Number 类型

**知识点1：** Number 类型使用 IEEE 754格式表示整数和浮点值（在某些语言中也叫双精度值），这也是某些小数相加精度失准的原因之一。

**知识点2：**最常使用十进制整数，但是也可以用八进制或十六进制字面量表示。

```
let octalNum1 = 070; // 八进制的 56
let hexNum1 = 0xA; // 十六进制 10
```

> 八进制字面量在严格模式下是无效的，会导致 JavaScript 引擎抛出语法错误

###### 1.浮点值

**知识点3：**小数点前面不是必须有整数，但推荐加上

```
let floatNum2 = 0.1;
let floatNum3 = .1; // 有效，但不推荐
floatNum2 === floatNum3 // true
```



**知识点4：**因为存储浮点值使用的内存空间是存储整数值的两倍，所以 ECMAScript 总是想方设法把值转换为
整数。

```
let floatNum1 = 1.; // 小数点后面没有数字，当成整数 1 处理
let floatNum2 = 10.0; // 小数点后面是零，当成整数 10 处理
```

**知识点5：**对于非常大或非常小的数值，浮点值可以用科学记数法来表示。格式要求是一个数值（整数或浮点数）后跟一个大写或小写的字母 e，再加上一个要乘的 10 的多少次幂。

```
let floatNum1 = 3.125e7; // 等于 31250000
let floatNum2 =  3e-17; // 等于 0.00000000000000003
```

**知识点6：**浮点值的精确度最高可达 17 位小数，但在算术计算中远不如整数精确。

* 0.1 加 0.2 得到的不是 0.3，而是 0.300 000 000 000 000 04

>注意:
>
>* 之所以存在这种舍入错误，是因为使用了 IEEE 754数值，这种错误并非 ECMAScript
>  所独有。其他使用相同格式的语言也有这个问题。

###### 2.值的范围

**知识点7：**js支持的数值大小是有限制的，最大是Number.MAX_VALUE 中，这个值在多数浏览器中是 1.797 693 134 862 315 7e+308，最小是Number.MIN_VALUE 中，这个值在多数浏览器中是 5e324

**知识点8：**如果超过最大/最小值，会被自动转换为Infinity/-Infinity

```
console.log( 3e+308) //Infinity
```

**知识点9：** 使用 Number.NEGATIVE_INFINITY 和 Number.POSITIVE_INFINITY 也可以获取正、负 Infinity

###### 3.NaN

**知识点10：**NaN表示“不是数值”（Not a Number）在 ECMAScript 中，0、+0 或0 相除会返回 NaN：

```
console.log(0/0); // NaN
console.log(-0/+0); // NaN
```

如果分子是非 0 值，分母是有符号 0 或无符号 0，则会返回 Infinity 或 -Infinity ：

```
console.log(5/0); // Infinity
console.log(5/-0); // -Infinity
```

**知识点11：** NaN与任何数计算都为NaN， NaN 不等于包括 NaN 在内的任何值

```
console.log(NaN + 3) // NaN
console.log(NaN == NaN); // false
```

**知识点12：**可以使用 isNaN() 函数检测值是否为NaN，对于非数值类型的值，会自动进行类型转换（其他类型转数字查看下文）

```
console.log(isNaN(NaN)); // true
console.log(isNaN(10)); // false，10 是数值
console.log(isNaN("10")); // false，可以转换为数值 10
console.log(isNaN("blue")); // true，不可以转换为数值
console.log(isNaN(true)); // false，可以转换为数值 1
```

###### 4.数值装换

**知识点13：**有 3 个函数可以将非数值转换为数值： Number() 、 parseInt() 和 parseFloat()。 Number() 是
转型函数，可用于任何数据类型。后两个函数主要用于将字符串转换为数值。

>除了这三个函数，使用一元计算符号也可以将非数值转换为数值

**知识点14：**Number() 函数基于如下规则执行转换

* 布尔值， true 转换为 1， false 转换为 0
* 数值，直接返回
* null ，返回 0
* undefined ，返回 NaN 
* 字符串，应用以下规则：
  * 如果字符串包含数值字符，包括数值字符前面带加、减号的情况，则转换为一个十进制数值。
    因此， Number("1") 返回 1， Number("123") 返回 123， Number("011") 返回 11（忽略前面
    的零）。
  * 如果字符串包含有效的浮点值格式如 "1.1" ，则会转换为相应的浮点值（同样，忽略前面的零）。
  * 如果字符串包含有效的十六进制格式如 "0xf" ，则会转换为与该十六进制值对应的十进制整
    数值。
  * 如果是空字符串（不包含字符或包含空字符），则返回 0。
  * 如果字符串包含除上述情况之外的其他字符，则返回 NaN 。
* 对象，调用 valueOf() 方法，并按照上述规则转换返回的值。如果转换结果是 NaN ，则调用
  toString() 方法，再按照转换字符串的规则转换。

**知识点15：**Number()和parseInt()的区别是：

* parseInt()对于空字符串会返回 NaN（Number放回0）
* parseInt的转换会依次检测每个字符，直到字符串末尾，或碰到非数值字符（Number有非数值字符直接返回NaN）

```
let num1 = parseInt("1234blue"); // 1234
```

**知识点16：**parseInt() 函数也能识别不同的整数格式（十进制、八进制、十六进制）,但是不同的数值格式很容易混淆，因此 parseInt() 也接收第二个参数，用于指定底数（进制数）。

```
let num3 = parseInt("0xA"); // 10，解释为十六进制整数
let num = parseInt("0xAF", 16); // 175
```

如果提供了十六进制参数，那么字符串前面的 "0x" 可以省掉：

```
let num1 = parseInt("AF", 16); // 175
let num2 = parseInt("AF"); // NaN
```

>注意：
>
>* 因为不传底数参数相当于让 parseInt() 自己决定如何解析，所以为避免解析出错，建议始终传给
>  它第二个参数
>* 多数情况下解析的应该都是十进制数，此时第二个参数就要传入 10。

**知识点17:**parseFloat()的检测规则：

* parseFloat()跟 parseInt() 函数类似，都是从位置 0 开始检测每个字符。它也是解析到字符串末尾或者解析到一个无效的浮点数值字符为止。所以， "22.34.5" 将转换成 22.34
* parseFloat()解析十进制值，因此不能指定底数（所以十六进制数值始终会返回 0，因为16进制是以0开头）

```
let num2 = parseFloat("0xA"); // 0
```

##### 3.4.6  String 类型

**知识点1:**字符串可以使用双引号（"）、单引号（'）或反引号（`）标示，单双引号这两种方式没有任何区别。不过要注意的是，以某种引号作为字符串开头，必须仍然以该种引号作为字符串结尾

```
let firstName = "John";
let lastName = 'Jacob';
let lastName = `Jingleheimerschmidt`
let firstName = 'Nicholas"; // 语法错误：开头和结尾的引号必须是同一种
```

###### 1. 字符字面量

**知识点2:**字符串数据类型包含一些字符字面量，用于表示非打印字符或有其他用途的字符：

| 字面量               | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| \n                   | 换行                                                         |
| \t                   | 制表                                                         |
| \b                   | 退格                                                         |
| \r                   | 回车                                                         |
| \f                   | 换页                                                         |
| \\\                  | 反斜杠（ \ ）                                                |
| \\'                  | 单引号（ ' ），在字符串以单引号标示时使用，例如 'He said, \'hey.\'' |
| \\"  双引号（ " ）   | 在字符串以双引号标示时使用，例如 "He said,\ \"hey.\\""       |
| \\`  反引号（ ` ）， | 在字符串以反引号标示时使用，例如 \`He said, \`hey.\\``       |
| \xnn                 | 以十六进制编码 nn 表示的字符（其中 n 是十六进制数字 0~F），例如 \x41 等于 "A" |
| \unnnn               | 以十六进制编码 nnnn 表示的 Unicode 字符（其中 n 是十六进制数字 0~F），例如 \u03a3 等于希腊字<br/>符 "Σ" |

**知识点3:**字符串的长度可以通过其 length 属性获取，但对于转义序列，只算一个字符：

```
"this \u03a3.".length   // 7
```

###### 2. 字符串的特点

**知识点4:**字符串是不可变的（immutable），意思是一旦创建，它们的值就不能变了。比如将变量赋值字符串，改变字符串的值，实际上底层是将原来的值销毁，再分配新的空间存放新值然后赋值，而不是在原有的基础上改变。

```
let lang = 'java',
lang = 'javaScript' // 将原有的java销毁，保存javaScript
```

###### 3. 转换为字符串

**知识点5:**有三种方式把一个值转换为字符串。第一种方式是toString()方法。

```
let age = 11;
let ageAsString = age.toString(); // 字符串"11"
let found = true;
let foundAsString = found.toString(); // 字符串"true"
```

**知识点6:**只有 null 和 undefined 值没有 toString() 方法。

**知识点7:**多数情况下， toString() 不接收任何参数。不过，在对数值调用这个方法时， toString() 可以接收一个底数参数。

```
let num = 10;
console.log(num.toString()); // "10"
console.log(num.toString(2)); // "1010"
console.log(num.toString(8)); // "12"
console.log(num.toString(10)); // "10"
console.log(num.toString(16)); // "a"
```

**知识点8:**第二种方式是Stirng()函数， String() 函数遵循如下规则

*  如果值有 toString() 方法，则调用该方法（不传参数）并返回结果。
* 如果值是 null ，返回 "null" 。
* 如果值是 undefined ，返回 "undefined" 。

所以，对于不是null和undifined的数据类型，其本质上还是调用了toString()方法。只是对null和undefined做了处理。

**知识点9:**第三种方式是用加号操作符给一个值加上一个空字符串 ""

```
console.log(true + '') // "string"
```

###### 4. 模板字面量

**知识点10:**ES6新增了使用模板字面量定义字符串的方式（即模板字符串），与使用单引号或双引号不同，模板字面量保留换行字符，可以跨行定义字符串：

```
let myMultiLineString = 'first line\nsecond line';
let myMultiLineTemplateLiteral = `first line
second line`;
console.log(myMultiLineString);
// first line
// second line"
console.log(myMultiLineTemplateLiteral);
// first line
// second line
console.log(myMultiLineString === myMultiLinetemplateLiteral); // true
```

顾名思义，模板字面量在定义模板时特别有用，比如下面这个 HTML 模板：

```
let pageHTML = `
<div>
    <a href="#">
    	<span>Jake</span>
    </a>
</div>`;
```

如果用单/双引号实现相同的效果是这样的：

```
let pageHTML = 
"<div>" +
	"<a href='#'>" +
    	"<span>Jake</span>" +
    "</a>" +
"</div>;"	
```

* 可以看到使用单/双引号的方式相比模板字面量麻烦很多，且最后的结果没有保留换行和空格。

**知识点11:**由于模板字面量会保持反引号内部的空格，因此在使用时要格外注意。格式正确的模板字符串看起
来可能会缩进不当：

```
// 这个模板字面量在换行符之后有 25 个空格符
let myTemplateLiteral = `first line
second line`;
console.log(myTemplateLiteral.length); // 47
// 这个模板字面量以一个换行符开头
let secondTemplateLiteral = `
first line
second line`;
console.log(secondTemplateLiteral[0] === '\n'); // true
// 这个模板字面量没有意料之外的字符
let thirdTemplateLiteral = `first line
second line`;
console.log(thirdTemplateLiteral);
// first line
// second line
```

###### 5. 字符串插值

**知识点12:**模板字面量最常用的一个特性是支持字符串插值，也就是可以在一个连续定义中插入一个或多个
值，字符串插值通过在 ${} 中使用一个 JavaScript 表达式实现：：

```
let value = 5;
let exponent = 'second';
// 以前，字符串插值是这样实现的：
let interpolatedString =
value + ' to the ' + exponent + ' power is ' + (value * value);
// 现在，可以用模板字面量这样实现：
let interpolatedTemplateLiteral =
`${ value } to the ${ exponent } power is ${ value * value }`;
console.log(interpolatedString); // 5 to the second power is 25
console.log(interpolatedTemplateLiteral); // 5 to the second power is 25
```

**知识点13:**所有插入的值都会使用 toString() 强制转型为字符串，而且任何 JavaScript 表达式都可以用于插
值。嵌套的模板字符串无须转义：

```
console.log(`Hello, ${ `World` }!`); // Hello, World!
```

**知识点14:**将表达式转换为字符串时会调用 toString() :

```
let foo = { toString: () => 'World' };
console.log(`Hello, ${ foo }!`); // Hello, World!
```

**知识点15:**在插值表达式中可以调用函数和方法：

```
function capitalize(word) {
	return `${ word[0].toUpperCase() }${ word.slice(1) }`;
}
console.log(`${ capitalize('hello') }, ${ capitalize('world') }!`); // Hello, World!
```

###### 6. 模板字面量标签函数(先略)

###### 7. 原始字符串(先略)

##### 3.4.7  Symbol 类型(先略)

##### 3.4.8  Object 类型(简单介绍)

 **知识点1：**ECMAScript 中的对象其实就是一组数据和功能的集合，开发者可以通过创建Object的实例来创建对象：

```
let o = new Object();
```

（补）但是还有另一种很受欢迎的创建对象的方式

```
let o = {}
```

****

**知识点2:**每个 Object 实例都有如下属性和方法

* constructor ：用于创建当前对象的函数。在前面的例子中，这个属性的值就是 Object()
  函数。
* hasOwnProperty(propertyName) ：用于判断当前对象实例（不是原型）上是否存在给定的属
  性。要检查的属性名必须是字符串（如 o.hasOwnProperty("name") ）或符号。
* isPrototypeOf(object) ：用于判断当前对象是否为另一个对象的原型。（第 8 章将详细介绍
  原型。）
* propertyIsEnumerable(propertyName) ：用于判断给定的属性是否可以使用（本章稍后讨
  论的） for-in 语句枚举。与 hasOwnProperty() 一样，属性名必须是字符串。
* toLocaleString() ：返回对象的字符串表示，该字符串反映对象所在的本地化执行环境。
* toString() ：返回对象的字符串表示。
* valueOf() ：返回对象对应的字符串、数值或布尔值表示。通常与 toString() 的返回值相同。

#### 3.5.操作符

##### 3.5.1 一元操作符

**知识点1:**只操作一个值的操作符叫一元操作符（unary operator）。一元操作符是 ECMAScript中最简单的操作符。

###### 1. 递增/递减操作符

**知识点2:**前/后置递增/递减操作

前置递增/递减：变量的值都会在语句被求值之前改变

```
let age = 29;
let anotherAge = --age + 2;
console.log(age); // 28
console.log(anotherAge); // 30
```

* anotherAge的值为：age先减为28后再与2相加的30

后置递增/递减：变量的值都会在语句被求值之后改变

```
let num1 = 2;
let num2 = 20;
let num3 = num1-- + num2;
let num4 = num1 + num2;
console.log(num3); // 22
console.log(num4); // 21
```

* num3的值为：num1还未减前的2和20相加的22

**知识点3:**递增和递减操作符遵循如下规则：

* 对于字符串，如果是有效的数值形式，则转换为数值再应用改变。变量类型从字符串变成数值。
* 对于字符串，如果不是有效的数值形式，则将变量的值设置为 NaN 。变量类型从字符串变成
  数值。
* 对于布尔值，如果是 false ，则转换为 0 再应用改变。变量类型从布尔值变成数值。
* 对于布尔值，如果是 true ，则转换为 1 再应用改变。变量类型从布尔值变成数值。
* 对于浮点值，加 1 或减 1。
* 如果是对象，则调用其（第 5 章会详细介绍的） valueOf() 方法取得可以操作的值。对得到的
  值应用上述规则。如果是 NaN ，则调用 toString() 并再次应用其他规则。变量类型从对象变成
  数值。

```
let s1 = "2";
let s2 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
    	return -1;
    }
};
s1++; // 值变成数值 3
s2++; // 值变成 NaN
b++; // 值变成数值 1
f--; // 值变成 0.10000000000000009（因为浮点数不精确）
o--; // 值变成-2
```

###### 2.一元加和减

**知识点4:**一元加由一个加号（ + ）表示，放在变量前头，对数值没有任何影响，如果将一元加应用到非数值，则会执行与使用 Number() 转型函数一样的类型转换：

```
let num = 25;
num = +num;
console.log(num); // 25,没影响

let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
    	return -1;
    }
};
s1 = +s1; // 值变成数值 1
s2 = +s2; // 值变成数值 1.1
s3 = +s3; // 值变成 NaN
b = +b; // 值变成数值 0
f = +f; // 不变，还是 1.1
o = +o; // 值变成数值-1
```

**知识点5:**对数值使用一元减会将其变成相应的负值（如上面的例子所示）。在应用到非数值时，一元减会遵
循与一元加同样的规则，先对它们进行转换，然后再取负值：

```
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
    	return -1;
    }
};
s1 = -s1; // 值变成数值-1
s2 = -s2; // 值变成数值-1.1
s3 = -s3; // 值变成 NaN
b = -b; // 值变成数值 0
f = -f; // 变成-1.1
o = -o; // 值变成数值 1
```

* 综上，一元加和减操作符主要用于基本的算术，但也可以用于数据类型转换

##### 3.5.2 位操作符（先略）

##### 3.5.3 布尔操作符

###### 1.逻辑非

逻辑非主要有以下两种用法：

**知识点1:**将操作数取反（先转换为布尔值（转换参考3.4.4），再对其取反）

```
console.log(!false); // true
console.log(!"blue"); // false
console.log(!0); // true
console.log(!NaN); // true
console.log(!""); // true
console.log(!12345); // false
```

**知识点2:**也可以用于把任意值转换为布尔值

```
console.log(!!"blue"); // true
console.log(!!0); // false
console.log(!!NaN); // false
console.log(!!""); // false
console.log(!!12345); // true
```

###### 2.逻辑与

**知识点3:**逻辑与操作符由两个和号（ && ）表示，其操作规则以我的总结是：返回第一个假值，如果第一个不是假值，则返回第二个值（对于不是布尔值，会先转换为布尔值再判断）。

```
let found = true;
let result = (found && someUndeclaredVariable); // 这里会出错
console.log(result); // 不会执行这一行
```

* 第二行因为found为true，执行并返回someUndeclaredVariable，someUndeclaredVariable未定义所以报错。

**其实原书中的对于逻辑与的规则描述是这样的**：

1. 如果第一个操作数是对象，则返回第二个操作数。
2. 如果第二个操作数是对象，则只有第一个操作数求值为 true 才会返回该对象。
3. 如果两个操作数都是对象，则返回第二个操作数。
4. 如果有一个操作数是 null ，则返回 null 。
5. 如果有一个操作数是 NaN ，则返回 NaN 。
6. 如果有一个操作数是 undefined ，则返回 undefined 。

再结合我的总结：

* 如果第一个操作数是对象（转为布尔值不是假值），则返回第二个操作数
* 如果第二个操作数是对象（转为布尔值是真值），则只有第一个操作数求值为 true 才会返回该对象（返回第一个假值）。
* 如果两个操作数都是对象，则返回第二个操作数。（对象都是真值，所以返回第二个值）
* 如果有一个操作数是 null ，则返回 null （null为假值，直接返回）
* 如果有一个操作数是 NaN ，则返回 NaN （NaN 为假值，直接返回）
* 如果有一个操作数是 undefined ，则返回 undefined （undefined 为假值，直接返回）

###### 2.逻辑或

**知识点4:**逻辑或操作符由两个管道符（ || ）表示，其操作规则以我的总结是：返回第一个真值，如果第一个不是真值，则返回第二个（对于不是布尔值，会先转换为布尔值再判断）。

```
let found = false;
let result = (found || someUndeclaredVariable); // 这里会出错
console.log(result); // 不会执行这一行
```

逻辑或和与在工作中的应用非常常见！！！

##### 3.5.3 乘性操作符

###### 1.乘法操作符

**知识点1:**ECMAScript 定义了 3 个乘性操作符：乘法、除法和取模，它们和其他语言对应的操作符作用一样，但在处理非数值时，它们会进行数据转换。

**知识点2:**处理特殊值时也有一些特殊的行为：

* 如果 ECMAScript 不能表示乘积，则返回 Infinity 或-Infinity 。
* 如果有任一操作数是 NaN ，则返回 NaN 
* 如果是 Infinity 乘以 0，则返回 NaN，乘以非0的有限数值，则根据第二个操作数的符号返回 Infinity 或 -Infinity 
* 如果是 Infinity 乘以 Infinity ，则返回 Infinity
* 如果有不是数值的操作数，则先在后台用 Number() 将其转换为数值，然后再应用上述规则

###### 2.除法操作符

**知识点3:**处理特殊值时也有一些特殊的行为：

* 如果ECMAScript不能表示商，则返回 Infinity 或 -Infinity 。
* 如果有任一操作数是 NaN ，则返回 NaN 
* 如果是 Infinity 除以 Infinity ，则返回 NaN 。

* 如果是 0 除以 0，则返回 NaN ， 如果是非 0 的有限值除以 0，则根据第一个操作数的符号返回 Infinity 或 -Infinity 。
* 如果是 Infinity 除以任何数值，则根据第二个操作数的符号返回 Infinity 或 -Infinity 。
* 如果有不是数值的操作数，则先在后台用 Number() 函数将其转换为数值，然后再应用上述规则。

###### 3.取模操作符

**知识点4:处理特殊值时也有一些特殊的行为：**

* 如果操作数是数值，则执行常规除法运算，返回余数