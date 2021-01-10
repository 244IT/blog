## 01.项目架构

### 1.1.目录结构的划分

* node_modules
* src
  * app：全局相关
    * config.js：配置文件
    * database.js：数据库配置
    * error-handle.js：错误处理
    * index.js：主文件
  * constants：常量
    * error-types.js：错误类型
  * controller：控制器
  * middleware：中间件
  * router：路由相关
  * service：数据库操作相关
  * utils：工具类
  * main: 项目入口
* .env：配置文件
* package-lock.json
* package.json：包配置文件

### 1.2.项目技术栈

* koa
* koa-router
* koa-bodyparser
* mysql2
* nodemon
* dotenv：将配置文件加载到环境变量中

### 1.3.使用dotenv管理配置信息

* `npm install dotenv`
* 在根目录下新增`.env`文件

```
APP_PORT = 8000
```

* 在app文件夹下新增`config.js`文件

```
const dotenv = require('dotenv')

dotenv.config()

module.exports = { 
	APP_PORT
} = process.env
```

* main文件使用

```
const config = require('./app/config.js')

app.listen(config.APP_PORT, () => {
	console.log(`服务器在${config.APP_PORT}端口启动成功`)
})
```

### 1.4.业务逻辑的划分

#### 1.router

在router文件下新建`user.router.js`文件

```
const Router = require('koa-router')
const { create } = require('../controller/user.controller')

const userRouter = new Router({
  prefix: '/user'
})

userRouter.post('/', create)


module.exports = userRouter
```

* 可见router文件负责路由的注册和配置操作
* 路由的具体业务逻辑在`controller`文件内体现

#### 2.controller

在controller文件夹下新增`user.controller.js`文件

```
const { create } = require('../service/user.service')

class UserController{
  async create(ctx, next) {
    const user = ctx.request.body
    const result = await create(user)
    ctx.body = result
  }
}

module.exports = new UserController()
```

* 可见controller文件负责路由中逻辑的处理
* 数据库的操作逻辑在`service`文件内体现

#### 3.service

在service文件夹下新增`user.service.js`文件

```
class UserService{
  async create(user) {
    console.log('存入数据库的用户信息：',  user)
  }
}

module.exports = new UserService()
```

* service文件负责数据库操作

### 1.5.数据库配置及连接

* 在`.env`文件新增数据库配置

```
MYSQL_HOST = localhost
MYSQL_PORT = 3306
MYSQL_DATABASE = coderhub
MYSQL_USER = root
MYSQL_PASSWORD = root
```

* 在`config.js`文件新增导出

```
module.exports = {
  APP_PORT,
  MYSQL_PORT,
  MYSQL_HOST,
  MYSQL_DATABASE,
  MYSQL_USER,
  MYSQL_PASSWORD
} = process.env
```

* 在database.js文件连接数据库

```
const mysql = require('mysql2')
const {
  MYSQL_HOST,
  MYSQL_PORT,
  MYSQL_DATABASE,
  MYSQL_USER,
  MYSQL_PASSWORD
} = require('./config')

const connections = mysql.createPool({
  host: MYSQL_HOST,
  port: MYSQL_PORT,
  database: MYSQL_DATABASE,
  user: MYSQL_USER,
  password: MYSQL_PASSWORD
})

connections.getConnection((err, conn) => {
  conn.connect(err => {
    if(err) {
      console.log('数据库连接失败')
    } else {
      console.log('数据库连接成功')
    }
  })
})

module.exports = connections
```

### 1.6.统一错误处理

* 在`user`路由使用`verifyUser`中间件

```
const Router = require('koa-router')

const { create } = require('../controller/user.controller')
const { verifyUser } = require('../middleware/user.middleware')

const userRouter = new Router({
  prefix: '/user'
})

userRouter.post('/', verifyUser, create)


module.exports = userRouter
```

* 在`moddleware`添加`verifyUser`中间件

```
const errorType = require('../constants/error-types')
const service = require('../service/user.service')

/* 校验账号账号密码中间件 */
const verifyUser = async (ctx, next) => {

  // 获取用户的昵称和密码
  const { name, password } = ctx.request.body

  console.log('错误校验')
  console.log(name, password)
  // 判断用户账号密码是否为空
  if(!name || !password) {
    const error = new Error(errorType.NAME_OR_PASSWORD_IS_REQUIRED)
    return ctx.app.emit('error', error, ctx)
  }

  // 判断是否已经存在此账号
  const result = await service.getUserByName(name)
  console.log(result)
  if(result.length) {
    const error = new Error(errorType.USER_ALREADT_EXIST)
    return ctx.app.emit('error', error, ctx)
  }

  await next()
}

module.exports = verifyUser
```

