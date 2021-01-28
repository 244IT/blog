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
    * index.js：入口文件，配置路由的动态加载
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
* koa-multer
* mysql2
* jsonwebtoken：使用jwt实现token机制
* jimp：图片处理库
* nodemon：生产依赖
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

### 1.7.动态加载路由

1. 在`router`文件夹下新增`index.js`文件

```
const fs = require('fs')

/* 路由动态加载 */
const useRoutes = (app) => {
    fs.readdirSync(__dirname).forEach(file => {
        if(file === 'index.js') return
        const router = require(`./${file}`)
        app.use(router.routes())
        app.use(router.allowedMethods())
    })
}

module.exports = useRoutes
```

2. 在`main.js`中使用

```
const useRoutes = require('./router/index')
useRoutes(app)
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

#### 2.生成token令牌

**使用openssl生成私钥和公钥**

1. 打开`git.bush`输入`openssl`
2. `genrsa -out private.key 1024`生成私钥
3. `rsa -in private.key -pubout -out public.key`生成公钥

**配置私钥和公钥**

`app/config.js`

```
const fs = require('fs')
const path = require('path')

const PRIVATE_KEY = fs.readFileSync(path.resolve(__dirname, './keys/private.key'))
const PUBLIC_KEY = fs.readFileSync(path.resolve(__dirname, './keys/public.key'))


module.exports.PRIVATE_KEY = PRIVATE_KEY
module.exports.PUBLIC_KEY = PUBLIC_KEY
```

**使用jsonwebtoken生成token**

1. 安装`jsonwebtoken`
2. 在校验中间件中返回数据库中的用户信息

`auth.middleware.js`(代码片段)

```
// 判断密码是否错误
if(md5password(password) !== user.password) {
    const error = new Error(errorType.PASSWORD_ERROR)
    return ctx.app.emit('error', error, ctx)
}

ctx.user = user

await next()
```

3. 在登录逻辑中添加生成token

`auth.controller.js`

```
const jwt = require('jsonwebtoken')
const { PRIVATE_KEY } = require('../app/config')

class AuthController{
  /* 用户登录控制层函数 */
  async login(ctx, next) {
    console.log(ctx.user)
    const { name, id } = ctx.user
    const token = jwt.sign({id, name}, PRIVATE_KEY, {
      expiresIn: 60 * 60 * 24, // 过期时间
      algorithm: 'RS256' // 使用的算法
    })
    ctx.body = {
      id,
      name,
      token
    }
  }
}

module.exports = new AuthController()
```

#### 3.用户登录验证

1. 在`router/user.auth.js`新增一个test路由，用于验证token是否有效，以备在后面接口中有需要验证token的接口

```
/* 用户登录验证 */
authRouter.get('/test', verifyAuth, success)
```

2. 在`auth.middleware.js`中编写中间件

```
const jwt = require('jsonwebtoken')

const errorType = require('../constants/error-types')
const { PUBLIC_KEY } = require('../app/config')

/* 登录验证 */
const verifyAuth = async (ctx, next) => {
  console.log('验证登录的middleware')
  // 获取token
  const authorization = ctx.header.authorization
  const token = authorization.replace('Bearer ', '')
  console.log(token)
  // 验证token
  try {
    const result = jwt.verify(token, PUBLIC_KEY, {
      algorithms: ['RS256']
    })
    ctx.user = result
    await next()
  } catch(err) {
    const error = new Error(errorType.UNAUTHORIZATION)
    ctx.app.emit('error', error, ctx)
  }
}

module.exports = {
  verifyAuth
}
```

3. 新增错误处理(代码片段)

```
const UNAUTHORIZATION = 'unauthorization'

module.exports = {
  UNAUTHORIZATION
}
```

```
const errorHandle = (error, ctx) => {
  console.log('进入错误分类处理')
  let status, message
  switch(error.message) {
	...
    case errorType.UNAUTHORIZATION:
      status = 401;
      message = '未授权token~';
      break;
    default: 
      status = 404;
      message = 'NOT FOUNT';
  }
  ctx.status = status
  ctx.body = message
}
```

4. 在`postman`中添加脚本方便token的校验，在登录接口的Test中添加脚本

```
const res = pm.response.json();
pm.globals.set("token", res.token);
```

* 然后在登录验证的`Bearer Token`内容中添加`{{token}}`即可

## 03.动态接口

创建动态表

```
CREATE TABLE `moment` (
  `id` int NOT NULL AUTO_INCREMENT,
  `content` varchar(1000) NOT NULL,
  `user_id` int NOT NULL,
  `createAt` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updateAt` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`),
  CONSTRAINT `moment_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `user` (`id`)
)
```

