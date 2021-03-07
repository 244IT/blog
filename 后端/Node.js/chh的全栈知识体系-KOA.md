## 1.邂逅KOA

Koa官方的介绍：

* koa：next generation web framework for node.js； 
* koa：node.js的下一代web框架；

事实上，koa是express同一个团队开发的一个新的Web框架：

* 目前团队的核心开发者TJ的主要精力也在维护Koa，express已经交给团队维护了；
* Koa旨在为Web应用程序和API提供更小、更丰富和更强大的能力；
* 相对于express具有更强的异步处理能力（后续我们再对比）；
* Koa的核心代码只有1600+行，是一个更加轻量级的框架，我们可以根据需要安装和使用中间件；

**KOA初体验**

```
const Koa = require('koa')

const app = new Koa()

app.use((ctx, next) => {
	ctx.response.body = 'hello koa'
})

app.listen('3000', () => {
	console.log('koa服务器已启动')
})
```

## 2.Koa注册中间件

```
const Koa = require('koa')

const app = new Koa()

app.use((ctx, next) => {
	if(ctx.url === '/login') {
		if(ctx.method === 'POST') {
			ctx.response.body = "login success"
		}
	} else {
		ctx.response.body = 'login fail'
	}
})

app.listen('3000', () => {
	console.log('koa服务器已启动')
})
```

**koa注册的中间件提供了两个参数：**

- ctx：上下文（Context）对象；

- - koa并没有像express一样，将req和res分开，而是将它们作为ctx的属性；
  - ctx代表依次请求的上下文对象；
  - `ctx.request`：获取请求对象；
  - `ctx.response`：获取响应对象；

- next：本质上是一个dispatch，类似于之前的next；

- - 后续我们学习Koa的源码，来看一下它是一个怎么样的函数；

**koa注册中间件只能通过app.use方法：**

- Koa并没有提供app.methods（app.get()，app.post()）的方式来注册中间件；
- 也没有提供path中间件来匹配路径；

但是真实开发中我们如何将路径和method分离呢？

- 方式一：根据request自己来判断；
- 方式二：使用第三方路由中间件；

方式一：根据request自己判断

```
app.use((ctx, next) => {
  if (ctx.request.path === '/users') {
    if (ctx.request.method === 'POST') {
      ctx.response.body = "Create User Success~";
    } else {
      ctx.response.body = "Users List~";
    }
  } else {
    ctx.response.body = "Other Request Response";
  }
})
```

整个代码的逻辑是非常复杂和混乱的，真实开发中我们会使用路由。

**koa在响应返回方面和express有些不同**

* koa通过`ctx.body.response`返回数据，express则是通过`res.end`返回数据
* koa如果所有的中间件都没有返回结果的话，默认返回status为404，msg为`Not Found`

## 3.Koa路由的使用

koa官方并没有给我们提供路由的库，我们可以选择第三方

库：koa-router

* `npm install koa-router` 安装路由
* 我们可以先封装一个 user.router.js 的文件：
* 在app中将router.routes()注册为中间件：

> 注意：allowedMethods用于判断某一个method是否支持：
>
> * 如果我们请求 get，那么是正常的请求，因为我们有实现get； 
> * 如果我们请求 put、delete、patch，那么就自动报错：Method Not Allowed，状态码：405； 
> * 如果我们请求 link、copy、lock，那么就自动报错：Not Implemented，状态码：501；

router/user.js

```
const Router = require('koa-router')

const router = new Router({prefix: '/user'})

router.get('/', (ctx, next) => {
	ctx.response.body = 'get request'
})

router.post('/login', (ctx, next) => {
	ctx.response.body = 'post request'
})

module.exports = router
```

* 通常一个路由对象是对一组相似路径的封装，那么路径的前缀都是一直的，所以我们可以直接在创建Router时，通过`prefix`属性添加前缀

index.js

```
const Koa = require('koa')

const userRouter = require('./router/user.js')
const app = new Koa()
app.use(userRouter.routes())
app.use(userRouter.allowedMethods())
app.listen('3000', () => {
	console.log('koa服务器已启动')
})
```

## 4.Koa参数解析

客户端传递到服务器参数的方法常见的是5种：

- 方式一：通过get请求中的URL的params；
- 方式二：通过get请求中的URL的query；
- 方式三：通过post请求中的body的json格式；
- 方式四：通过post请求中的body的x-www-form-urlencoded格式；
- 方式五：通过post请求中的form-data格式；

不使用koa-router无法解析params，所以需要借助koa-router解析参数:

url:`localhost:3000/user?name=chh`

```
app.use((ctx, next) => {
	console.log(ctx.request.url) 
	console.log(ctx.request.query)
	console.log(ctx.request.params)
})
// /user?name=chh
// { name: 'chh' }
// undefined
```



### 4.1.解析query和params

url：`http://localhost:3000/user/123?name=chh&age=12`

```
const Koa = require('koa')
const Router = require('koa-router')
const app = new Koa()
const router = new Router({prefix: '/user'})

router.get('/:id', (ctx, next) => {
	console.log(ctx.request.query)
	console.log(ctx.request.params)
	ctx.response.body = 'success'
})
// {id: 123}
// {name: chh, age: 12}
app.use(router.routes())

app.listen('3000', () => {
	console.log('koa服务器已启动')
})
```

