为什么中间件函数前要加async？

因为中间件函数中使用了await，如果不用async会报错

为什么koa中要使用async，await语法？

无法保证中间件中的函数是按照洋葱模型的顺序执行

### 第三章 让KOA更加好用-路由系统的改造

#### 3.1路由

#### 3.2.服务端编程必备思维：主体与模型划分

#### 3.3.多Router拆分

在进行多Router拆分之前，我们先搞清楚为什么要进行多Router拆分

**原因一:文件逻辑过于复杂臃肿**

如果我们直接在app.js中写每个router的逻辑，那业务逻辑会非常臃肿

app.js

```
const Koa = require('koa')
const Router = require('koa-router')

const app = new Koa()
const router = new Router()

router.get('classic/latest', (ctx, next) => {
	ctx.body = 'hello classic'
})

router.get('book', (ctx, next) => {
	ctx.body = 'book
})

app.use(router)
app.listen(3000)
```

**原因二：api版本问题**

很多时候由于新版本业务的变动，需要对api返回的结果进行改动，这是候如果直接在原api的基础上进行改动，会造成不兼容老版本的情况（多指app），所以很多时候服务器api需要兼容多个版本，通常比较合适的是三个，太多版本的兼容消耗更多的成本。

支持版本号的方式：客户端请求时携带

* 携带在请求路径中
* 携带在请求参数中
* 携带在header中

##### 如何拆分？

直接在router中通过if判断版本的方式吗？

```
router.get('book', (ctx, next) => {
	if(v1) {
		...
	}
	if(v2) {
		...
	}
	...
})
```

* 直接在router中通过if判断版本会使业务逻辑过于臃肿和复杂

* 加入不需要v1版本，则需要把v1部分的代码删除，这是非常危险的操作，编程有个非常重要的原则：开闭原则
* 开闭原则： 尽量去扩展代码，而不是修改代码

在根目录下新建api文件夹，再新建v1、v2..文件夹存放不同版本的api，然后在v1中新建classic.js，books.js文件编写路由文件

classic.js

```
const Router = require('koa-router')
const router = new Router()

router.get('/classic', (ctx, next) => {
	ctx.body = 'classic'
}) 

module.exports = router
```

book.js

```
const Router = require('koa-router')
const router = new Router()

router.get('/book', (ctx, next) => {
	ctx.body = 'book'
}) 

module.exports = router
```

app.js

```
const Koa = require('koa')
const classic = require('./api/v1/classic')
const book = require('./api/v1/book')

const app = new Koa()

app.use(classic.routes())
app.use(book.routes())
app.listen(3000)
```

#### 3.4.nodemon自动重启server

### 第四章 异步异常与全局异常处理

#### 4.1.KOA接收参数的方式

* get参数

```

```

* post参数

```

```

* header参数

### 密码撒盐

撒盐逻辑写在路由中

```
// 校验
const v = await new RegisterValidator().validate(ctx)

const salt = bcrypt.genSaltSync(10)
const pwd = bcrypt.hashSync(v.get('body.password2'), salt)
// 插入数据库
const user = {
  email: v.get('body.email'),
  password: pwd,
  nickname: v.get('body.nickname')
}
```

撒盐逻辑写在模型中

```
password: {
  type: Sequelize.STRING,
  set(val) {
    const salt = bcrypt.genSaltSync(10)
    const pwd = bcrypt.hashSync(val, salt)
    this.setDataValue(pwd)
  }
},
```

### token校验器

v1/token.js

```
const Router = require('koa-router')

const router = new Router({
	prefix" '/v1/token'
})

router.post('/', async (ctx) => {
})
```

http-exception.js

```
class TokenValidate extends LinValidate {
	constructor() {
		super()
		this.account = [		
			new Rule('isLength', '不符合账号规则，至少6个字符', {min: 6, max: 32})
		]
		this.secret = [
			new Rule('isOption')
			new Rule('isLength'， '至少6个字符', {min: 6, max: 128})
		]
	}
}
```

### 模拟枚举

lib/enum.js

```
function isThisType(val) {
	for(let prop in this) {
		if(this[prop] === val) {
			return true
		}
	}
	return false
}

const loginType = {
	USER_MINI_PROGRAM: 100,
	USER_EMAIL: 101,
	USRE_MOBILE: 102,
	ADMIN_EMAIL: 201,
	isThisType
}
```

http-exception.js

```
class TokenValidate extends LinValidate {
	constructor() {
		super()
		this.account = [		
			new Rule('isLength', '不符合账号规则，至少6个字符', {min: 6, max: 32})
		]
		this.secret = [
			new Rule('isOption')
			new Rule('isLength'， '至少6个字符', {min: 6, max: 128})
		]
		validateLoginType(vals) {
			if(!vals.body.type) {
				throw new Error('type是必填参数')
			}
			if(!loginType.isThisType(vals.body.type)) {
				throw new Error('type参数不合法')
			}
		}
	}
}
```

v1/token.js

```
const Router = require('koa-router')
const { TokenValidate } = require('../../validate/validate')
const router = new Router({
	prefix" '/v1/token'
})

router.post('/', async (ctx) => {
	const v = await new TokenValidate().validate(ctx)
})
```

### 根据不同的登录type作不同的处理

