## 01.JavaScript运行原理

### 1.1.浏览器内核

- Gecko：早期被Netscape和Mozilla Firefox浏览器浏览器使用；
- Trident：微软开发，被IE4~IE11浏览器使用，但是Edge浏览器已经转向Blink；
- Webkit：苹果基于KHTML开发、开源的，用于Safari，Google Chrome之前也在使用；
- Blink：是Webkit的一个分支，Google开发，目前应用于Google Chrome、Edge、Opera等；
- 等等...

事实上，我们经常说的浏览器内核指的是浏览器的排版引擎：

- **排版引擎**（layout engine），也称为**浏览器引擎**（browser engine）、**页面渲染引擎**（rendering engine）或**样版引擎**。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw01HG0j1Yiay9ice38320ibvMu6XibFYJJcT53oH82BV4b6AUvb7kS9uiciaNw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)WebKit main flow

但是在这个执行过程中，HTML解析的时候遇到了JavaScript标签，应该怎么办呢？

- 会停止解析HTML，而去加载和执行JavaScript代码；

当然，为什么不直接异步去加载执行JavaScript代码，而要在这里停止掉呢？

- 这是因为JavaScript代码可以操作我们的DOM；
- 所以浏览器希望将HTML解析的DOM和JavaScript操作之后的DOM放到一起来生成最终的DOM树，而不是频繁的去生成新的DOM树；

那么，JavaScript代码由谁来执行呢？

- JavaScript引擎

### 1.2. JavaScript引擎

为什么需要JavaScript引擎呢？

- 事实上我们编写的JavaScript无论你交给浏览器或者Node执行，最后都是需要被CPU执行的；
- 但是CPU只认识自己的指令集，实际上是机器语言，才能被CPU所执行；
- 所以我们需要JavaScript引擎帮助我们将JavaScript代码翻译成CPU指令来执行；

比较常见的JavaScript引擎有哪些呢？

- **SpiderMonkey**：第一款JavaScript引擎，由Brendan Eich开发（也就是JavaScript作者）；
- **Chakra**：微软开发，用于IT浏览器；
- **JavaScriptCore**：WebKit中的JavaScript引擎，Apple公司开发；
- **V8**：Google开发的强大JavaScript引擎，也帮助Chrome从众多浏览器中脱颖而出；

这里我们先以WebKit为例，WebKit事实上由两部分组成的：

- WebCore：负责HTML解析、布局、渲染等等相关的工作；
- JavaScriptCore：解析、执行JavaScript代码；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0ictqFSK4mQRMq1QlUahGHbqU2JlZctsYoVuH9BTQdowian3fxYBn9NIA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)webkit内核

看到这里，学过小程序的同学有没有感觉非常的熟悉呢？

- 在小程序中编写的JavaScript代码就是被JSCore执行的；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw00EBdcmxxy5ibz4rPm6IEgnL2ac6BdbU9Q8t1plYMYYTGl1wJcFibS3aQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

另外一个强大的JavaScript引擎就是V8引擎。

### 1.3.V8引擎

我们来看一下官方对V8引擎的定义：

- V8是用C ++编写的Google开源高性能JavaScript和WebAssembly引擎，它用于Chrome和Node.js等。
- 它实现ECMAScript和WebAssembly，并在Windows 7或更高版本，macOS 10.12+和使用x64，IA-32，ARM或MIPS处理器的Linux系统上运行。
- V8可以独立运行，也可以嵌入到任何C ++应用程序中。

V8引擎本身的源码非常复杂，大概有超过100w行C++代码，但是我们可以简单了解一下它执行JavaScript代码的原理：

- Parse模块会将JavaScript代码转换成AST（抽象语法树），这是因为解释器并不直接认识JavaScript代码；

- - 如果函数没有被调用，那么是不会被转换成AST的；
  - Parse的V8官方文档：https://v8.dev/blog/scanner

- Ignition是一个解释器，会将AST转换成ByteCode（字节码）

- - 同时会收集TurboFan优化所需要的信息（比如函数参数的类型信息，有了类型才能进行真实的运算）；
  - 如果函数只调用一次，Ignition会执行解释执行ByteCode；
  - Ignition的V8官方文档：https://v8.dev/blog/ignition-interpreter

- TurboFan是一个编译器，可以将字节码编译为CPU可以直接执行的机器码；

- - 如果一个函数被多次调用，那么就会被标记为热点函数，那么就会经过TurboFan转换成优化的机器码，提高代码的执行性能；
  - 但是，机器码实际上也会被还原为ByteCode，这是因为如果后续执行函数的过程中，类型发生了变化（比如sum函数原来执行的是number类型，后来执行变成了string类型），之前优化的机器码并不能正确的处理运算，就会逆向的转换成字节码；
  - TurboFan的V8官方文档：https://v8.dev/blog/turbofan-jit

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0NsdC5lgxmMx4g5IdddzjM3y2EsjflNlXA5hCpouMSgIhjuMuTaK7sQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

上面是JavaScript代码的执行过程，事实上V8的内存回收也是其强大的另外一个原因，这里暂时先不展开讨论：

- Orinoco模块，负责垃圾回收，将程序中不需要的内存回收；
- Orinoco的V8官方文档：https://v8.dev/blog/trash-talk

## 02.邂逅Node.js

### 2.1.Node.js是什么？

官方对Node.js的定义：

- Node.js是一个基于V8 JavaScript引擎的JavaScript运行时环境。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0CVgJr8GbR3yZqwAZc5LoC7L9h7DhJJEvK02OpNeZbK2u7qQZutrJPQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image-20200924193328076

也就是说Node.js基于V8引擎来执行JavaScript的代码，但是不仅仅只有V8引擎：

- 前面我们知道Node.js可以嵌入到任何C ++应用程序中，无论是Chrome还是Node.js，事实上都是嵌入了V8引擎来执行JavaScript代码；
- 但是在Chrome浏览器中，还需要解析、渲染HTML、CSS等相关渲染引擎，另外还需要提供支持浏览器操作的API、浏览器自己的事件循环等；
- 另外，在Node.js中我们也需要进行一些额外的操作，比如文件系统读/写、网络IO、加密、压缩解压文件等操作；

所以，我们可以简单理解规划出Node.js和浏览器的差异：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0QoNkQ5etdCFE2v3Km6xo9Fwt033tJU8DB3ic9icYDWymPaqvzOcehkQw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)Chrome浏览器和Node架构区别

这里也有一份单独的Node.js的架构图：

- 我们编写的JavaScript代码会经过V8引擎，再通过Node.js的Bindings，将任务放到Libuv的事件循环中；
- **libuv**（Unicorn Velociraptor—独角伶盗龙）是使用C语言编写的库；
- libuv提供了事件循环、文件系统读写、网络IO、线程池等等内容；
- 具体内部代码的执行流程，我会在后续专门讲解事件和异步IO的原理中详细讲解；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0icKKjBDq3aSaMLmMjPTY5Ux1QtjsDgiaZTom62yt3hQUlZLc7unJ8Rdw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)What is Node.js? Where, when and how to use it with examples

### 2.2.Node.js可以做什么?

了解了Node.js的架构，那么使用它我们可以做什么呢？

- 前面我们提到，Node.js的出现，真正让Atwood定律变成了现实，Node.js的应用场景也越来越多。

我们可以使用基于Node.js的Electron开发出类似于VSCode这种强大的桌面应用程序。另外前端自动化、模块化打包工具gulp、webpack也是基于Node.js开发和使用的。

Node.js的快速发展也让企业对Node.js技术越来越重视，在前端招聘中通常会对Node.js有一定的要求，特别对于高级前端开发工程师，Node.js更是必不可少的技能：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw09Fg50B0b7JsykNNKngN1MH8PaFKZWYnxOhE6vT5bv4J7vuYLR0HTGw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)前端工程师岗位需求

目前前端开发的库都是以node包的形式进行管理；

- npm、yarn工具成为前端开发使用最多的工具；
- 越来越多的公司使用Node.js作为web服务器开发；
- 大量项目需要借助Node.js完成前后端渲染的同构应用；
- 资深前端工程师需要为项目编写脚本工具（前端工程师编写脚本通常会使用JavaScript，而不是Python或者shell）；
- 很多企业在使用Electron来开发桌面应用程序；

总结一下，目前Node.js到底有哪些应用场景呢？

- 前后端页面渲染

- - 支持项目同构开发
  - 对于需要进行首屏优化、SEO的页面进行后端渲染

- 开发命令行工具

- - webpack、gulp等都是基于Node
  - 开发自己独立的命令行工具（类似于shell、Python做的事情，对于前端更加友好）

- 桌面应用的开发

- - 类似于VSCode这种强大的桌面应用
  - 甚至开发桌面端类似于wayward大型游戏

- 进行服务器开发

- - 拥有类似express、koa等强大的web框架
  - 开发Web Socket等服务器

所以，作为前端开发工程师，Node.js已经是我们必须掌握的核心技术。

### 2.3.Node.js的安装

Node.js是在2009年诞生的，目前最新的版本是分别是12.18.4以及14.12.0：

- LTS版本：相对稳定一些，推荐线上环境使用该版本；
- Current版本：最新的Node版本，包含很多新特性；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0xlb6z8syRPdY3icwZYunKxGibLO3gIyibRicz5S4ORp8e8OpybYHKnBxJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)node的版本

这些我们选择什么版本呢？

- 如果你是学习使用，可以选择current版本；
- 如果你是公司开发，建议选择LTS版本；

Node的安装方式有很多：

- 可以借助于一些操作系统上的软件管理工具，比如Mac上的homebrew，Linux上的yum、dnf等；
- 也可以直接下载对应的安装包下载安装；

我们选择下载安装，下载自己操作系统的安装包直接安装就可以了：

- window选择.msi安装包，Mac选择.pkg安装包，Linux会在后续部署中讲解；
- 安装过程中会配置环境变量；
- 并且安装node过程中会安装npm（*Node Package Manager*）工具；

### 2.4.Node.js版本管理

在实际开发学习中，我们只需要使用一个Node版本来开发或者学习即可。但是，如果你希望通过可以快速更新或切换多个版本时，可以借助于一些工具：

* nvm：Node Version Manager
* n：Interactively Manage Your Node.js Versions（交互式管理你的Node.js版本）

这里我演示管理工具：n

- n是TJ方便node的版本管理，专门开发的；
- 官方介绍是：n -  Interactively Manage Your Node.js Versions（交互式管理你的Node.js版本）

安装n：直接使用npm安装即可

```
# 安装工具n
npm install -g n
# 查看安装的版本
n --version
```

安装最新的lts版本：

- 前面添加的sudo是权限问题；
- 可以两个版本都安装，之后我们可以通过n快速在两个版本间切换；

```
# 安装最新的lts版本
n lts

# 安装最新的版本
n latest
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw01l41FBS8sDZYclR0cTB6FCuQJjptRzc3xoLwajFriaJiaVmrRIEiaO7PQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)安装lts版本

查看所有的版本，并且选择要使用的版本：

- 可以上下选择想使用的版本

```
# 查看所有的版本
n
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0aoBbgIUcG1bzUgn2eHd5hbw5LUoFH9e9eTPib940eA7dP9d7kcosGJQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)查看和选择所有的版本

