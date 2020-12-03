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
* body参数

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

### 根据不同的登录type获取不同的token

```
router.post('/', async (ctx) => {
    const v = await new TokenValidator().validate(ctx)
    let token;
    switch (v.get('body.type')) {
        case LoginType.USER_EMAIL:
            token = await emailLogin(v.get('body.account'), v.get('body.secret'))
            break
        case LoginType.USER_MINI_PROGRAM:
        case LoginType.ADMIN_EMAIL:
        default:
            throw new global.errs.ParameterException('没有相应的处理函数')
    }
    ctx.body = {
        token
    }
})
async function emailLogin(account, secret) {
    const user = await User.verifyEmailPassword(account, secret)
    return token = generateToken(user.id, Auth.USER)
}
```

models/user.js

```
const bcrypt = require('bcryptjs')
class User extends Model {
    static async verifyEmailPassword(email, plainPassword) {
        const user = await User.findOne({
            where: {
                email
            }
        })
        if (!user) {
            throw new global.errs.AuthFailed('账号不存在')
        }
        // user.password === plainPassword
        const correct = bcrypt.compareSync(plainPassword, user.password)
        if(!correct){
            throw new global.errs.AuthFailed('密码不正确')
        }
        return user
    }
}
```

core/util.js

```
const jwt = require('jsonwebtoken')
const generateToken = function(uid, scope){
    const secretKey = global.config.security.secretKey
    const expiresIn = global.config.security.expiresIn
    const token = jwt.sign({
        uid,
        scope
    },secretKey,{
        expiresIn
    })
    return token
}
```

config/config.js

```
module.exports = {
    security:{
        secretKey:"abcdefg",
        expiresIn:60*60*24*30
    }
}
```

### 公开api和非公开api（检测请求是否有携带token）

编写检测token的中间件

middlewares/auth.js

```
const basicAuth = require('basic-auth')
const jwt = require('jsonwebtoken')

class Auth {
    constructor() {
    }
    get m() {
        return async (ctx, next) => {
            
            const userToken = basicAuth(ctx.req)
            let errMsg = 'token不合法'
			// 检测是否携带token
            if (!userToken || !userToken.name) {
                throw new global.errs.Forbbiden(errMsg)
            }
            //检测token的合法性
            try {
                var decode = jwt.verify(userToken.name, global.config.security.secretKey)
            } catch (error) {
                if (error.name == 'TokenExpiredError'){
                    errMsg = 'token已过期'
                }
                throw new global.errs.Forbbiden(errMsg)
            }
            // uid,scope
            ctx.auth = {
                uid:decode.uid,
                scope:decode.scope
            }
			// 进入下一个中间件
            await next()
        }
    }

    static verifyToken(token){
        try{
            jwt.verify(token, global.config.security.secretKey)
            return true
        }
        catch (error){
            return false
        }

    }
}

module.exports = {
    Auth
}
```

core/http-exception.js

```
class Forbbiden extends HttpException{
    constructor(msg, errorCode) {
        super()
        this.msg = msg || '禁止访问'
        this.errorCode = errorCode || 10006
        this.code = 403
    }
}
```

### api权限分级控制

middlewares/auth.js

```
constructor(level) {
    this.level = level || 1
    Auth.USER = 8
    Auth.ADMIN = 16
    Auth.SUPER_ADMIN = 32
}

if(decode.scope < this.level){
    errMsg = '权限不足'
    throw new global.errs.Forbbiden(errMsg)
}
```

app/api/v1/token.js

```
async function emailLogin(account, secret) {
    const user = await
        User.verifyEmailPassword(account, secret)
    return token = generateToken(user.id, Auth.USER)
}
```

使用

app/api/v1/classic.js

```
router.get('/latest', new Auth(9).m, async (ctx, next) => {

})
```

### 小程序登录方式，微信授权

**业务逻辑**

1. 在API接口编写？
2. Model编写！！！

MVC中业务逻辑写在Model层

app/service/wx.js

```
const util = require('util')
const axios = require('axios')

const { User } = require('../models/user')
const { generateToken } = require('../../core/util')
const { Auth } = require('../../middlewares/auth')
class WXManager{
  	static async codeToToken(code) {
		// code appid appsecret -> openid
		const url = util.format(global.config.wx.loginUrl, global.config.wx.appId, global.config.wx.appSecret, code)
		const result = await axios.get(url)
		if(result.status !== 200) {
			throw new global.errs.AuthFailed('openid获取失败')
		}
		const errcode = result.data.errcode
		const errmsg = result.data.errmsg
		if(result.errcode) {
			throw new global.errs.AuthFailed('openid获取失败' +　errcode)
		}
		// 存储档案到user表中
		let user = await User.getUserByOpenid(result.data.openid)
        if(!user){
            user = await User.registerByOpenid(result.data.openid)
        }
        return generateToken(user.id, Auth.USER)
	}
}
module.exports = {
    WXManager
}
```

config/config.js

```
wx: {
	// appId appSecret通过微信开发后台获取
	appID: 'xxx',
	appSecret: 'xxx',
	loginUrl:'https://api.weixin.qq.com/sns/jscode2session?appid=%s&secret=%s&js_code=%s&grant_type=authorization_code'
}
```

/models/user.js

```
static async getUserByOpenid(openid){
    const user = await User.findOne({
        where:{
            openid
        }
    })
    return user
}

static async registerByOpenid(openid) {
    return await User.create({
        openid
    })
}
```

使用

v1/token.js

```
const axios = require('axios')

case LoginType.USER_MINI_PROGRAM:
      token = await WXManager.codeToToken(v.get('body.account'))
      break
```

