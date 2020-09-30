### 前言

2020.9.27收到js宝书第四版，再一次打算跟着新版本系统地学习JS，并将里面的难点和重点做好总结。

### 第一章 什么是JavaScript

#### 1.1 简短的历史回顾（95、96、97）

为了实现客户端来处理表单验证的功能，1995年网景公司的工程师Brendan Eich开发了Mocha（后改为LiveScript）语言。在网景浏览器2发布前又借着Java语言的热度改名为JavaScript。

1996年，微软开发了JScript并发布了IE3，成为网景的竞争对手

两个不同的JavaScript实现显然对于语言本身的发展和开发者都是不利的，所以1997年，ECMA(欧洲计算机制造商协会)打造了ECMAScript这个js语言标准（也可成为js语言规范）

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

### 第二章 





