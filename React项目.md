# React 项目 ： 硅谷招聘(具有聊天功能)

## 项目功能

* 登陆功能
  * 求职者登陆
  * 老板登陆
* 注册功能，需要输入账号信息以及选择头像和填写个人信息，比如用用户类型
* 注销功能
* 查看职位信息功能（求职者）
* 查看求职信息功能（老版）
* 聊天功能：双方实时聊天，并且可以发送表情
* 历史消息记录：保留上次聊天的对象的消息
* 查看、修改个人信息功能

## 项目描述

1. 基于前后台分离的招聘的单页应用
2. 包括用户注册，登陆，求职者和招聘者的列表，实时聊天功能
3. 前端技术栈：使用React 全家桶 以及 Ant Design UI
4. 后端技术栈：基于NodeJs的服务端，使用MongoDB数据库
5. 使用模块化、组件化、工程化的模式进行开发

## 技术选型

* 前端部分（模块化）：React + react-router + redux + Ant Design UI
* 后台部分：NodeJs + express + MongoDB + mongoose + SocketIO
* 前后台交互
  * ajax请求部分：axios ，
  * 交互模式：async await 
  * 测试接口：postman
* 模块化：ES6 Babel
* 工程化项目构建：webpack + react脚手架 + eslint代码风格检查
* 辅助库：js-cookie 等

## 前端路由与组件

|        功能        |   相对路径(/)   |   组件(.jsx)   |
| :----------------: | :-------------: | :------------: |
|        注册        |    /register    |    register    |
|        登陆        |     /login      |     login      |
|      通用界面      |        /        |      main      |
|     老版主界面     |      /boss      |      boss      |
|    求职者主界面    |   /jobseeker    |   jobseeker    |
|    消息列表界面    |    /message     |    message     |
|    个人中心界面    |    /personal    |    personal    |
|  老板信息完善界面  |   /boss-info    |   boss-info    |
| 求职者信息完善界面 | /jobseeker-info | jobseeker-info |
|      聊天界面      |  /chat/:userid  |      chat      |
|      404页面       |   /not-found    |   not-found    |

主界面渲染逻辑

* 在渲染主路径以`/`之前先判断当前浏览器cookie是否拥有userId
  * 如果`userId`不存在，则直接跳转到登陆页面`/login`
  * 如果`userId`存在，再判断redux中是否存在`_id`
    * 如果`_id`存在，检查对应`userType`以及检查是否存在头像信息`avatar`
      * 如果请求的是根路径`/` ，则计算跳转的默认路由
        * 如果`avatar`存在，则直接渲染对应用户主界面
        * 如果`avatar`不存在，通过`userType`判断得到`redirectTo`重定向路由信息，跳转到对应用户的信息完善页面
    * 如果`_id`不存在，则说明网页被刷新，则使用cookie向服务器发送请求获取用户数据，渲染null

用户数据Schema:   users

|    字段名    | 字段类型 | 是否必要 |
| :----------: | :------: | :------: |
|   username   |  String  |    是    |
|   password   |  String  |    是    |
|   useType    |  String  |    否    |
|    avatar    |  String  |    否    |
|   position   |  String  |    否    |
| personalInfo |  String  |    否    |
| companyInfo  |  String  |    否    |
|    salary    |  String  |    否    |

聊天数据Schema:   chat

|   字段名    | 字段类型 |  是否必要   |
| :---------: | :------: | :---------: |
|   from_id   |  String  |     是      |
|    to_id    |  String  |     是      |
|   chat_id   |  String  |     是      |
|   content   |  String  |     是      |
|   hasRead   | Boolean  | 默认为false |
| create_time |  Number  |     否      |

* from_id 发送者id
* to_id 接受者id
* chat_id 聊天会话id，由发送接受者共同决定
* content 聊天内容
* hasRead 对方是否已读
* create_time 创建时间，排序使用

## API接口设计

* 端口：5000

* 用户注册接口

  * 路径：/register

  * 请求方式：POST

  * 接收参数：

    * username 
    * password ，
    * userType, administrator为管理员用户名

  * 注册成功：返回

    ```js
    {code:0,data:{ _id:'xxx', username:'xxx'}}
    ```

  * 注册失败：返回

    ```js
    {code:1,msg: '用户名已存在，或不可使用'}
    ```