查看当前Node的版本：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusHFTAWoQP8JvEX0wfuorw0gOicVkLUIgr0IHSXMSV6X9eiabSRQ3Bz0mCoGQRwHFpOMHJbREKJww5A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)查看当前的版本

问题：这两个工具都不支持window

- n：n is not supported natively on Windows.
- nvm：nvm does not support Windows

### 2.5.window中使用nvm

**1.安装**

1. 安装num-windows安装包https://github.com/coreybutler/nvm-windows/releases

**2.使用**

1. 查看当前安装过的node版本：`nvm list`
2. 查看可用的node版本：`nvm list avilable`
3. 安装指定版本：`nvm install 版本号`
4. 安装最新版：`nvm install latest`
5. 安装最新的lts版本：`nvm install lts`
6. 使用指定版本：`nvm use 版本号`
7. 卸载指定版本：`nvm uninstall 版本号`

**3.设置淘宝镜像**

1. `nvm node_mirror https://npm.taobao.org/mirror/node/`
2. `nvm npm_mirror https://npm.taobao.org/mirror/npm/`

## 03.Node基础知识

### 2.1.Node执行代码

如果我们编写一个js文件，里面存放JavaScript代码，如何来执行它呢？

```
// 1.直接打印一段文字
console.log("我是一段JavaScript代码");

// 2.定义一个函数, 调用这个函数
function sum(num1, num2) {
  return num1 + num2;
}

const result = sum(20, 30);
console.log("计算结果:", result);

// 3.执行定时器代码
setTimeout(() => {
  console.log("2s后执行的代码");
}, 2000);
```

目前我们知道有两种方式可以执行：

- 将代码交给浏览器执行；
- 将代码载入到node环境中执行；

**演练一：浏览器执行**

如果我们希望把代码交给浏览器执行：

- 需要通过让浏览器加载、解析html代码，所以我们需要创建一个html文件；
- 在html中通过script标签，引入js文件；
- 当浏览器遇到script标签时，就会根据src加载、执行JavaScript代码；

index.html文件：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  
  <script src="./index.js"></script>
</body>
</html>
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRlNL5icuqPZcTYuUQYWibfyZRXz2Cq0WqSLjUpxYE2AWP6SyyUnib0FeYQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)浏览器执行结果

**演练二：Node执行**

如果我们希望把js文件交给node执行：

- 首先电脑上需要安装Node.js环境，安装过程中会自动配置环境变量；
- 可以通过终端命令`node js文件`的方式来载入和执行对应的js文件；

```
node index.js
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRzR7meXTMFsdHTU1eFlyicnoB1VG3a1qKPibgDOUkbTFX3kneicL6tIqXw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Node执行结果

### 2.2.Node的REPL

什么是REPL呢？感觉挺高大上

- **REPL**是**Read-Eval-Print Loop**的简称，翻译为**“读取-求值-输出”循环**；
- REPL是一个简单的，交互式的编程环境；

事实上，我们浏览器的console就可以看成一个REPL：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMR8BuCVR5suuVk032TuLibVmfaib3KT0AUFNqx9zibcZe7Lxru8zjjia9LbQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)浏览器控制台

Node也给我们提供了一个REPL环境，我们可以在其中演练简单的代码：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRg5C61IZdYw43ODX4OMcTlB9oKSO49mUrfyGfiaILQ50QG9YkQBIMhUQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)REPL演练

### 2.3.Node输入输出

#### 2.3.1.给node程序传递参数

正常情况下执行一个node程序，直接跟上我们对应的文件即可：

```
node index.js
```

但是，在某些情况下执行node程序的过程中，我们可能希望给node传递一些参数：

```
node index.js env=development coderwhy
```

如果我们这样来使用程序，就意味着我们需要在程序中获取到传递的参数：

- 获取参数其实是在`process`的内置对象中的；

如果我们直接打印这个内置对象，它里面包含特别的信息：

- 其他的一些信息，比如版本、操作系统等大家可以自行查看，后面用到一些其他的我们还会提到；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRdWHgg3yOGUCQaXfrHa7gNxSDXCjjgcsicKv2wjVSIHoMU0q5UrEiaWcw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)process对象

现在，我们先找到其中的argv属性：

- 我们发现它是一个数组，里面包含了我们需要的参数；
- 你可能有个疑问，为什么叫argv呢？

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRANq96ASLOyrFibKXET5ExBFG8otgsvibaVE21nKnn3S6VAibF9ADMyDrQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)argv属性

在C/C++程序中的main函数中，实际上可以获取到两个参数：

- argc：argument counter的缩写，传递参数的个数；

- argv：argument vector的缩写，传入的具体参数。

- - vector翻译过来是矢量的意思，在程序中表示的是一种数据结构。
  - 在C++、Java中都有这种数据结构，是一种数组结构；
  - 在JavaScript中也是一个数组，里面存储一些参数信息；

我们可以在代码中，将这些参数信息遍历出来，使用：

```
// 获取参数
console.log(process.argv);
process.argv.forEach(item => {
  console.log(item);
});

// 结果如下：
// /usr/local/bin/node
// /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/02_给Node传递参数/index.js
// ENV=dev
// coderwhy
```

#### 2.3.2.node程序输出内容

**console.log**

最常用的输入内容的方式：console.log

```
console.log("hello coderwhy");
```

**console.clear**

清空控制台：console.clear

```
console.clear
```

**console.trace**

打印函数的调用栈：console.trace

```
function test() {
  demo();
}

function demo() {
  foo();
}

function foo() {
  console.trace();
}

test();
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRyGNr0G8ibXhF45uFd21ibbUUqt1Rps93MEZRUbZOrZnZ97mXLyV9QJxA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)console.trace结果

还有一些其他的方法，其他的一些console方法，可以自己在下面学习研究一下。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRVMicUFTWQeVPdJ97a7ZlRvfjCdh97lNLiaYiay6EOTwZoRT6dVs7cv41A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.4.全局对象

#### 2.4.1.常见全局对象

Node中给我们提供了一些全局对象，方便我们进行一些操作：

- 这些全局对象，我们并不需要从一开始全部一个个学习；
- 某些全局对象并不常用，某些全局对象我们会在后续学习中讲到；
- 比如module、exports、require()会在模块化中讲到；
- 比如Buffer后续会专门讲到；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRXTARF9ib7YIxlbzKvpwib1CEVSibJQshOFQDEFaej1wO3m921N9spwpSw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**process对象**

process提供了Node进程中相关的信息：

- 比如Node的运行环境、参数信息等；
- 后面在项目中，我也会讲解，如何将一些环境变量读取到 `process` 的 `env` 中；

```
console.log(process);
```

**console对象**

提供了简单的调试控制台，在前面讲解输入内容时已经学习过了。

- 更加详细的查看官网文档：https://nodejs.org/api/console.html

**定时器函数**

在Node中使用定时器有好几种方式：

- `setTimeout(callback, delay[, ...args])`：`callback`在`delay`毫秒后执行一次；

- `setInterval(callback, delay[, ...args])`：`callback`每`delay`毫秒重复执行一次；

- `setImmediate(callback[, ...args])`：`callback`I / O事件后的回调的“立即”执行；

- - 这里先不展开讨论它和`setTimeout(callback, 0)`之间的区别；
  - 因为它涉及到事件循环的阶段问题，我会在后续详细讲解事件循环相关的知识；

- `process.nextTick(callback[, ...args])`：添加到下一次tick队列中；

- - 具体的讲解，也放到事件循环中说明；

代码演练：

- 暂时不用关心执行顺序问题，在后续事件循环中我会讲到；

```
setTimeout(() => {
  console.log("setTimtout");
}, 1000);

setInterval(() => {
  console.log('setInterval');
}, 1000);

setImmediate(() => {
  console.log("setImmediate");
});

process.nextTick(() => {
  console.log("process.nextTick");
});
```

当然，它们有对应的取消定时器的方法：

- clearTimeout(timeoutObject);
- clearInterval(intervalObject);
- clearImmediate(immediateObject)

**global对象**

global是一个全局对象，事实上前端我们提到的process、console、setTimeout等都有被放到global中：

```
console.log(process);
console.log(global.process);
```

为什么结果是一样的呢？

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMRF0kic1mxontL0K2Yg10mlCPG5RHSgWIqAXg8Njre0kRNtfOke81bchQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)Node的源码

global中还有哪些属性呢？

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutHOrSK1pdcFibeiaWpZ3rmMR5XPKUUyx8cSDxqzRWOrk7kS0QMrL4zsK4Yy3HDuqc4ttI2eMa4fXLA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)global其他属性的查看

**window和global的区别是什么？**

在浏览器中，全局变量都是在window上的，比如有document、setInterval、setTimeout、alert、console等等

在Node中，我们也有一个global属性，并且看起来它里面有很多其他对象。

但是在浏览器中执行的JavaScript代码，如果我们在顶级范围内通过var定义的一个属性，默认会被添加到window对象上：

```
var name = 'coderwhy';
console.log(window.name); // coderwhy
```

但是在node中，我们通过var定义一个变量，它只是在当前模块中有一个变量，不会放到全局中：

```
var name = 'coderwhy';
console.log(global.name); // undefined
```

#### 2.4.2.特殊的全局对象

为什么我称之为特殊的全局对象呢？

- 这些全局对象可以在模块中任意使用，但是在命令行交互中是不可以使用的；
- 包括：__dirname、__filename、exports、module、require()

**__dirname**

获取当前文件所在的路径：

- 注意：不包括后面的文件名

```
console.log(__dirname);

// /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/03_常见的全局变量
```

**__filename**

获取当前文件所在的路径和文件名称：

- 注意：包括后面的文件名称

```
console.log(__filename);
// /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/03_常见的全局变量/global对象.js
```



## 04.彻底掌握前端模块化

### 4.1. 什么是模块化开发

#### 4.1.1 JavaScript设计缺陷

那么，到底什么是模块化开发呢？

- 事实上模块化开发最终的目的是将程序划分成一个个小的结构；
- 这个结构中编写属于自己的逻辑代码，有自己的作用域，不会影响到其他的结构；
- 这个结构可以将自己希望暴露的变量、函数、对象等导出给其结构使用；
- 也可以通过某种方式，导入另外结构中的变量、函数、对象等；

**上面说提到的结构，就是模块；**

**按照这种结构划分开发程序的过程，就是模块化开发的过程；**

无论你多么喜欢JavaScript，以及它现在发展的有多好，我们都需要承认在*Brendan Eich*用了10天写出JavaScript的时候，它都有很多的缺陷：

- 比如var定义的变量作用域问题；
- 比如JavaScript的面向对象并不能像常规面向对象语言一样使用class；
- 比如JavaScript没有模块化的问题；

*Brendan Eich*本人也多次承认过JavaScript设计之初的缺陷，但是随着JavaScript的发展以及标准化，存在的缺陷问题基本都得到了完善。

- JavaScript目前已经得到了快速的发展，无论是web、移动端、小程序端、服务器端、桌面应用都被广泛的使用；

在网页开发的早期，*Brendan Eich*开发JavaScript仅仅作为一种脚本语言，做一些简单的表单验证或动画实现等，那个时候代码还是很少的：