* 在`constants`文件夹下添加`error-type.js`文件，存储错误常量

```
const NAME_OR_PASSWORD_IS_REQUIRED = 'name or password is required'
const USER_ALREADT_EXIST = 'user already exist'


module.exports = {
  NAME_OR_PASSWORD_IS_REQUIRED,
  USER_ALREADT_EXIST
}
```

* 在`app`文件夹下添加`error-handle.js`文件，将`main.js`文件夹下的错误逻辑处理函数单独提取出来

```
const errorType = require('../constants/error-types')

const errorHandle = (error, ctx) => {
  console.log('进入错误分类处理')
  let status, message
  switch(error.message) {
    case errorType.NAME_OR_PASSWORD_IS_REQUIRED:
      status = 400;
      message = '用户名或者密码为空~';
      break;
 	...
  }
  ctx.status = status
  ctx.body = message
}

module.exports = errorHandle
```

* 在`main.js`中捕获错误

```
const errorHandle = require('./app/error-handle')

app.on('error', errorHandle)
```

## 02.用户接口

### 2.1.用户注册接口

#### 1.注册密码加密处理

* 在路由中添加密码加密中间件

```
userRouter.post('/', verifyUser, handlePassword, create)
```

* 在`user.middleware.js`中新增`handlePassword`中间件

```
const md5password = require('../utils/handle-password')

const handlePassword = (ctx, nect) => {
	const { password } = ctx.request.body
	ctx.request.body.password = md5password(password)
  	await next()
}
```

* 在`utils`文件夹下新增`handle-password.js`文件

```
const crypto = require('crypto')

/* 密码加密函数 */
const md5password = (password) => {
  const md5 = crypto.createHash('md5')
  const result = md5.update(password).digest('hex')
  return result 
}

module.exports = md5password
```

### 2.2.用户登录接口

#### 1.账号密码校验

1. 在`router`中新建`router.auth.js`文件

```
const Router = require('koa-router')

const { login } = require('../controller/auth.controller.js')
const { verifyLogin } = require('../middleware/auth.middleware')
const authRouter = new Router({
  prefix: '/login'
})


authRouter.post('/', verifyLogin, login)

module.exports = authRouter
```

* 可先把`verifyLogin`中间去除，写个简易的`login`处理函数

2. 在`controller`文件夹下新增`auth.controller.js`文件，编写建议的`login`函数

```
class AuthController{
  /* 用户登录控制层函数 */
  async login(ctx, next) {
    const { name, password } = ctx.request.body
    ctx.response.body = `欢迎${name}回来`
  }
}

module.exports = new AuthController()
```

3. 在`middleware`文件夹下新增`auth.middleware.js`文件，编写`verifyLogin`中间件，校验账号密码的准确性

```
const errorType = require('../constants/error-types')
const service = require('../service/user.service')
const md5password = require('../utils/handle-password')

class AuthMiddleware{
  async verifyLogin(ctx, next) {
    // 获取账号密码
    const { name, password } = ctx.request.body 
    
    // 判断账号密码是否为空
    if(!name || !password) {
      const error = new Error(errorType.NAME_OR_PASSWORD_IS_REQUIRED)
      return ctx.app.emit('error', error, ctx)
    }
    // 判断用户是否存在
    const result = await service.getUserByName(name)
    const user = result[0]
    console.log(user)
    if(!user) {
      const error = new Error(errorType.USER_DOSE_NOT_EXIST)
      return ctx.app.emit('error', error, ctx)
    }
    // 判断密码是否错误
    if(md5password(password) !== user.password) {
      const error = new Error(errorType.PASSWORD_ERROR)
      return ctx.app.emit('error', error, ctx)
    }

    await next()
  }
}

module.exports = new AuthMiddleware()
```

4. 在`error-types.js`和`error-handle.js`新增新的错误处理配置

```
const USER_DOSE_NOT_EXIST = 'user dose not exist'
const PASSWORD_ERROR = 'password error'

module.exports = {
  USER_DOSE_NOT_EXIST,
  PASSWORD_ERROR
}
```

```
switch(error.message) {
  ...
  case errorType.USER_DOSE_NOT_EXIST:
    status = 400;
    message = '用户名不存在~';
    break;
  case errorType.PASSWORD_ERROR:
    status = 400;
    message = '密码错误~';
    break;
  default: 
    status = 404;
    message = 'NOT FOUNT';
}
```