* 用户登陆接口

  * 路径：/login

  * 请求方式：POST

  * 接收参数：

    * username
    *  password

  * 登陆成功：返回

    ```js
    {code:0,data:userDoc}
    ```

  * 登陆失败：返回

    ```js
    {code:1,msg:'用户名或密码错误'}
    ```

* 更新用户信息接口

  * 路径：/update-user-data

  * 请求方式：POST

  * 接收参数：

    * name 真实姓名
    * avatar 头像信息
    * position 职位信息（老板）
    * personalInfo 职位要求、个人简介
    * companyInfo 公司信息 （老板）
    * salary 薪资信息（老板）

  * cookie不存在，返回

    ```js
    {code:1,msg:'请先登录'}
    ```

  * 更新成功：

    ```js
    {code:0,data:user}
    ```

  * id查找到的信息不存在，更新失败，返回

    ```js
    {code:1,msg:'请先登录'}
    ```

* 获取用户信息接口（自动登录）

  * 路径：/get-user-data

  * 请求方式：GET

  * 接收参数：

    * userId cookie中的userId

  * 找不到userId 对应的用户信息，返回

    ```js
    {code:1,msg:'请先登录'}
    ```

  * 找到了，并且返回userId对应的用户信息

    ```js
    {code:0,data:user}
    ```

  
  
* 获取用户列表

  * 路径：/get-user-list

  * 请求方式：GET

  * 接收参数：

    * userType

  * 没有信息返回，返回：

    ```js
    {code:1,msg:'当前无内容'}
    ```

  * 寻找到信息，返回：

    ```js
    {code:0,data:usersDoc}
    ```

* 聊天接口

  * 获取当前用户所有聊天信息列表
    * 路径：/get-msg-list
    * 请求方式：GET
    * 接收参数：无
    * 返回：
      * 所有用户的id，姓名以及头像信息，因为不能确定向某个用户发送信息，使用用户的id作为对象属性名
      * 当前用户与某个用户的聊天信息数据结构,chatMessages 为数组类型，内部包含多个聊天信息对象

  * 修改指定消息为已读
    * 路径：/has-read-msg

    * 请求方式：POST

    * 接收参数：from_id

      * from_id 当前消息列表中对方的id
      * 在点击未读消息时，客户端向服务器发起当前请求，修改

    * 返回：

      * ```js
        {code: 0,data: msgDoc.nModified // .nModified返回修改的条目总数}
        ```

  * socket.io接口

    * url: ws://localhost:5000
    * 客户端使用单例模式进行监听

## 项目源码目录结构

* src
  * api  ajax请求相关的模块文件夹
  * assets 静态资源文件夹
  * components UI组件封装的模块文件夹
  * containers 容器组件模块文件夹（在此组装UI组件）
  * redux 状态管理相关模块
  * utils 工具类相关模块(md5 cookie socket.io等)
  * index.js webpack打包入口js文件

## npm相关内容

* 下载按需打包模块

  ```npm
  npm i --save-dev babel-plugin-import react-app-rewired
  ```

  打包报错解决方案：https://blog.csdn.net/zoepriselife316/article/details/88063171

* 下载less-loader以实现自定义样式，因为`antd`的样式使用less书写

  * css-loader出错解决方案：

    ```js
    addLessLoader({
        lessOptions:{
            javascriptEnabled:true ,
            ModifyVars:{  '@primary-color':'#eee'  } 
        }
    })
    ```



## 登陆/注册页面组件

* UI 均使用antd-mobile组件进行构建
* 登陆页面
  * 必要字段信息
    * 用户名：手机号、邮箱、和8-12为的字符串构成
    * 密码：就是密码
  * 按钮：
    * 登陆：发送请求验证身份，并进入主页面
    * 注册账户：跳转到注册页面 
  * 附加项：
    * 登陆cookie持久化，维持登陆一天
* 注册页面
  * 必要字段信息
    * 用户名：手机号、邮箱、或者8-12为的字符串构成
    * 密码：6-12位的字符串构成，需要至少包含一个大写和小写字母和数字
    * 确认密码
    * 用户类型：单向按钮，选择求职者和老板其中一个，默认为求职者
  * 按钮
    * 注册：发送请求并添加账户信息，并进入主页面
    * 登陆：跳转到登陆页面
