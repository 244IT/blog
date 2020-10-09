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