### 4.2.解析body（json、urluncoded、form-data）

解析body中的数据我们需要借助koa-bodyparser：

`npm install koa-bodyparser`

**1.解析json和urluncoded**

url：`http://localhost:3000/user`

参数：

```
{
 "name": "chh",
 "age": "12"
}
```

代码：

```
const Koa = require('koa')
const Router = require('koa-router')
const bodyParser = require('koa-bodyparser')
const app = new Koa()
const router = new Router({prefix: '/user'})

app.use(bodyParser())
app.use(router.routes())

router.get('/', (ctx, next) => {
	console.log(ctx.request.body)
})	
// {name: 'chh', age: 12}
...
```

>注意：
>
>* 要先注册bodyParser再注册路由中间件，不然解析body数据会返回undifined
>* json和urlunicode都是用ctx.request.body接收

**2.解析form-data**

解析form-data我们还需要借助koa-multer：

`npm install koa-multer`

url：`http://localhost:3000/user`

参数：

* name: chh
* age: 18

代码：

```
const Koa = require('koa')
const Router = require('koa-router')
const bodyParser = require('koa-bodyparser')
const multer = require('koa-multer')
const app = new Koa()
const router = new Router({prefix: '/user'})

const upload = multer()
app.use(bodyParser())
app.use(router.routes())

router.post('/', upload.any(), (ctx, next) => {
	console.log(ctx.req.body)
	ctx.response.body = 'success'
})

...
```

>注意：
>
>* 使用koa-multer接收form-data数据通过ctx.req.body而不是ctx.request.body
>* ctx.request.body是koa中自定义的对象，而ctx.req.body是http中原生的req对象

## 5.koa-multer实现文件上传

```
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, "./uploads/")
  },
  filename: (req, file, cb) => {
    cb(null, Date.now() + path.extname(file.originalname))
  }
})

const upload = multer({
  storage
});

const fileRouter = new Router();

fileRouter.post("/upload", upload.single('avatar'), (ctx, next) => {
  console.log(ctx.req.file);
})

app.use(fileRouter.routes());
```

* dest：指定文件存放目录，如不存在该目录，会自动生成，路径相对于cmd开启路径
* storage：修改图片相关信息，通过storage的destination属性指定的目录不会自动生成，需保证存在
* ctx.req.file属性详解

```
{
  fieldname: 'avatar', // key名称
  originalname: 'aisi04.jpg', // 文件名
  encoding: '7bit',
  mimetype: 'image/jpeg', // 文件类型
  destination: './upload/',
  filename: '1615038030082.jpg',
  path: 'upload\\1615038030082.jpg',
  size: 27874 // 文件大小
}
```

## 6.数据的响应

**1.输出结果：body**将响应主体设置为以下之一：

* string ：字符串数据
* Buffer ：Buffer数据
* Stream ：流数据
* Object|| Array：对象或者数组 
* null ：不输出任何内容
* 如果response.status尚未设置，Koa会自动将状态设置为200或204。 

>在实际开发中我们通常返回对象或者数组给客户端。ctx.respons.body = '对象'，会直接作为json类型返回

**2.设置状态码**

```
ctx.status = 状态码
```

* 状态码和响应内容不区分前后顺序，一般写在ctx.body之前即可

3.ctx.response.body和ctx.body**

* ctx.response.body和ctx.body都可以返回数据，而实际上是koa源码中将ctx.xxx做了代理，所以我们调用ctx.body本质上最后还是使用ctx.response.body返回数据

- 我们可以看到源码中，我们访问 proto（这里就是ctx），其实是访问proto中的response的属性；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusUoicD7NHXV5rLv2RQouhMqyHZyxiaX72EXRPnqL3JppmnZeZicXLTeNTyLcBd34AfK1ZlcgkrR4lxw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 7.部署静态资源

部署静态资源需要安装`npm install koa-static`

```
const Koa = require('koa')
const koaStatic = require('koa-static')

const app = new Koa()

app.use(koaStatic('./build'))

...
```

## 8.错误处理方式

```
const Koa = require('koa')

const app = new Koa()

app.use((ctx, next) => {
	const flag = false
	if(!flag) {
		ctx.app.emit('error', new Error('您还没有登录'), ctx)
	}
})

app.on('error', (err, ctx) => {
	ctx.status = 401
	ctx.body = err.message
})

...
```

## 9.Koa和express对比

在学习了两个框架之后，我们应该已经可以发现koa和express的区别：

从架构设计上来说：

- express是完整和强大的，其中帮助我们内置了非常多好用的功能；

- koa是简洁和自由的，它只包含最新的功能，并不会对我们使用其他中间件进行任何的限制。

- - 甚至是在app中连最基本的get、post都没有给我们提供；
  - 我们需要通过自己或者路由来判断请求方式或者其他功能；

因为express和koa框架他们的核心其实都是中间件：

