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
> * 如果我们请求 link、copy、lock，那么久自动报错：Not Implemented，状态码：501；

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

不使用koa-router无法解析params，所以需要借助koa-router解析参数：

### 4.1.解析query和params

url：`http://localhost:3000/user/123?name=chh&age=12`

```
const Koa = require('koa')
const Router = require('koa-router')
const app = new Koa()
const router = new Router({prefix: '/user'})

router.get('/:id', (ctx, next) => {
	console.log(ctx.request.quiry)
	console.log(ctx.request.params)
	ctx.response.body = 'success'
})
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
 "account": "031wUB000ehAJK1GYK000XucT31wUB0W",
 "type": "100"
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

...
```

>注意：要先注册bodyParser再注册路由中间件，不然解析body数据会返回undifine

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

## 5.koa-multer实现文件上传

## 6.数据的响应

**输出结果：body**将响应主体设置为以下之一：

* string ：字符串数据
* Buffer ：Buffer数据
* Stream ：流数据
* Object|| Array：对象或者数组 
* null ：不输出任何内容
* 如果response.status尚未设置，Koa会自动将状态设置为200或204。 

>在实际开发中我们通常返回对象或者数组给客户端。ctx.respons.body = '对象'，会直接作为json类型返回

**1.ctx.response.body和ctx.body**

ctx.response.body和ctx.body都可以返回数据，而实际上是koa源码中将ctx.xxx做了代理，所以我们调用ctx.body实际上最后还是使用ctx.response.body返回数据

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