### 3.1.新增动态

1. 新增路由，新增动态需要token权限

```
const { create } = require('../controller/moment.controller.js')
const { verifyAuth } = require('../middleware/auth.middleware')

router.post('/create', verifyAuth, create)
```

2. 在`moment.controller.js`中编写`create`函数

```
class MomentController{
    /* 新增动态 */
    async create(ctx, next) {
        // 获取发表动态的用户id和评论内容
        const { id } = ctx.user
        const { content } = ctx.request.body
        // 新增动态（操作数据库）
        const result = await create(id, content)
        ctx.body = result
    }
}    

module.exports = new MomentController()
```

### 3.2.查询动态详情

动态详情接口难度不大，需要前端在params中添加动态的id

在`moment.router.js`中新增获取动态详情的路由

```
/* 获取动态详情 */
momentRouter.get('/detail/:id', detail)
```

`moment.controller.js`

```
/* 获取动态详情 */
async detail(ctx, next) {
    // 获取动态id
    const { id } = ctx.params
    // 获取动态详情（操作数据库）
    const result = await detail(id)
    ctx.body = result
}
```

`moment.service.js`

```
/* 获取动态详情 */
async detail(momentId) {
    console.log(momentId)
    const statement = `
        SELECT m.id momentId, m.content, m.updateAt updateTime, m.createAt createTime, 
            JSON_OBJECT('userId', u.id, 'userName', u.name, 'avatar', u.avatar_url) author,
            (SELECT JSON_ARRAYAGG(CONCAT('http://localhost:8000/moment/images/', file.filename))
                FROM file
                WHERE file.moment_id = m.id
            ) images,
            (SELECT JSON_ARRAYAGG(l.name)
                FROM moment_label ml 
                LEFT JOIN label l
                ON ml.label_id = l.id
                WHERE ml.moment_id = m.id
            ) labelList
        FROM moment m
        LEFT JOIN user u
        ON m.user_id = u.id
        WHERE m.id = ?; 
    `
    const [[result]] = await connection.execute(statement, [momentId])
    return result
}
```



### 3.3.查询动态列表

coderhub首页展示所有动态列表，获取全部动态不需要校验权限：

在`moment.router.js`中新增获取列表的路由

```
const { list } = require('../controller/moment.controller.js')
/* 获取动态列表 */
momentRouter.get('/list', list)
```

`moment.controller.js`

```
/* 获取动态列表 */
async list(ctx, next) {
	// 获取列表大小和页数
	const { size, page } = ctx.request.query
	// 获取动态列表（操作数据库）
	const result = await list(size, page)
	ctx.body = result
}
```

`moment.service.js`

```
/* 获取动态列表 */
async list(size = 10, page = 1) {
    const offset = (page - 1) * 10
    const statement = `
        SELECT m.id momentId, m.content, m.updateAt updateTime, m.createAt createTime, 
        JSON_OBJECT('userId', u.id, 'userName', u.name, 'avatar', u.avatar_url) author,
        (SELECT COUNT(*) FROM comment c WHERE c.moment_id = m.id) commentCount,
        (
            SELECT JSON_ARRAYAGG(CONCAT('http://localhost:8000/moment/images/', file.filename))
            FROM file
            WHERE file.moment_id = m.id
        ) images
        FROM moment m
        LEFT JOIN user u
        ON m.user_id = u.id
        LIMIT ?, ?;
    `
    const [result] = await connection.execute(statement, [offset, size])
    return result
}
```

### 3.4.修改动态

修改动态和删除动态都需要以下条件：

* 用户登录
* 动态是属于此用户的

所以我们要新编写校验用户是否有权限修改此动态资源

`auth.middleware.js`