- 但是他们的中间件事实上，它们的中间件的执行机制是不同的，特别是针对某个中间件中包含异步操作时；
- 所以，接下来，我们再来研究一下express和koa中间件的执行顺序问题；

我通过一个需求来演示所有的过程：

- 假如有三个中间件会在一次请求中匹配到，并且按照顺序执行；

- 我希望最终实现的方案是：

- - 注意：是middleware1中；
  - 在middleware1中，在req.message中添加一个字符串 `aaa`；
  - 在middleware2中，在req.message中添加一个 字符串`bbb`；
  - 在middleware3中，在req.message中添加一个 字符串`ccc`；
  - 当所有内容添加结束后，在middleware1中，通过res返回最终的结果；

### 2.1. 同步执行顺序

假如我们获取的所有数据，是可以同步获取的；

**我们先通过express实现这个过程：**

```
const express = require('express');

const app = express();


const middleware1 = (req, res, next) => {
  req.message = "aaa";
  next();
  res.end(req.message);
}

const middleware2 = (req, res, next) => {
  req.message = req.message + 'bbb';
  next();
}

const middleware3 = (req, res, next) => {
  req.message = req.message + 'ccc';
}

app.use(middleware1, middleware2, middleware3);

app.listen(8000, () => {
  console.log("启动成功~");
})
```

最终的结果是：`aaabbbccc`，没问题；

**我们再通过koa实现这个过程：**

```
const Koa = require('koa');

const app = new Koa();

const middleware1 = (ctx, next) => {
  ctx.message = "aaa";
  next();
  console.log("aaaa");
  ctx.body = ctx.message;
}

const middleware2 = (ctx, next) => {
  ctx.message = ctx.message + 'bbb';
  console.log("bbbb");
  next();
}

const middleware3 = (ctx, next) => {
  ctx.message = ctx.message + 'ccc';
}

app.use(middleware1);
app.use(middleware2);
app.use(middleware3);

app.listen(8000, () => {
  console.log("启动成功~");
})
```

最终的结果也是：`aaabbbccc`，也没问题；

### 2.2. 异步执行顺序

但是，如果我们最后的ccc中的结果，是需要异步操作才能获取到的，是否会产生问题呢？

#### 2.2.1. express中遇到异步操作

**express有异步操作（没有在next前，加async、await）：**

```
const middleware1 = (req, res, next) => {
  req.message = "aaa";
  next();
  res.end(req.message);
}

const middleware2 = (req, res, next) => {
  req.message = req.message + 'bbb';
  next();
}

const middleware3 = async (req, res, next) => {
  const result = await axios.get('http://123.207.32.32:9001/lyric?id=167876');
  req.message = req.message + result.data.lrc.lyric;
  console.log(req.message);
}
```

最终的结果`aaabbb`，是不正确。

**express有异步操作（有在next前，加async、await）：**

```
const middleware1 = async (req, res, next) => {
  req.message = "aaa";
  await next();
  res.end(req.message);
}

const middleware2 = async (req, res, next) => {
  req.message = req.message + 'bbb';
  await next();
}

const middleware3 = async (req, res, next) => {
  const result = await axios.get('http://123.207.32.32:9001/lyric?id=167876');
  req.message = req.message + result.data.lrc.lyric;
  console.log(req.message);
}
```

最终的结果也是`aaabbb`，也是不正确。

为什么呢？

- 原因是本质上的next()和异步没有任何关系；
- 它本身就是一个同步函数的调用，所以它不会等到你异步有结果之后，再继续执行后续的操作；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXusUoicD7NHXV5rLv2RQouhMqVuavawiaOVaicouicDsZrC0BfDk3PJuqU7HibZiaib3pbRLvLVHpmhF5l5Yg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image-20201106175205300

#### 2.2.2. koa中遇到异步操作

**koa有异步操作（没有在next前，加async、await）：**

```
const middleware1 = async (ctx, next) => {
  ctx.message = "aaa";
  next();
  ctx.body = ctx.message;
}

const middleware2 = async (ctx, next) => {
  ctx.message = ctx.message + 'bbb';
  next();
}

const middleware3 = async (ctx, next) => {
  const result = await axios.get('http://123.207.32.32:9001/lyric?id=167876');
  ctx.message = ctx.message + result.data.lrc.lyric;
}
```

最终的结果也是`aaabbb`，也是不正确。

- 这是因为虽然next函数是一个返回promise的异步操作，但是在前面不加await的情况，是不同等待结果的返回，就会继续向后执行了；

**koa有异步操作（有在next前，加async、await）：**

```
const middleware1 = async (ctx, next) => {
  ctx.message = "aaa";
  await next();
  ctx.body = ctx.message;
}

const middleware2 = async (ctx, next) => {
  ctx.message = ctx.message + 'bbb';
  await next();
}

const middleware3 = async (ctx, next) => {
  const result = await axios.get('http://123.207.32.32:9001/lyric?id=167876');
  ctx.message = ctx.message + result.data.lrc.lyric;
}
```

最终的结果是`aaabbb+歌词信息`，是正确。

- 这是因为，当我们在koa中的next前面加await时，它会等到后续有一个确定结果时，在执行后续的代码；