- 这个时候我们只需要讲JavaScript代码写到<script>标签中即可；
- 并没有必要放到多个文件中来编写；

```
<button id="btn">按钮</button>

<script>
  document.getElementById("btn").onclick = function() {
    console.log("按钮被点击了");
  }
</script>
```

但是随着前端和JavaScript的快速发展，JavaScript代码变得越来越复杂了：

- ajax的出现，前后端开发分离，意味着后端返回数据后，我们需要通过JavaScript进行前端页面的渲染；
- SPA的出现，前端页面变得更加复杂：包括前端路由、状态管理等等一系列复杂的需求需要通过JavaScript来实现；
- 包括Node的实现，JavaScript编写复杂的后端程序，没有模块化是致命的硬伤；

所以，模块化已经是JavaScript一个非常迫切的需求：

- 但是JavaScript本身，直到ES6（2015）才推出了自己的模块化方案；
- 在此之前，为了让JavaScript支持模块化，涌现出了很多不同的模块化规范：AMD、CMD、CommonJS等；

在这个章节，我们将详细学习JavaScript的模块化，尤其是CommonJS和ES6的模块化。

### 4.2. 没有模块化的问题

我们先来简单体会一下没有模块化代码的问题。

我们知道，对于一个大型的前端项目，通常是多人开发的（即使一个人开发，也会将代码划分到多个文件夹中）：

- 我们假设有两个人：小明和小丽同时在开发一个项目，并且会将自己的JavaScript代码放在一个单独的js文件中。

小明开发了aaa.js文件，代码如下（当然真实代码会复杂的多）：

```
var flag = true;

if (flag) {
  console.log("aaa的flag为true")
}
```

小丽开发了bbb.js文件，代码如下：

```
var flag = false;

if (!flag) {
  console.log("bbb使用了flag为false");
}
```

很明显出现了一个问题：

- 大家都喜欢使用flag来存储一个boolean类型的值；
- 但是一个人赋值了true，一个人赋值了false；
- 如果之后都不再使用，那么也没有关系；

但是，小明又开发了ccc.js文件：

```
if (flag) {
  console.log("使用了aaa的flag");
}
```

问题来了：小明发现ccc中的flag值不对

- 对于聪明的你，当然一眼就看出来，是小丽将flag赋值为了false；
- 但是如果每个文件都有上千甚至更多的代码，而且有上百个文件，你可以一眼看出来flag在哪个地方被修改了吗？

备注：引用路径如下：

```
<script src="./aaa.js"></script>
<script src="./bbb.js"></script>
<script src="./ccc.js"></script>
```

所以，没有模块化对于一个大型项目来说是灾难性的。

**当然，我们有办法可以解决上面的问题：立即函数调用表达式（IIFE）**

- **IIFE** (Immediately Invoked Function Expression)

aaa.js

```
const moduleA = (function () {
  var flag = true;

  if (flag) {
    console.log("aaa的flag为true")
  }

  return {
    flag: flag
  }
})();
```

bbb.js

```
const moduleB = (function () {
  var flag = false;

  if (!flag) {
    console.log("bbb使用了flag为false");
  }
})();
```

ccc.js

```
const moduleC = (function() {
  const flag = moduleA.flag;
  if (flag) {
    console.log("使用了aaa的flag");
  }
})();
```

命名冲突的问题，有没有解决呢？解决了。

但是，我们其实带来了新的问题：

- 第一，我必须记得每一个模块中返回对象的命名，才能在其他模块使用过程中正确的使用；
- 第二，代码写起来混乱不堪，每个文件中的代码都需要包裹在一个匿名函数中来编写；
- 第三，在没有合适的规范情况下，每个人、每个公司都可能会任意命名、甚至出现模块名称相同的情况；

**所以，我们会发现，虽然实现了模块化，但是我们的实现过于简单，并且是没有规范的。**

- 我们需要制定一定的规范来约束每个人都按照这个规范去编写模块化的代码；
- 这个规范中应该包括核心功能：模块本身可以导出暴露的属性，模块又可以导入自己需要的属性；

JavaScript社区为了解决上面的问题，涌现出一系列好用的规范，接下来我们就学习具有代表性的一些规范。

### 4.3. CommonJS规范

#### 4.3.1. CommonJS和Node

我们需要知道CommonJS是一个规范，最初提出来是在浏览器意外的地方使用，并且当时被命名为**ServerJS**，后来为了体现它的广泛性，修改为**CommonJS**，平时我们也会简称为CJS。

- Node是CommonJS在服务器端一个具有代表性的实现；
- Browserify是CommonJS在浏览器中的一种实现；
- webpack打包工具具备对CommonJS的支持和转换（后面我会讲到）；

所以，Node中对CommonJS进行了支持和实现，让我们在开发node的过程中可以方便的进行模块化开发：

- 在Node中每一个js文件都是一个单独的模块；
- 这个模块中包括CommonJS规范的核心变量：exports、module.exports、require；
- 我们可以使用这些变量来方便的进行模块化开发；

前面我们提到过模块化的核心是导出和导入，Node中对其进行了实现：

- exports和module.exports可以负责对模块中的内容进行导出；
- require函数可以帮助我们导入其他模块（自定义模块、系统模块、第三方库模块）中的内容；

#### 4.3.2. Node模块化开发

我们来看一下两个文件：

bar.js

```
const name = 'coderwhy';
const age = 18;

function sayHello(name) {
  console.log("Hello " + name);
}
```

main.js

```
console.log(name);
console.log(age);

sayHello('kobe');
```

上面的代码会报错：

- 在node中每一个文件都是一个独立的模块，有自己的作用域；
- 那么，就意味着别的模块main中不能随便访问另外一个模块bar中的内容；
- bar需要导出自己想要暴露的变量、函数、对象等等；
- main从bar中导入自己想要使用的变量、函数、对象等等；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kiboje8hwm1puPMdA3bP3sUYp9LA7m7LpVibnl8ACF7EyTwLgJEtoiadNV6tmQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)导出和导入

##### 1. exports导出

**强调：exports是一个对象，我们可以在这个对象中添加很多个属性，添加的属性会导出**

bar.js中导出内容：

```
exports.name = name;
exports.age = age;
exports.sayHello = sayHello;
```

main.js中导入内容：

```
const bar = require('./bar');
```

上面这行代码意味着什么呢？

- 意味着main中的bar变量等于exports对象；

```
main中的bar = bar中的exports
```

所以，我可以编写下面的代码：

```
const bar = require('./bar');

const name = bar.name;
const age = bar.age;
const sayHello = bar.sayHello;

console.log(name);
console.log(age);

sayHello('kobe');
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojewT2wSgxhOcSHdyzZWicejEY7dYHuicgm9AzH1jtLibH0U9Loj7ZTVFbKw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)模块之间的引用关系

为了进一步论证，bar和exports是同一个对象：

- 所以，bar对象是exports对象的浅拷贝；
- 浅拷贝的本质就是一种引用的赋值而已；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeicBuAibNeFXU9qkIauXYDOMsE7QCUxZesYWFouOBC7SQatrZpb4lUoEA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)定时器修改对象

##### 2.module.exports

但是Node中我们经常导出东西的时候，又是通过module.exports导出的：

- module.exports和exports有什么关系或者区别呢？

我们追根溯源，通过维基百科中对CommonJS规范的解析：

- CommonJS中是没有module.exports的概念的；
- 但是为了实现模块的导出，Node中使用的是Module的类，每一个模块都是Module的一个实例，也就是module；
- 所以在Node中真正用于导出的其实根本不是exports，而是module.exports；
- 因为module才是导出的真正实现者；

但是，为什么exports也可以导出呢？

- 这是因为module对象的exports属性是exports对象的一个引用；
- 也就是说 `module.exports = exports = main中的bar`；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeib966pL7iazrlQAJFO14AUekibxu8JF2MHxUrVoX0csXo1nJMjZeWOjmw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image-20201011163653515

注意：真正导出的模块内容的核心其实是module.exports，只是为了实现CommonJS的规范，刚好module.exports对exports对象有一个引用而已；

那么，如果我的代码这样修改了：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeE1wEXS3pNL4evJyQ0ia6lF5nx0aH7Lc6CQichwyWtoOKUlcBqIM8qOHQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image-20201011164006266

你能猜到内存中会有怎么样的表现吗？

- 结论：和exports对象没有任何关系了，exports你随便玩自己的吧；
- module.exports我现在导出一个自己的对象，不带着你玩了；
- 新的对象取代了exports对象的导出，那么就意味着require导入的对象是新的对象；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeQeOBuV3g4Gv6td12x74GguP2uS3Ze7FeHtWWLN3FM5o4j18yB3Bkiaw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image-20201011164223607

##### 3. require细节

我们现在已经知道，require是一个函数，可以帮助我们引入一个文件（模块）中导入的对象。

那么，require的查找规则是怎么样的呢？

- https://nodejs.org/dist/latest-v14.x/docs/api/modules.html#modules_all_together

**这里我总结比较常见的查找规则：**

导入格式如下：require(X)

- 情况一：X是一个核心模块，比如path、http

- - 直接返回核心模块，并且停止查找

- 情况二：X是以 `./` 或 `../` 或 `/`（根目录）开头的

- - 查找目录下面的index文件
  - 1> 查找X/index.js文件
  - 2> 查找X/index.json文件
  - 3> 查找X/index.node文件
  - 1.如果有后缀名，按照后缀名的格式查找对应的文件
  - 2.如果没有后缀名，会按照如下顺序：
  - 1> 直接查找文件X
  - 2> 查找X.js文件
  - 3> 查找X.json文件
  - 4> 查找X.node文件
  - 第一步：将X当做一个文件在对应的目录下查找；
  - 第二步：没有找到对应的文件，将X作为一个目录
  - 如果没有找到，那么报错：`not found`

- 情况三：直接是一个X（没有路径），并且X不是一个核心模块

- - 比如 `/Users/coderwhy/Desktop/Node/TestCode/04_learn_node/05_javascript-module/02_commonjs/main.js`中编写 `require('why')`
  - ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeccY2Bics4UStoH4FzQrWyibQ4JPiaQayWNXn7ph55CpDPSNmeVXb0Jxyg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)查找顺序
  - 如果上面的路径中都没有找到，那么报错：`not found`

##### 4. 模块加载顺序

这里我们研究一下模块的加载顺序问题。

**结论一：模块在被第一次引入时，模块中的js代码会被运行一次**

aaa.js

```
const name = 'coderwhy';

console.log("Hello aaa");

