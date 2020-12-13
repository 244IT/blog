## 01.邂逅node

### **1.1.Node的版本工具**

在实际开发学习中，我们只需要使用一个Node版本来开发或者学习即可。但是，如果你希望通过可以快速更新或切换多个版本时，可以借助于一些工具：

* nvm：Node Version Manager
* n：Interactively Manage Your Node.js Versions（交互式管理你的Node.js版本）

>这两个工具都不支持window

#### 1.1.1.window中使用nvm

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

## 02.常见的内置模块

### 2.1. 内置模块path

#### 2.1.1. 认识path模块

path模块用于对路径和文件进行处理，提供了很多好用的方法。

并且我们知道在Mac OS、Linux和window上的路径是不一样的

- window上会使用 `\`或者 `\\` 来作为文件路径的分隔符，当然目前也支持 `/`；
- 在Mac OS、Linux的Unix操作系统上使用 `/` 来作为文件路径的分隔符；

那么如果我们在window上使用 `\` 来作为分隔符开发了一个应用程序，要部署到Linux上面应该怎么办呢？

- 显示路径会出现一些问题；
- 所以为了屏蔽他们之间的差异，在开发中对于路径的操作我们可以使用 `path` 模块；

#### 2.1.2. path常见的API

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

### 2.2. 内置模块fs

#### 2.2.1. 认识fs模块

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

#### 2.2.2. 文件描述符

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

#### 2.2.3.文件的读写

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

#### 2.2.3.文件夹操作

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

### 2.3. 内置模块events

#### 2.3.1. 基本使用

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

#### 2.3.1. 常见的属性

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

#### 2.3.1.  方法的补充

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

## 03.npm你不知道的细节

### 3.1. 包管理工具

#### 3.1.1. 认识npm

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

#### 3.1.2. 项目配置文件

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

### 3.2. npm工具解析

#### 3.2.1. npm install命令

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

#### 3.2.2. npm install原理

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

#### 3.2.3. 其他npm命令

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

#### 3.2.4. yarn和cnpm

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