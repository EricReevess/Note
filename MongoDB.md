# 概念

* 数据库：存放集合，可以有多个，不需要手动创建
* 集合：类似于数组，存放文档，不需要手动创建
* 文档：数据的最小单位

![image-20200810170644701](C:\Users\Fusion\AppData\Roaming\Typora\typora-user-images\image-20200810170644701.png)

常用命令

* show dbs 查看数据库占用空间
* use 数据库名 进入到指定数据库
* db 显示当前所在的数据库
* show collections 显示数据库中所有的集合
* db.集合名.insert(js对象类型的键值对)
* db.集合名.find()  查找集合下的文档

## Mongoose

mongoose 是一个对象-文档模型，他对NodeJS原生的MongoDB模块进行了进一步的封装，提供了更多的功能，大多数亲情况下，他被用来把结构化的模式应用到一个集合中

* 并且提供数据验证和类型转换的功能
* 为文档创建一个约束使得数据结构合法
* 使用中间件
* 更容易驱动

核心对象：

* Schema（模式对象），约束了集合中的文档结构
* Model 将集合作为对象来表示，包含在内的所有文档
* Document 表示集合中的一个具体文档对象



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