setTimeout(() => {
  console.log("setTimeout");
}, 1000);
```

main.js

```
const aaa = require('./aaa');
```

aaa.js中的代码在引入时会被运行一次

**结论二：模块被多次引入时，会缓存，最终只加载（运行）一次**

main.js

```
const aaa = require('./aaa');
const bbb = require('./bbb');
```

aaa.js

```
const ccc = require("./ccc");
```

bbb.js

```
const ccc = require("./ccc");
```

ccc.js

```
console.log('ccc被加载');
```

ccc中的代码只会运行一次。

**为什么只会加载运行一次呢？**

- 这是因为每个模块对象module都有一个属性：loaded。
- 为false表示还没有加载，为true表示已经加载；

**结论三：如果有循环引入，那么加载顺序是什么？**

如果出现下面模块的引用关系，那么加载顺序是什么呢？

- 这个其实是一种数据结构：图结构；
- 图结构在遍历的过程中，有深度优先搜索（DFS, depth first search）和广度优先搜索（BFS, breadth first search）；
- Node采用的是深度优先算法：main -> aaa -> ccc -> ddd -> eee ->bbb

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeicyjvLkriaru3ojNlKRmvS2JxaSFMSc2FBKKwYarUJo4yg4gswJiaR3pA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)多个模块的引入关系

#### 4.3.3. Node的源码解析

Module类

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeY4IPWs3EMyKOMWKeM6j67YYqCxEBibXBPr88jK58OMNW4vAiaj6sWAnw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)Module类

Module.prototype.require函数

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kiboje0sX4zUA3vz40Ozw8qcUhylACJpZPhEXeZrKboRMRgQpwhCpZibWbceg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)require函数

Module._load函数

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeEjMHCucTVCC7q8WcQNmShUiaKWkFvkLSccd6HjJdM2MVkyVwrDoff8Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)_load函数的实现

### 4.4. AMD和CMD规范

#### 4.4.1. CommonJS规范缺点

CommonJS加载模块是同步的：

- 同步的意味着只有等到对应的模块加载完毕，当前模块中的内容才能被运行；
- 这个在服务器不会有什么问题，因为服务器加载的js文件都是本地文件，加载速度非常快；

如果将它应用于浏览器呢？

- 浏览器加载js文件需要先从服务器将文件下载下来，之后在加载运行；
- 那么采用同步的就意味着后续的js代码都无法正常运行，即使是一些简单的DOM操作；

所以在浏览器中，我们通常不使用CommonJS规范：

- 当然在webpack中使用CommonJS是另外一回事；
- 因为它会将我们的代码转成浏览器可以直接执行的代码；

在早期为了可以在浏览器中使用模块化，通常会采用AMD或CMD：

- 但是目前一方面现代的浏览器已经支持ES Modules，另一方面借助于webpack等工具可以实现对CommonJS或者ES Module代码的转换；
- AMD和CMD已经使用非常少了，所以这里我们进行简单的演练；

#### 4.4.2. AMD规范

AMD主要是应用于浏览器的一种模块化规范：

- AMD是Asynchronous Module Definition（异步模块定义）的缩写；
- 它采用的是异步加载模块；
- 事实上AMD的规范还要早于CommonJS，但是CommonJS目前依然在被使用，而AMD使用的较少了；

我们提到过，规范只是定义代码的应该如何去编写，只有有了具体的实现才能被应用：

- AMD实现的比较常用的库是require.js和curl.js；

**这里我们以require.js为例讲解：**

第一步：下载require.js

- 下载地址：https://github.com/requirejs/requirejs
- 找到其中的require.js文件；

第二步：定义HTML的script标签引入require.js和定义入口文件：

- data-main属性的作用是在加载完src的文件后会加载执行该文件

```
<script src="./lib/require.js" data-main="./index.js"></script>
```

第三步：编写如下目录和代码

```
├── index.html
├── index.js
├── lib
│   └── require.js
└── modules
    ├── bar.js
    └── foo.js
```

index.js

```
(function() {
  require.config({
    baseUrl: '',
    paths: {
      foo: './modules/foo',
      bar: './modules/bar'
    }
  })
 
  // 开始加载执行foo模块的代码
  require(['foo'], function(foo) {

  })
})();
```

modules/bar.js

- 如果一个模块不依赖其他，那么直接使用define(function)即可

```
define(function() {
  const name = "coderwhy";
  const age = 18;
  const sayHello = function(name) {
    console.log("Hello " + name);
  }

  return {
    name,
    age, 
    sayHello
  }
})
```

modules/foo.js

```
define(['bar'], function(bar) {
  console.log(bar.name);
  console.log(bar.age);
  bar.sayHello('kobe');
})
```

#### 4.4.3. CMD规范

CMD规范也是应用于浏览器的一种模块化规范：

- CMD 是Common Module Definition（通用模块定义）的缩写；
- 它也采用了异步加载模块，但是它将CommonJS的优点吸收了过来；
- 但是目前CMD使用也非常少了；

CMD也有自己比较优秀的实现方案：

- SeaJS

**我们一起看一下SeaJS如何使用：**

第一步：下载SeaJS

- 下载地址：https://github.com/seajs/seajs
- 找到dist文件夹下的sea.js

第二步：引入sea.js和使用主入口文件

- `seajs`是指定主入口文件的

```
<script src="./lib/sea.js"></script>
<script>
  seajs.use('./index.js');
</script>
```

第三步：编写如下目录和代码

```
├── index.html
├── index.js
├── lib
│   └── sea.js
└── modules
    ├── bar.js
    └── foo.js
```

index.js

```
define(function(require, exports, module) {
  const foo = require('./modules/foo');
})
```

bar.js

```
define(function(require, exports, module) {
  const name = 'lilei';
  const age = 20;
  const sayHello = function(name) {
    console.log("你好 " + name);
  }

  module.exports = {
    name,
    age,
    sayHello
  }
})
```

foo.js

```
define(function(require, exports, module) {
  const bar = require('./bar');

  console.log(bar.name);
  console.log(bar.age);
  bar.sayHello("韩梅梅");
})
```

### 4.5. ES Module

#### 4.5.1. 认识ES Module

JavaScript没有模块化一直是它的痛点，所以才会产生我们前面学习的社区规范：CommonJS、AMD、CMD等，所以在ES推出自己的模块化系统时，大家也是兴奋异常。

ES Module和CommonJS的模块化有一些不同之处：

- 一方面它使用了import和export关键字；
- 另一方面它采用编译期静态类型检测，并且动态引用的方式；

ES Module模块采用export和import关键字来实现模块化：

- export负责将模块内的内容导出；
- import负责从其他模块导入内容；

了解：采用ES Module将自动采用严格模式：`use strict`

- 如果你不熟悉严格模式可以简单看一下MDN上的解析；
- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode

#### 4.5.2. ES Module的使用

##### 1.代码结构组件

这里我在浏览器中演示ES6的模块化开发：

代码结构如下：

```
├── index.html
├── main.js
└── modules
    └── foo.js
```

index.html中引入两个js文件作为模块：

```
<script src="./modules/foo.js" type="module"></script>
<script src="main.js" type="module"></script>
```

如果直接在浏览器中运行代码，会报如下错误：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeeWRicwIysicCm4m69Kfd8FFfaEcWuRdSV4spX0zrGLzX51KCgZ6ibELPg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)模块化运行

这个在MDN上面有给出解释：

- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules
- 你需要注意本地测试 — 如果你通过本地加载Html 文件 (比如一个 `file://` 路径的文件), 你将会遇到 CORS 错误，因为Javascript 模块安全性需要。
- 你需要通过一个服务器来测试。

我这里使用的VSCode，VSCode中有一个插件：Live Server

- 通过插件运行，可以将我们的代码运行在一个本地服务中；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeUG7186yJvYjzRXmzl3NFJg46oIEJibAPpbJvyBmycic4YF91qVfibuW7A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image-20201012153439900

#### 4.5.3. export关键字

export关键字将一个模块中的变量、函数、类等导出；

foo.js文件中默认代码如下：

```
const name = 'coderwhy';
const age = 18;
let message = "my name is why";

function sayHello(name) {
  console.log("Hello " + name);
}
```

**我们希望将其他中内容全部导出，它可以有如下的方式：**

方式一：在语句声明的前面直接加上export关键字

```
export const name = 'coderwhy';
export const age = 18;
export let message = "my name is why";

export function sayHello(name) {
  console.log("Hello " + name);
}
```

方式二：将所有需要导出的标识符，放到export后面的 `{}`中

- 注意：这里的 `{}`里面不是ES6的对象字面量的增强写法，`{}`也不是表示一个对象的；
- 所以：`export {name: name}`，是错误的写法；

```
const name = 'coderwhy';
const age = 18;
let message = "my name is why";

function sayHello(name) {
  console.log("Hello " + name);
}

export {
  name,
  age,
  message,
  sayHello
}
```

方式三：导出时给`标识符`起一个别名

```
export {
  name as fName,
  age as fAge,
  message as fMessage,
  sayHello as fSayHello
}
```

#### 4.5.4. import关键字

import关键字负责从另外一个模块中导入内容

**导入内容的方式也有多种：**

方式一：`import {标识符列表} from '模块'`；

- 注意：这里的`{}`也不是一个对象，里面只是存放导入的标识符列表内容；

```
import { name, age, message, sayHello } from './modules/foo.js';

console.log(name)
console.log(message);
console.log(age);
sayHello("Kobe");
```

方式二：导入时给标识符起别名

```
import { name as wName, age as wAge, message as wMessage, sayHello as wSayHello } from './modules/foo.js';
```

方式三：将模块功能放到一个模块功能对象（a module object）上

```
import * as foo from './modules/foo.js';

console.log(foo.name);
console.log(foo.message);
console.log(foo.age);
foo.sayHello("Kobe");
```

#### 4.5.5. export和import结合

如果从一个模块中导入的内容，我们希望再直接导出出去，这个时候可以直接使用export来导出。

bar.js中导出一个sum函数：

```
export const sum = function(num1, num2) {
  return num1 + num2;
}
```

foo.js中导入，但是只是做一个中转：

```
export { sum } from './bar.js';
```

main.js直接从foo中导入：

```
import { sum } from './modules/foo.js';
console.log(sum(20, 30));
```

甚至在foo.js中导出时，我们可以变化它的名字

```
export { sum as barSum } from './bar.js';
```

为什么要这样做呢？

- 在开发和封装一个功能库时，通常我们希望将暴露的所有接口放到一个文件中；
- 这样方便指定统一的接口规范，也方便阅读；
- 这个时候，我们就可以使用export和import结合使用；

#### 4.5.6. default用法

前面我们学习的导出功能都是有名字的导出（named exports）：

- 在导出export时指定了名字；
- 在导入import时需要知道具体的名字；

还有一种导出叫做默认导出（default export）

- 默认导出export时可以不需要指定名字；
- 在导入时不需要使用 `{}`，并且可以自己来指定名字；
- 它也方便我们和现有的CommonJS等规范相互操作；

导出格式如下：

```
export default function sub(num1, num2) {
  return num1 - num2;
}
```

导入格式如下：

```
import sub from './modules/foo.js';

console.log(sub(20, 30));
```

注意：在一个模块中，只能有一个默认导出（default export）；

#### 4.5.7. import()

通过import加载一个模块，是不可以在其放到逻辑代码中的，比如：

```
if (true) {
  import sub from './modules/foo.js';
}
```

为什么会出现这个情况呢？

- 这是因为ES Module在被JS引擎解析时，就必须知道它的依赖关系；
- 由于这个时候js代码没有任何的运行，所以无法在进行类似于if判断中根据代码的执行情况；
- 甚至下面的这种写法也是错误的：因为我们必须到运行时能确定path的值；

```
const path = './modules/foo.js';

import sub from path;
```

但是某些情况下，我们确确实实希望动态的来加载某一个模块：

- 如果根据不懂的条件，动态来选择加载模块的路径；
- 这个时候我们需要使用 `import()` 函数来动态加载；

aaa.js模块：