```
const AuthService = require('../service/auth.service')
/* 验证资源修改权限 */
const verifyPermission = async (ctx, next) => {
  console.log('验证资源修改的middleware')
  // 获取要验证的表名和资源id
  console.log(ctx.params)
  const [ resourceKey ] = Object.keys(ctx.params)
  console.log(resourceKey)
  const tableName = resourceKey.replace('Id', '')
  const resourceId = ctx.params[resourceKey]
  // 获取用户的id
  const { id } = ctx.user
  // 查询此用户是否有修改的权限
  try {
    let result = await AuthService.checkSource(id, resourceId, tableName)
    console.log(result)
    if(!result) {
      throw new Error()
    }
    console.log('资源修改验证成功')
    await next()
  }catch(err) {
    const error = new Error(errorType.UNPERMISSION)
    return ctx.app.emit('error', error, ctx)
  }
}
```

在`auth.service.js`新增checkSource，将指定的用户id和资源id一起传入查询，如果查询到则说明此资源属于该用户

```
const connection = require('../app/database')

class AuthService{
  async checkSource(userId, SourceId, tableName) {
    const statement = `
      SELECT * FROM ${tableName}
      WHERE id = ? AND user_id = ?;
    `
    const [result] = await connection.execute(statement, [SourceId, userId])
    return result.length ? true : false
  }
}

module.exports = new AuthService()
```

新增校验资源中间件后又开始做重复的工作了，新增修改动态路由：

`moment.router.js`

```
momentRouter.patch('/:momentId', verifyAuth, verifyPermission, update)
```

`moment.controller.js`

```
/* 修改动态 */
async update(ctx, next) {
    // 获取用户id和修改的内容
    const { content } = ctx.request.body
    const { momentId } = ctx.params
    // 修改动态
    const result = await MomentService.update(momentId, content)
    ctx.body = result
}
```

`moment.service.js`

```
/* 修改动态 */
async update(momentId, content) {
    const statement = `
        UPDATE moment SET content = ? WHERE id = ?;
    `
    const result = await connection.execute(statement, [content, momentId])
    return result
}
```

### 3.5.删除动态

删除动态的逻辑和修改动态几乎一样，唯一的区别是只需要获取到动态的id删除数据库记录即可

## 04.评论接口

创建评论表：

```
CREATE TABLE `comment` (
  `id` int NOT NULL AUTO_INCREMENT,
  `content` varchar(1000) NOT NULL,
  `moment_id` int NOT NULL,
  `user_id` int NOT NULL,
  `comment_id` int DEFAULT NULL,
  `createAt` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updateAt` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `moment_id` (`moment_id`),
  KEY `user_id` (`user_id`),
  KEY `comment_id` (`comment_id`),
  CONSTRAINT `comment_ibfk_1` FOREIGN KEY (`moment_id`) REFERENCES `moment` (`id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `comment_ibfk_2` FOREIGN KEY (`user_id`) REFERENCES `user` (`id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `comment_ibfk_3` FOREIGN KEY (`comment_id`) REFERENCES `comment` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
)
```

### 4.1.评论动态

评论目前是针对某条动态的，所以评论动态需要获取到用户的id，动态的id，评论的id

`comment.router.js`

```
/* 发表评论接口 */
commentRouter.post('/', verifyAuth, create)
```

`comment.controller.js`

```
class CommentController{
  /* 发表评论 */
  async create(ctx, next) {
    // 获取用户id，动态id，评论内容
    const { id } = ctx.user
    const { content, momentId } = ctx.request.body

    // 创建评论
    const result = await create(id, momentId, content)
    ctx.body = result
  }
} 

module.exports = new CommentController()
```

`comment.service.js`

```
/* 创建评论 */
async create(userId, momentId, content) {
  const statement = `
    INSERT INTO comment (content, user_id, moment_id) VALUES (?, ?, ?);
  `
  const [result] = await connection.execute(statement, [content, userId, momentId])
  return result
}
```



### 4.2.回复评论

### 4.3.修改评论

### 4.4.删除评论

### 4.5.获取评论列表

## 05.标签接口

### 5.1.创建标签

### 5.2.给动态添加标签