* 用户信息完善页面
  * 老板信息完善
    * 必要字段信息，均为字符串
      * 真实姓名
      * 招聘职位
      * 公司名称
      * 职位薪资
      * 职位要求
    * 按钮
      * 多个默认头像选择按钮，点击即选择头像
      * 保存按钮，信息正确后调整到主页面
  * 求职者信息完善
    * 必要字段信息，均为字符串
      * 姓名
      * 求职岗位
      * 个人简介
    * 按钮
      * 多个默认头像选择按钮，点击即选择头像
      * 保存按钮，信息正确后调整到主页面

## 用户主页面

* 导航标题部分
* 内容部分
* 底部导航栏部分
  * 求职信息/职位列表
  * 消息管理
  * 个人中心

## 个人中心页面

* 展示用户的基本信息包括头像
* 提供一个红色的注销按钮，点击后询问是否注销用户

## 消息页面

* 显示收到的聊天消息，以时间先后排序
* 显示未查看的信息数量，在导航栏显示红点
* 聊天功能
  * 从列表页面点击即可发起聊天，切换到聊天页面
  * 双方都可以发送即时消息
  * 双方能够发送表情

## 封装axios

## Socket.IO

socket.io是一个能实现多人实时聊天的库，同时包装H5的WebSocket和轮询机制，来保证兼容性

## 解决跨域问题

使用代理解决：在`package.json`中添加代理

```json
"proxy": "http://localhost:5000"
```



## 构建一个简单的NodeJs后台

使用WebStorm的新建项目快速创建一个express框架的Node服务端

## 使用mongoose操作数据库

首先定义一个用户身份信息的约束文档结构Schema

```js
const userSchema = mongoose.Schema({
  username: { type: String, required: true },
  password: { type: String, required: true },
  type: { type: String, required: true },
  avatar:{type:String}
})
```

在定义一个用于操作集合的Model，其本质为构造函数

```js
const UserModel = mongoose.model('users', userSchema)
```

引入md5算法加密函数，用于密码加密，密码在插入数据库的时候需要通过加密函数转换为`hash散列值`

连接数据库，并测试CRUD

```js
// 连接数据库
mongoose.connect('mongodb://localhost:27017/test',
  { useNewUrlParser: true, useUnifiedTopology: true }
)
const mongooseConnection = mongoose.connection

mongooseConnection.on('connected', function () {
  console.log('Mongo数据库连接成功')
})

function saveTest () {
  const userModel = new UserModel({
    username: 'test',
    password: md5('654321'),
    type: 'jobSeeker'
  })
  userModel.save((err, userDoc) => {
    if (err){
      console.log(err)
    } else {
      console.log(userDoc)
    }

  })
}
saveTest()

// 测试查询数据，一个或多个
function findTest () {
  UserModel.find((err, usersDocArr) => {
    if (err) {
      console.log(err)
    } else {
      console.log(usersDocArr)
    }
  })
  UserModel.findOne({ _id: '5f32912c5a24d012779cf1ea' },
    (err, userDoc) => {
      if (err) {
        console.log(err)
      } else {
        console.log(userDoc)
      }
    })
}

findTest()
function updateTest () {
  UserModel.findByIdAndUpdate(
    {_id: '5f329d9fb913dd12e4d67a0a'},
    {username: 'test-update'},
    {useFindAndModify:false},
    (err,oldUserDoc) => {
      if (err) {
        console.log(err)
      } else {
        console.log(oldUserDoc)
      }
    })

}
updateTest()

function deleteTest () {
  UserModel.deleteOne( // 这里官方建议使用deleteOne() 和 deleteMany()
    {_id: '5f329d9fb913dd12e4d67a0a'},
    (err,delInfo) =>{
      if (err) {
        console.log(err)
      } else {
        console.log(delInfo)
      }
    })
}

deleteTest()
// 在删除之后返回的对象格式为{ n: 1, ok: 1, deletedCount: 0 }
```







## 通过这个项目学习到了什么？

* 前端部分
  * 一个比较完整的开发流程
  * 工程化、组件化、模块化的开发模式
  * 掌握使用react-create-app脚手架初始化项目
  * 学会使用react全家桶的SPA的开发，包括路由和状态管理
  * 学会使用react第三方UI组件库 Ant Design 构建页面
  * 学会用axios与后台进行数据交互
  * 学会使用 socket.io 库实现实时通信
  * 学会使用blueimp-md5 对登陆数据进行加密
  * 学会使用js-cookie 库操作浏览器cookie的数据
* 后台部分
  * 学会使用express框架以及mongoose进行NodeJs的后台开发
  * 学会使用express搭建后台路由
  * 学会使用mongoose来操作MongoDB数据库