```
export function aaa() {
  console.log("aaa被打印");
}
```

bbb.js模块：

```
export function bbb() {
  console.log("bbb被执行");
}
```

main.js模块：

```
let flag = true;
if (flag) {
  import('./modules/aaa.js').then(aaa => {
    aaa.aaa();
  })
} else {
  import('./modules/bbb.js').then(bbb => {
    bbb.bbb();
  })
}
```

### 4.3. ES Module的原理

#### 4.3.1. ES Module和CommonJS的区别

**CommonJS模块加载js文件的过程是运行时加载的，并且是同步的：**

- 运行时加载意味着是js引擎在执行js代码的过程中加载 模块；
- 同步的就意味着一个文件没有加载结束之前，后面的代码都不会执行；

```
console.log("main代码执行");

const flag = true;
if (flag) {
  // 同步加载foo文件，并且执行一次内部的代码
  const foo = require('./foo');
  console.log("if语句继续执行");
}
```

**CommonJS通过module.exports导出的是一个对象：**

- 导出的是一个对象意味着可以将这个对象的引用在其他模块中赋值给其他变量；
- 但是最终他们指向的都是同一个对象，那么一个变量修改了对象的属性，所有的地方都会被修改；

**ES Module加载js文件的过程是编译（解析）时加载的，并且是异步的：**

- 编译时（解析）时加载，意味着import不能和运行时相关的内容放在一起使用：

- - 比如from后面的路径需要动态获取；
  - 比如不能将import放到if等语句的代码块中；
  - 所以我们有时候也称ES Module是静态解析的，而不是动态或者运行时解析的；

- 异步的意味着：JS引擎在遇到`import`时会去获取这个js文件，但是这个获取的过程是异步的，并不会阻塞主线程继续执行；

- - 也就是说设置了 `type=module` 的代码，相当于在script标签上也加上了 `async` 属性；
  - 如果我们后面有普通的script标签以及对应的代码，那么ES Module对应的js文件和代码不会阻塞它们的执行；

```
<script src="main.js" type="module"></script>
<!-- 这个js文件的代码不会被阻塞执行 -->
<script src="index.js"></script>
```

**ES Module通过export导出的是变量本身的引用：**

- export在导出一个变量时，js引擎会解析这个语法，并且创建**模块环境记录**（module environment record）；
- **模块环境记录**会和变量进行 `绑定`（binding），并且这个绑定是实时的；
- 而在导入的地方，我们是可以实时的获取到绑定的最新值的；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeezXibRWtBBwGrUqbiceysIwmIBaWxjhEc1QAOOr0MSjCbOJTkKhP5dfw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)export和import绑定的过程

**所以我们下面的代码是成立的：**

bar.js文件中修改

```
let name = 'coderwhy';

setTimeout(() => {
  name = "湖人总冠军";
}, 1000);

setTimeout(() => {
  console.log(name);
}, 2000);

export {
  name
}
```

main.js文件中获取

```
import { name } from './modules/bar.js';

console.log(name);

// bar中修改, main中验证
setTimeout(() => {
  console.log(name);
}, 2000);
```

但是，下面的代码是不成立的：main.js中修改

```
import { name } from './modules/bar.js';

console.log(name);

// main中修改, bar中验证
setTimeout(() => {
  name = 'kobe';
}, 1000);
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojelXdJWbQtsL9dkFfVickXCPiclf8icow980G4sPwpITsbzuEYaZS1VPIoA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)导入的变量不可以被修改

思考：如果bar.js中导出的是一个对象，那么main.js中是否可以修改对象中的属性呢？

- 答案是可以的，因为他们指向同一块内存空间；（自己编写代码验证，这里不再给出）

#### 4.3.2. Node中支持 ES Module

**在Current版本中**

在最新的Current版本（v14.13.1）中，支持es module我们需要进行如下操作：

- 方式一：在package.json中配置 `type: module`（后续再学习，我们现在还没有讲到package.json文件的作用）
- 方式二：文件以 `.mjs` 结尾，表示使用的是ES Module；

这里我们暂时选择以 `.mjs` 结尾的方式来演练：

bar.mjs

```
const name = 'coderwhy';

export {
  name
}
```

main.mjs

```
import { name } from './modules/bar.mjs';

console.log(name);
```

**在LTS版本中**

在最新的LST版本（v12.19.0）中，我们也是可以正常运行的，但是会报一个警告：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojefTf4B6uacj7YUH5BD1IKh5IzA1DcedJ5ia6Y1Wnul1HCo9fVCWjMdRQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)lts版本的警告

#### 4.3.3. ES Module和CommonJS的交互

**CommonJS加载ES Module**

结论：通常情况下，CommonJS不能加载ES Module

- 因为CommonJS是同步加载的，但是ES Module必须经过静态分析等，无法在这个时候执行JavaScript代码；
- 但是这个并非绝对的，某些平台在实现的时候可以对代码进行针对性的解析，也可能会支持；
- Node当中是不支持的；

**ES Module加载CommonJS**

结论：多数情况下，ES Module可以加载CommonJS

- ES Module在加载CommonJS时，会将其module.exports导出的内容作为default导出方式来使用；
- 这个依然需要看具体的实现，比如webpack中是支持的、Node最新的Current版本也是支持的；
- 但是在最新的LTS版本中就不支持；

foo.js

```
const address = 'foo的address';

module.exports = {
  address
}
```

main.js

```
import foo from './modules/foo.js';
console.log(foo.address);
```

## 05.常见的内置模块

### 5.1. 内置模块path

#### 5.1.1. 认识path模块

path模块用于对路径和文件进行处理，提供了很多好用的方法。

并且我们知道在Mac OS、Linux和window上的路径是不一样的

- window上会使用 `\`或者 `\\` 来作为文件路径的分隔符，当然目前也支持 `/`；
- 在Mac OS、Linux的Unix操作系统上使用 `/` 来作为文件路径的分隔符；

那么如果我们在window上使用 `\` 来作为分隔符开发了一个应用程序，要部署到Linux上面应该怎么办呢？

- 显示路径会出现一些问题；
- 所以为了屏蔽他们之间的差异，在开发中对于路径的操作我们可以使用 `path` 模块；

#### 5.1.2. path常见的API

**从路径中获取信息**

- dirname：获取文件的父文件夹；
- basename：获取文件名；
- extname：获取文件扩展名；

```
const path = require("path");

const myPath = '/Users/coderwhy/Desktop/Node/课堂/PPT/01_邂逅Node.pdf';

const dirname = path.dirname(myPath);
const basename = path.basename(myPath);
const extname = path.extname(myPath);

console.log(dirname); // /Users/coderwhy/Desktop/Node/课堂/PPT
console.log(basename); // 01_邂逅Node.pdf
console.log(extname); // .pdf
```

**路径的拼接**

- 如果我们希望将多个路径进行拼接，但是不同的操作系统可能使用的是不同的分隔符；
- 这个时候我们可以使用`path.join`函数；

```
console.log(path.join('/user', 'why', 'abc.txt'));
```

**将文件和某个文件夹拼接**

- 如果我们希望将某个文件和文件夹拼接，可以使用 `path.resolve`;

- - `resolve`函数会判断我们拼接的路径前面是否有 `/`或`../`或`./`；
  - 如果有表示是一个绝对路径，会返回对应的拼接路径；
  - 如果没有，那么会和当前执行文件所在的文件夹进行路径的拼接

```
path.resolve('abc.txt'); // /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/06_常见的内置模块/02_文件路径/abc.txt
path.resolve('/abc.txt'); // /abc.txt
path.resolve('/User/why', 'abc.txt'); // /User/why/abc.txt
path.resolve('User/why', 'abc.txt'); // /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/06_常见的内置模块/02_文件路径/User/why/abc.txt
```

resolve其实我们在webpack中也会使用：

```
const CracoLessPlugin = require('craco-less');
const path = require("path");

const resolve = dir => path.resolve(__dirname, dir);

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    }
  ],
  webpack: {
    alias: {
      "@": resolve("src"),
      "components": resolve("src/components")
    }
  }
}
```

### 5.2. 内置模块fs

#### 5.2.1. 认识fs模块

fs是File System的缩写，表示文件系统。

对于任何一个为服务器端服务的语言或者框架通常都会有自己的文件系统：

- 因为服务器需要将各种数据、文件等放置到不同的地方；
- 比如用户数据可能大多数是放到数据库中的（后面我们也会学习）；
- 比如某些配置文件或者用户资源（图片、音视频）都是以文件的形式存在于操作系统上的；

Node也有自己的文件系统操作模块，就是fs：

- 借助于Node帮我们封装的文件系统，我们可以在任何的操作系统（window、Mac OS、Linux）上面直接去操作文件；
- 这也是Node可以开发服务器的一大原因，也是它可以成为前端自动化脚本等热门工具的原因；

Node文件系统的API非常的多：https://nodejs.org/dist/latest-v14.x/docs/api/fs.html

- 我们不可能，也没必要一个个去学习；
- 这个更多的应该是作为一个API查询的手册，等用到的时候查询即可；
- 学习阶段我们只需要学习最常用的即可；

但是这些API大多数都提供三种操作方式：

- 方式一：同步操作文件：代码会被阻塞，不会继续执行；
- 方式二：异步回调函数操作文件：代码不会被阻塞，需要传入回调函数，当获取到结果时，回调函数被执行；
- 方式三：异步Promise操作文件：代码不会被阻塞，通过 `fs.promises` 调用方法操作，会返回一个Promise，可以通过then、catch进行处理；

**我们这里以获取一个文件的状态为例：**

- 注意：都需要引入 `fs` 模块；

方式一：同步操作文件

```
// 1.方式一: 同步读取文件
const state = fs.statSync('../foo.txt');
console.log(state);

console.log('后续代码执行');
```

方式二：异步回调函数操作文件

```
// 2.方式二: 异步读取
fs.stat("../foo.txt", (err, state) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log(state);
})
console.log("后续代码执行");
```

方式三：异步Promise操作文件

```
// 3.方式三: Promise方式
fs.promises.stat("../foo.txt").then(state => {
  console.log(state);
}).catch(err => {
  console.log(err);
})
console.log("后续代码执行");
```

后续代码演练中，我将以异步回调的方式演练：相对更通用一些；

#### 5.2.2. 文件描述符

文件描述符（File descriptors）是什么呢？

在 POSIX 系统上，对于每个进程，内核都维护着一张当前打开着的文件和资源的表格。

- 每个打开的文件都分配了一个称为文件描述符的简单的数字标识符。
- 在系统层，所有文件系统操作都使用这些文件描述符来标识和跟踪每个特定的文件。
- Windows 系统使用了一个虽然不同但概念上类似的机制来跟踪资源。
- 为了简化用户的工作，Node.js 抽象出操作系统之间的特定差异，并为所有打开的文件分配一个数字型的文件描述符。

`fs.open()` 方法用于分配新的文件描述符。一旦被分配，则文件描述符可用于从文件读取数据、向文件写入数据、或请求关于文件的信息。

```
// 获取文件描述符
fs.open("../foo.txt", 'r', (err, fd) => {
  console.log(fd);

  fs.fstat(fd, (err, state) => {
    console.log(state);
  })
})
```

#### 5.2.3.文件的读写

如果我们希望对文件的内容进行操作，这个时候可以使用文件的读写：

- `fs.readFile(path[, options], callback)`：读取文件的内容；
- `fs.writeFile(file, data[, options], callback)`：在文件中写入内容；

文件写入：

```
fs.writeFile('../foo.txt', content, {}, err => {
  console.log(err);
})
```

在上面的代码中，你会发现有一个大括号没有填写任何的内容，这个是写入时填写的option参数：

- flag：写入的方式。
- encoding：字符的编码；

我们先来看flag：

- flag的值有很多：https://nodejs.org/dist/latest-v14.x/docs/api/fs.html#fs_file_system_flags

- - `w` 打开文件写入，默认值；
  - `w+`打开文件进行读写，如果不存在则创建文件；
  - `r+` 打开文件进行读写，如果不存在那么抛出异常；
  - `r`打开文件读取，读取时的默认值；
  - `a`打开要写入的文件，将流放在文件末尾。如果不存在则创建文件；
  - `a+`打开文件以进行读写，将流放在文件末尾。如果不存在则创建文件

我们再来看看编码：

- 我之前在简书上写过一篇关于字符编码的文章：https://www.jianshu.com/p/899e749be47c
- 目前基本用的都是UTF-8编码；

文件读取：

- 如果不填写encoding，返回的结果是Buffer；

```
fs.readFile('../foo.txt', {encoding: 'utf-8'}, (err, data) => {
  console.log(data);
})
```

文件读取：

```
const fs = require('fs');

fs.readFile('../foo.txt', {encoding: 'utf-8'}, (err, data) => {
  console.log(data);
})
```

#### 5.2.3.文件夹操作

**新建一个文件夹**

使用`fs.mkdir()`或`fs.mkdirSync()`创建一个新文件夹：

```
const fs = require('fs');

const dirname = '../why';

if (!fs.existsSync(dirname)) {
  fs.mkdir(dirname, (err) => {
    console.log(err);
  })
}
```

**获取文件夹的内容**

```
// 读取文件夹
function readFolders(folder) {
  fs.readdir(folder, {withFileTypes: true} ,(err, files) => {
    files.forEach(file => {
      if (file.isDirectory()) {
        const newFolder = path.resolve(dirname, file.name);
        readFolders(newFolder);
      } else {
        console.log(file.name);
      }
    })
  })
}

readFolders(dirname);
```

**文件重命名**

```
fs.rename('../why', '../coder', err => {
  console.log(err);
})
```

### 5.3. 内置模块events

#### 5.3.1. 基本使用

Node中的核心API都是基于异步事件驱动的：

- 在这个体系中，某些对象（发射器（Emitters））发出某一个事件；
- 我们可以监听这个事件（监听器 Listeners），并且传入的回调函数，这个回调函数会在监听到事件时调用；

发出事件和监听事件都是通过EventEmitter类来完成的，它们都属于events对象。

- `emitter.on(eventName, listener)`：监听事件，也可以使用`addListener`；
- `emitter.off(eventName, listener)`：移除事件监听，也可以使用`removeListener`；
- `emitter.emit(eventName[, ...args])`：发出事件，可以携带一些参数；

```
const EventEmmiter = require('events');

// 监听事件
const bus = new EventEmmiter();

function clickHanlde(args) {
  console.log("监听到click事件", args);
}

bus.on("click", clickHanlde);

setTimeout(() => {
  bus.emit("click", "coderwhy");
  bus.off("click", clickHanlde);
  bus.emit("click", "kobe");
}, 2000);
```

#### 5.3.1. 常见的属性

EventEmitter的实例有一些属性，可以记录一些信息：

- `emitter.eventNames()`：返回当前 `EventEmitter对象`注册的事件字符串数组；
- `emitter.getMaxListeners()`：返回当前 `EventEmitter对象`的最大监听器数量，可以通过`setMaxListeners()`来修改，默认是10；
- `emitter.listenerCount(事件名称)`：返回当前 `EventEmitter对象`某一个事件名称，监听器的个数；
- `emitter.listeners(事件名称)`：返回当前 `EventEmitter对象`某个事件监听器上所有的监听器数组；

```
console.log(bus.eventNames());
console.log(bus.getMaxListeners());
console.log(bus.listenerCount("click"));
console.log(bus.listeners("click"));
```

#### 5.3.1.  方法的补充

`emitter.once(eventName, listener)`：事件监听一次

```
const EventEmitter = require('events');

const emitter = new EventEmitter();

emitter.once('click', (args) => {
  console.log("监听到事件", args);
})

setTimeout(() => {
  emitter.emit('click', 'coderwhy');
  emitter.emit('click', 'coderwhy');
}, 2000);
```

`emitter.prependListener()`：将监听事件添加到最前面

```
emitter.on('click', (args) => {
  console.log("a监听到事件", args);
})

// b监听事件会被放到前面
emitter.prependListener("click", (args) => {
  console.log("b监听到事件", args);
})
```

`emitter.prependOnceListener()`：将监听事件添加到最前面，但是只监听一次

```
emitter.prependOnceListener("click", (args) => {
  console.log("c监听到事件", args);
})
```

`emitter.removeAllListeners([eventName])`：移除所有的监听器

```
// 移除emitter上的所有事件监听
emitter.removeAllListeners();
// 移除emitter上的click事件监听
emitter.removeAllListeners("click");
```

## 06.npm你不知道的细节

### 6.1. 包管理工具

#### 6.1.1. 认识npm

我们已经学习了在JavaScript中可以通过模块化的方式将代码划分成一个个小的结构：

- 在以后的开发中我们就可以通过模块化的方式来封装自己的代码，并且封装成一个工具；
- 这个工具我们可以让同事通过导入的方式来使用，甚至你可以分享给世界各地的程序员来使用；

**如果我们分享给世界上所有的程序员使用，有哪些方式呢？**

方式一：上传到GitHub上、其他程序员通过GitHub下载我们的代码手动的引用；

- 缺点是大家必须知道你的代码GitHub的地址，并且从GitHub上手动下载；
- 需要在自己的项目中手动的引用，并且管理相关的依赖；
- 不需要使用的时候，需要手动来删除相关的依赖；
- 当遇到版本升级或者切换时，需要重复上面的操作；

显然，上面的方式是有效的，但是这种传统的方式非常麻烦，并且容易出错；

方式二：使用一个专业的工具来管理我们的代码

- 我们通过工具将代码发布到特定的位置；
- 其他程序员直接通过工具来安装、升级、删除我们的工具代码；

显然，通过第二种方式我们可以更好的管理自己的工具包，其他人也可以更好的使用我们的工具包。

包管理工具npm：

- Node Package Manager，也就是Node包管理器；
- 但是目前已经不仅仅是Node包管理器了，在前端项目中我们也在使用它来管理依赖的包；
- 比如express、koa、react、react-dom、axios、babel、webpack等等；

npm管理的包可以在哪里查看、搜索呢？

- https://www.npmjs.com/
- 这是我们安装相关的npm包的官网；

npm管理的包存放在哪里呢？

- 我们发布自己的包其实是发布到registry上面的；
- 当我们安装一个包时其实是从registry上面下载的包；

#### 6.1.2. 项目配置文件

事实上，我们每一个项目都会有一个对应的配置文件，无论是前端项目还是后端项目：

- 这个配置文件会记录着你项目的名称、版本号、项目描述等；
- 也会记录着你项目所依赖的其他库的信息和依赖库的版本号；

这个配置文件在Node环境下面（无论是前端还是后端）就是package.json。

我们以vue cli4脚手架创建的项目为例：

```
{
  "name": "my-vue",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "core-js": "^3.6.5",
    "vue": "^2.6.11"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^6.2.2",
    "vue-template-compiler": "^2.6.11"
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead"
  ]
}
```

事实上Vue ClI4脚手架创建的项目相对进行了简化，我们来看一下CLI2创建的项目：

```
{
  "name": "vuerouterbasic",
  "version": "1.0.0",
  "description": "A Vue.js project",
  "author": "'coderwhy' <'coderwhy@gmail.com'>",
  "private": true,
  "scripts": {
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "build": "node build/build.js"
  },
  "dependencies": {
    "vue": "^2.5.2",
    "vue-router": "^3.0.1"
  },
  "devDependencies": {
    "autoprefixer": "^7.1.2",
    "babel-core": "^6.22.1",
    "babel-helper-vue-jsx-merge-props": "^2.0.3",
    "babel-loader": "^7.1.1",
    "babel-plugin-syntax-jsx": "^6.18.0",
    "babel-plugin-transform-runtime": "^6.22.0",
    "babel-plugin-transform-vue-jsx": "^3.5.0",
    "babel-preset-env": "^1.3.2",
    "babel-preset-stage-2": "^6.22.0",
    "chalk": "^2.0.1",
    "copy-webpack-plugin": "^4.0.1",
    "css-loader": "^0.28.0",
    "extract-text-webpack-plugin": "^3.0.0",
    "file-loader": "^1.1.4",
    "friendly-errors-webpack-plugin": "^1.6.1",
    "html-webpack-plugin": "^2.30.1",
    "node-notifier": "^5.1.2",
    "optimize-css-assets-webpack-plugin": "^3.2.0",
    "ora": "^1.2.0",
    "portfinder": "^1.0.13",
    "postcss-import": "^11.0.0",
    "postcss-loader": "^2.0.8",
    "postcss-url": "^7.2.1",
    "rimraf": "^2.6.0",
    "semver": "^5.3.0",
    "shelljs": "^0.7.6",
    "uglifyjs-webpack-plugin": "^1.1.1",
    "url-loader": "^0.5.8",
    "vue-loader": "^13.3.0",
    "vue-style-loader": "^3.0.1",
    "vue-template-compiler": "^2.5.2",
    "webpack": "^3.6.0",
    "webpack-bundle-analyzer": "^2.9.0",
    "webpack-dev-server": "^2.9.1",
    "webpack-merge": "^4.1.0"
  },
  "engines": {
    "node": ">= 6.0.0",
    "npm": ">= 3.0.0"
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not ie <= 8"
  ]
}
```

我们也可以手动创建一个package.json文件：

```
npm init #创建时填写信息
npm init -y # 所有信息使用默认的
```

`npm init -y`生成文件的效果：

```
{
  "name": "learn-npm",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

我们会发现属性非常的多，我们这里对一些常见属性进行一些解析。

**必须填写的属性：name、version**

- name是项目的名称；
- version是当前项目的版本号；
- description是描述信息，很多时候是作为项目的基本描述；
- author是作者相关信息（发布时用到）；
- license是开源协议（发布时用到）；

**private属性：**

- private属性记录当前的项目是否是私有的；
- 当值为true时，npm是不能发布它的，这是防止私有项目或模块发布出去的方式；

**main属性：**

- 设置程序的入口。

- 很多人会有疑惑，webpack不是会自动找到程序的入口吗？

- - 这个入口和webpack打包的入口并不冲突；
  - 它是在你发布一个模块的时候会用到的；
  - 比如我们使用axios模块 `const axios = require('axios');`
  - 实际上是找到对应的main属性查找文件的；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutp13xTTeLSEf3gnNgqpmiblicnvVDK7acaTgJS9KKJccQ1WjnBamLVg2eKmbNu5z41oaoBbThehoicg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)axios的入口

**scripts属性**

- scripts属性用于配置一些脚本命令，以键值对的形式存在；

- 配置后我们可以通过 `npm run 命令的key`来执行这个命令；

- `npm start`和`npm run start`的区别是什么？

- - 它们是等价的；
  - 对于常用的 start、 test、stop、restart可以省略掉run直接通过 `npm start`等方式运行；

**dependencies属性**

- dependencies属性是指定无论开发环境还是生成环境都需要依赖的包；
- 通常是我们项目实际开发用到的一些库模块；
- 与之对应的是devDependencies；

**devDependencies属性**

- 一些包在生成环境是不需要的，比如webpack、babel等；
- 这个时候我们会通过 `npm install webpack --save-dev`，将它安装到devDependencies属性中；

疑问：那么在生成环境如何保证不安装这些包呢？

- 生成环境不需要安装时，我们需要通过 `npm install --production` 来安装文件的依赖；

**版本管理的问题**

我们会发现安装的依赖版本出现：`^2.0.3`或`~2.0.3`，这是什么意思呢？

npm的包通常需要遵从semver版本规范：

- semver：https://semver.org/lang/zh-CN/
- npm semver：https://docs.npmjs.com/misc/semver

semver版本规范是X.Y.Z：

- X主版本号（major）：当你做了不兼容的 API 修改（可能不兼容之前的版本）；
- Y次版本号（minor）：当你做了向下兼容的功能性新增（新功能增加，但是兼容之前的版本）；
- Z修订号（patch）：当你做了向下兼容的问题修正（没有新功能，修复了之前版本的bug）；

我们这里解释一下 ^和~的区别：

- `^x.y.z`：表示x是保持不变的，y和z永远安装最新的版本；
- `~x.y.z`：表示x和y保持不变的，z永远安装最新的版本；

**engines属性**

- engines属性用于指定Node和NPM的版本号；
- 在安装的过程中，会先检查对应的引擎版本，如果不符合就会报错；
- 事实上也可以指定所在的操作系统 `"os" : [ "darwin", "linux" ]`，只是很少用到；

**browserslist属性**

- 用于配置打包后的JavaScript浏览器的兼容情况，参考；
- 否则我们需要手动的添加polyfills来让支持某些语法；
- 也就是说它是为webpack等打包工具服务的一个属性（这里不是详细讲解webpack等工具的工作原理，所以不再给出详情）；

### 6.2. npm工具解析

#### 6.2.1. npm install命令

安装npm包分两种情况：

- 全局安装（global install）：`npm install yarn -g`;
- 项目（局部）安装（local install）：`npm install`

**全局安装**

全局安装是直接将某个包安装到全局：

比如yarn的全局安装：

```
npm install yarn -g
```

但是很多人对全局安装有一些误会：

- 通常使用npm全局安装的包都是一些工具包：yarn、webpack等；
- 并不是类似于 axios、express、koa等库文件；
- 所以全局安装了之后并不能让我们在所有的项目中使用 axios等库；

**项目安装**

项目安装会在当前目录下生产一个 `node_modules` 文件夹，我们之前讲解require查找顺序时有讲解过这个包在什么情况下被查找；

局部安装分为开发时依赖和生产时依赖：

```
# 安装开发和生产依赖
npm install axios --save
npm install axios -S
npm install axios
npm i axios

# 开发者
npm install axios --save-dev
npm install axios -D
npm i axios -D
```

#### 6.2.2. npm install原理

很多同学之情应该已经会了 `npm install `，但是你是否思考过它的内部原理呢？

- 执行 `npm install`它背后帮助我们完成了什么操作？
- 我们会发现还有一个成为package-lock.json的文件，它的作用是什么？
- 从npm5开始，npm支持缓存策略（来自yarn的压力），缓存有什么作用呢？

这是一幅我画出的根据 `npm install` 的原理图：

- npm install会检测是有package-lock.json文件：

- - 检测lock中包的版本是否和package.json中一致（会按照semver版本规范检测）；
  - 一致的情况下，会去优先查找缓存
  - 查找到，会获取缓存中的压缩文件，并且将压缩文件解压到node_modules文件夹中；
  - 不一致，那么会重新构建依赖关系，直接会走顶层的流程；
  - 没有找到，会从registry仓库下载，直接走顶层流程；
  - 分析依赖关系，这是因为我们可能包会依赖其他的包，并且多个包之间会产生相同依赖的情况；
  - 从registry仓库中下载压缩包（如果我们设置了镜像，那么会从镜像服务器下载压缩包）；
  - 获取到压缩包后会对压缩包进行缓存（从npm5开始有的）；
  - 将压缩包解压到项目的node_modules文件夹中（前面我们讲过，require的查找顺序会在该包下面查找）
  - 没有lock文件
  - 有lock文件

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXutp13xTTeLSEf3gnNgqpmiblKOmfSCkaUr8XicHJE3ic92lhsURmXhXuhFuSyXn3l9ApYVQHD8azxJEg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)npm install原理图

package-lock.json文件：

```
{
  "name": "learn-npm",
  "version": "1.0.0",
  "lockfileVersion": 1,
  "requires": true,
  "dependencies": {
    "axios": {
      "version": "0.20.0",
      "resolved": "https://registry.npmjs.org/axios/-/axios-0.20.0.tgz",
      "integrity": "sha512-ANA4rr2BDcmmAQLOKft2fufrtuvlqR+cXNNinUmvfeSNCOF98PZL+7M/v1zIdGo7OLjEA9J2gXJL+j4zGsl0bA==",
      "requires": {
        "follow-redirects": "^1.10.0"
      }
    },
    "follow-redirects": {
      "version": "1.13.0",
      "resolved": "https://registry.npmjs.org/follow-redirects/-/follow-redirects-1.13.0.tgz",
      "integrity": "sha512-aq6gF1BEKje4a9i9+5jimNFIpq4Q1WiwBToeRK5NvZBd/TRsmW8BsJfOEGkr76TbOyPVD3OVDN910EcUNtRYEA=="
    }
  }
}
```

package-lock.json文件解析：

- name：项目的名称；

- version：项目的版本；

- lockfileVersion：lock文件的版本；

- requires：使用requires来跟着模块的依赖关系；

- dependencies：项目的依赖

- - version表示实际安装的axios的版本；
  - resolved用来记录下载的地址，registry仓库中的位置；
  - requires记录当前模块的依赖；
  - integrity用来从缓存中获取索引，再通过索引去获取压缩包文件；
  - 当前项目依赖axios，但是axios依赖follow-redireacts；
  - axios中的属性如下：

#### 6.2.3. 其他npm命令

我们这里再介绍几个比较常用的：

卸载某个依赖包：

```
npm uninstall package
npm uninstall package --save-dev
npm uninstall package -D
```

强制重新build

```
npm rebuild
```

清除缓存

```
npm cache clean
```

npm的命令其实是非常多的：

- https://docs.npmjs.com/cli-documentation/cli
- 更多的命令，可以根据需要查阅官方文档

#### 6.2.4. yarn和cnpm

另一个node包管理工具yarn：

- yarn是由Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具；
- yarn 是为了弥补 npm 的一些缺陷而出现的；
- 早期的npm存在很多的缺陷，比如安装依赖速度很慢、版本依赖混乱等等一系列的问题；
- 虽然从npm5版本开始，进行了很多的升级和改进，但是依然很多人喜欢使用yarn；

这里给出一张常用命令的对比

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)npm和yarn常用命令对比

**补充：cnpm**

由于一些特殊的原因，某些情况下我们没办法很好的从 `https://registry.npmjs.org`下载下来一些需要的包。

查看npm镜像：

```
npm config get registry # npm config get registry
```

我们可以直接设置npm的镜像：

```
npm config set registry https://registry.npm.taobao.org
```

但是对于大多数人来说（比如我），并不希望将npm镜像修改了：

- 第一，不太希望随意修改npm原本从官方下来包的渠道；
- 第二，担心某天淘宝的镜像挂了或者不维护了，又要改来改去；

这个时候，我们可以使用cnpm，并且将cnpm设置为淘宝的镜像：

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm config get registry # https://r.npm.taobao.org/
```

**补充：npx**

npx是npm5.2之后自带的一个命令。

npx的作用非常多，但是比较常见的是使用它来调用项目中的某个模块的指令。

我们以webpack为例：

- 全局安装的是webpack5.1.3
- 项目安装的是webpack3.6.0

如果我在终端执行 `webpack --version`使用的是哪一个命令呢？

- 显示结果会是 `webpack 5.1.3`，事实上使用的是全局的，为什么呢？
- 原因非常简单，在当前目录下找不到webpack时，就会去全局找，并且执行命令；

那么如何使用项目（局部）的webpack，常见的是两种方式：

- 方式一：明确查找到node_module下面的webpack
- 方式二：在 `scripts`定义脚本，来执行webpack；

方式一：在终端中使用如下命令（在项目根目录下）

```
./node_modules/.bin/webpack --version
```

方式二：修改package.json中的scripts

```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "webpack": "webpack --version"
  },
```

终端中执行：

```
npm run webpack
```

但是这两种方式都有一点点麻烦，更好的办法是直接使用npx：

```
npx webpack --version
```

npx的原理非常简单，它会到当前目录的node_modules/.bin目录下查找对应的命令；

#### 6.2.5.nrm

nrm是npm的镜像源管理工具，很多使用我们使用npm下载国外的源文件速度太慢，可以使用nrm快速地在npm镜像源之间进行切换。

```
npm install -g nrm
```

```
nrm ls // 查看可选源
nrm test taobao // 测试源响应时间
nrm use taobeo // 切换源
nrm add imooc https://xxx // 增加定制源
nrm del imooc // 删除定制源

```



## 07.Buffer的使用

### 7.1. 认识Buffer

#### 7.1.1. 数据的二进制

计算机中所有的内容：文字、数字、图片、音频、视频最终都会使用二进制来表示。

JavaScript可以直接去处理非常直观的数据：比如字符串，我们通常展示给用户的也是这些内容。

不对啊，JavaScript不是也可以处理图片吗？

- 事实上在网页端，图片我们一直是交给浏览器来处理的；
- JavaScript或者HTML，只是负责告诉浏览器一个图片的地址；
- 浏览器负责获取这个图片，并且最终将这个图片渲染出来；

但是对于服务器来说是不一样的：

- 服务器要处理的本地文件类型相对较多;
- 比如某一个保存文本的文件并不是使用 `utf-8`进行编码的，而是用 `GBK`，那么我们必须读取到他们的二进制数据，再通过GKB转换成对应的文字；
- 比如我们需要读取的是一张图片数据（二进制），再通过某些手段对图片数据进行二次的处理（裁剪、格式转换、旋转、添加滤镜），Node中有一个Sharp的库，就是读取图片或者传入图片的Buffer对其再进行处理；
- 比如在Node中通过TCP建立长连接，TCP传输的是字节流，我们需要将数据转成字节再进行传入，并且需要知道传输字节的大小（客服端需要根据大小来判断读取多少内容）；

我们会发现，对于前端开发来说，通常很少会和二进制打交道，但是对于服务器端为了做很多的功能，我们必须直接去操作其二进制的数据；

所以Node为了可以方便开发者完成更多功能，提供给了我们一个类Buffer，并且它是全局的。

#### 7.1.2. Buffer和二进制

我们前面说过，Buffer中存储的是二进制数据，那么到底是如何存储呢？

- 我们可以将Buffer看成是一个存储二进制的数组；
- 这个数组中的每一项，可以保存8位二进制：`00000000`

为什么是8位呢？

- 在计算机中，很少的情况我们会直接操作一位二进制，因为一位二进制存储的数据是非常有限的；
- 所以通常会将8位合在一起作为一个单元，这个单元称之为一个字节（byte）；
- 也就是说 `1byte = 8bit`，`1kb=1024byte`，`1M=1024kb`;
- 比如很多编程语言中的int类型是4个字节，long类型是8个字节；
- 比如TCP传输的是字节流，在写入和读取时都需要说明字节的个数；
- 比如RGB的值分别都是255，所以本质上在计算机中都是用一个字节存储的；

也就是说，Buffer相当于是一个字节的数组，数组中的每一项对于一个字节的大小：

如果我们希望将一个字符串放入到Buffer中，是怎么样的过程呢？

```
const buffer01 = new Buffer("why");

console.log(buffer01);
```

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)字符串存储buffer的过程

当然目前已经不希望我们这样来做了：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)VSCode的警告

那么我们可以通过另外一个创建方法：

```
const buffer2 = Buffer.from("why");
console.log(buffer2);
```

如果是中文呢？

```
const buffer3 = Buffer.from("王红元");
console.log(buffer3);
// <Buffer e7 8e 8b e7 ba a2 e5 85 83>
const str = buffer3.toString();
console.log(str);
// 王红元
```

如果编码和解码不同：

```
const buffer3 = Buffer.from("王红元", 'utf16le');
console.log(buffer3);

const str = buffer3.toString('utf8');
console.log(str); // �s�~CQ
```

### 7.2. Buffer其他用法

#### 7.2.1. Buffer的其他创建

Buffer的创建方式有很多：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusNM013pxvDCic4ued4GQC5bEGVONgOsCZ6utEwUAuoHjUib2nF5MhBtMloTJovgt02CIstHgpX3m1Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)buffer的创建

来看一下`Buffer.alloc`:

- 我们会发现创建了一个8位长度的Buffer，里面所有的数据默认为00；

```
const buffer01 = Buffer.alloc(8);

console.log(buffer01); // <Buffer 00 00 00 00 00 00 00 00>
```

我们也可以对其进行操作：

```
buffer01[0] = 'w'.charCodeAt();
buffer01[1] = 100;
buffer01[2] = 0x66;
console.log(buffer01);
```

也可以使用相同的方式来获取：

```
console.log(buffer01[0]);
console.log(buffer01[0].toString(16));
```

#### 7.2.2. Buffer和文件读取

文本文件的读取：

```
const fs = require('fs');

fs.readFile('./test.txt', (err, data) => {
  console.log(data); // <Buffer 48 65 6c 6c 6f 20 57 6f 72 6c 64>
  console.log(data.toString()); // Hello World
})
```

图片文件的读取：

```
fs.readFile('./zznh.jpg', (err, data) => {
  console.log(data); // <Buffer ff d8 ff e0 ... 40418 more bytes>
});
```

图片文件的读取和转换：

- 将读取的某一张图片，转换成一张200x200的图片；
- 这里我们可以借助于 `sharp` 库来完成；

```
const sharp = require('sharp');
const fs = require('fs');

sharp('./test.png')
  .resize(1000, 1000)
  .toBuffer()
  .then(data => {
    fs.writeFileSync('./test_copy.png', data);
  })
```

### 7.4. Buffer的内存分配

事实上我们创建Buffer时，并不会频繁的向操作系统申请内存，它会默认先申请一个8 * 1024个字节大小的内存，也就是8kb

- node/lib/buffer.js：135行

```
Buffer.poolSize = 8 * 1024;
let poolSize, poolOffset, allocPool;

const encodingsMap = ObjectCreate(null);
for (let i = 0; i < encodings.length; ++i)
  encodingsMap[encodings[i]] = i;

function createPool() {
  poolSize = Buffer.poolSize;
  allocPool = createUnsafeBuffer(poolSize).buffer;
  markAsUntransferable(allocPool);
  poolOffset = 0;
}
createPool();
```

假如我们调用Buffer.from申请Buffer：

- 这里我们以从字符串创建为例
- node/lib/buffer.js：290行

```
Buffer.from = function from(value, encodingOrOffset, length) {
  if (typeof value === 'string')
    return fromString(value, encodingOrOffset);
 
 // 如果是对象，另外一种处理情况
  // ...
};
```

我们查看fromString的调用：

- node/lib/buffer.js：428行

```
function fromString(string, encoding) {
  let ops;
  if (typeof encoding !== 'string' || encoding.length === 0) {
    if (string.length === 0)
      return new FastBuffer();
    ops = encodingOps.utf8;
    encoding = undefined;
  } else {
    ops = getEncodingOps(encoding);
    if (ops === undefined)
      throw new ERR_UNKNOWN_ENCODING(encoding);
    if (string.length === 0)
      return new FastBuffer();
  }
  return fromStringFast(string, ops);
}
```

接着我们查看fromStringFast：

- 这里做的事情是判断剩余的长度是否还足够填充这个字符串；
- 如果不足够，那么就要通过 `createPool` 创建新的空间；
- 如果够就直接使用，但是之后要进行 `poolOffset`的偏移变化；
- node/lib/buffer.js：428行

```
function fromStringFast(string, ops) {
  const length = ops.byteLength(string);

  if (length >= (Buffer.poolSize >>> 1))
    return createFromString(string, ops.encodingVal);

  if (length > (poolSize - poolOffset))
    createPool();
  let b = new FastBuffer(allocPool, poolOffset, length);
  const actual = ops.write(b, string, 0, length);
  if (actual !== length) {
    // byteLength() may overestimate. That's a rare case, though.
    b = new FastBuffer(allocPool, poolOffset, actual);
  }
  poolOffset += actual;
  alignPool();
  return b;
}
```

### 7.4. Stream

#### 7.4.1. 认识Stream

什么是流呢？

- 我们的第一反应应该是流水，源源不断的流动；
- 程序中的流也是类似的含义，我们可以想象当我们从一个文件中读取数据时，文件的二进制（字节）数据会源源不断的被读取到我们程序中；
- 而这个一连串的字节，就是我们程序中的流；

所以，我们可以这样理解流：

- 是连续字节的一种表现形式和抽象概念；
- 流应该是可读的，也是可写的；

在之前学习文件的读写时，我们可以直接通过 `readFile`或者 `writeFile`方式读写文件，为什么还需要流呢？

- 直接读写文件的方式，虽然简单，但是无法控制一些细节的操作；
- 比如从什么位置开始读、读到什么位置、一次性读取多少个字节；
- 读到某个位置后，暂停读取，某个时刻恢复读取等等；
- 或者这个文件非常大，比如一个视频文件，一次性全部读取并不合适；

事实上Node中很多对象是基于流实现的：

- http模块的Request和Response对象；
- process.stdout对象；

官方：另外所有的流都是EventEmitter的实例：

我们可以看一下Node源码中有这样的操作：

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)Stream和EventEmitter关系

流（Stream）的分类：

- `Writable`：可以向其写入数据的流（例如 `fs.createWriteStream()`）。
- `Readable`：可以从中读取数据的流（例如 `fs.createReadStream()`）。
- `Duplex`：同时为`Readable`和的流`Writable`（例如 `net.Socket`）。
- `Transform`：`Duplex`可以在写入和读取数据时修改或转换数据的流（例如`zlib.createDeflate()`）。

这里我们通过fs的操作，讲解一下Writable、Readable，另外两个大家可以自行学习一下。

#### 7.4.2. Readable

之前我们读取一个文件的信息：

```
fs.readFile('./foo.txt', (err, data) => {
  console.log(data);
})
```

这种方式是一次性将一个文件中所有的内容都读取到程序（内存）中，但是这种读取方式就会出现我们之前提到的很多问题：

- 文件过大、读取的位置、结束的位置、一次读取的大小；

这个时候，我们可以使用 `createReadStream`，我们来看几个参数，更多参数可以参考官网：

- start：文件读取开始的位置；
- end：文件读取结束的位置；
- highWaterMark：一次性读取字节的长度，默认是64kb；

```
const read = fs.createReadStream("./foo.txt", {
  start: 3,
  end: 8,
  highWaterMark: 4
});
```

我们如何获取到数据呢？

- 可以通过监听data事件，获取读取到的数据；

```
read.on("data", (data) => {
  console.log(data);
});
```

我们也可以监听其他的事件：

```
read.on('open', (fd) => {
  console.log("文件被打开");
})

read.on('end', () => {
  console.log("文件读取结束");
})

read.on('close', () => {
  console.log("文件被关闭");
})
```

甚至我们可以在某一个时刻暂停和恢复读取：

```
read.on("data", (data) => {
  console.log(data);

  read.pause();

  setTimeout(() => {
    read.resume();
  }, 2000);
});
```

#### 7.4.3. Writable

之前我们写入一个文件的方式是这样的：

```
fs.writeFile('./foo.txt', "内容", (err) => {
  
});
```

这种方式相当于一次性将所有的内容写入到文件中，但是这种方式也有很多问题：

- 比如我们希望一点点写入内容，精确每次写入的位置等；

这个时候，我们可以使用 `createWriteStream`，我们来看几个参数，更多参数可以参考官网：

- flags：默认是`w`，如果我们希望是追加写入，可以使用 `a`或者 `a+`；
- start：写入的位置；

我们进行一次简单的写入

```
const writer = fs.createWriteStream("./foo.txt", {
  flags: "a+",
  start: 8
});

writer.write("你好啊", err => {
  console.log("写入成功");
});
```

如果我们希望监听一些事件：

```
writer.on("open", () => {
  console.log("文件打开");
})

writer.on("finish", () => {
  console.log("文件写入结束");
})

writer.on("close", () => {
  console.log("文件关闭");
})
```

我们会发现，我们并不能监听到 `close` 事件：

- 这是因为写入流在打开后是不会自动关闭的；
- 我们必须手动关闭，来告诉Node已经写入结束了；
- 并且会发出一个 `finish` 事件的；

```
writer.close();

writer.on("finish", () => {
  console.log("文件写入结束");
})

writer.on("close", () => {
  console.log("文件关闭");
})
```

另外一个非常常用的方法是 `end`：

- `end`方法相当于做了两步操作：`write`传入的数据和调用`close`方法；

```
writer.end("Hello World");
```

#### 7.4.4. pipe方法

正常情况下，我们可以将读取到的 `输入流`，手动的放到 `输出流`中进行写入：

```
const fs = require('fs');
const { read } = require('fs/promises');

const reader = fs.createReadStream('./foo.txt');
const writer = fs.createWriteStream('./bar.txt');

reader.on("data", (data) => {
  console.log(data);
  writer.write(data, (err) => {
    console.log(err);
  });
});
```

我们也可以通过pipe来完成这样的操作：

```
reader.pipe(writer);

writer.on('close', () => {
  console.log("输出流关闭");
})
```

## 08.深入事件循环

