# NodeJS 概述
* Node.js 是一个在服务器端运行的JS开放源代码跨平台运行环境
* Node.js Node采用谷歌开发的V8引擎 来运行JS代码
* 使用以下技术提高性能，优化应用的传输性能
    * 事件驱动
    * 非阻塞
    * 异步I/O模型
* Node.js仅仅对ES进行了实现，其中不包含DOM 和 BOM (无windows对象)


# NodeJS 的使用 
* 模块化
    * 在NodeJS中一个JS文件就是一个模块
    * 在NodeJS中，每个JS文件中的代码都独立运行在一个函数作用域中的，而不是在模块的全局作用域(global)中，一个模块中的某些变量无法被其他函数访问

* 模块分类
    * 核心模块
        * 由Node引擎提供的本地模块，require标识为模块的名字
    * 文件模块
        * 用户自定义模块，require标识为模块的相对路径

* 向外暴露变量或方法
```JavaScript
exports.x  = "...";
exports.fn = function (){
    ...
}
```

* 引入外部模块，会返回一个外部模块对象
```JavaScript
var moduleObject = require("文件相对路径");
```

* 全局对象 global ，类似于网页js中的window，要想声明全局变量，去掉let 或者 var 即可

* 在执行模块代码时候，NodeJS会将执行模块中的代码使用一个函数包裹起来
```JavaScript
function (exports, require, module, __filename, __dirname) { //传递5个参数
    //要执行的模块代码.....
}
```
* exports 用于将函数内部变量暴露到外部(module的属性，相当于module.exports)
    > 为什么exports 和 module.exports 相同呢？
       在默认模块中，会默认执行以下语句
    ```JavaScript
    var module = {...}
    module.exports = {...}
    var exports = module.exports;
    ```
* require 用于引入外部模块
* module 代表当前模块本身
* __filename 当前JS模块文件的绝对路径
* __dirname 当前JS模块文件所在文件夹的绝对路径

## 包(Package)
* 实质为一个压缩文件
* 将多个相关的模块组合到一起，形成一个完整的工具包
* 包规范：
    * 包结构：用于组织包中的各种文件 
        * package.json 描述文件 (必须)
        * 文件夹
            * bin 二进制文件 (非必须)
            * lib JS代码文件 (非必须)
            * doc 使用文档 (非必须)
            * test 单元测试文件 (非必须)
    * 包描述文件：描述包的相关信息，提供给外部读取分析

## Node 包管理 (Node Package  Manager / NPM)
* 命令
    * 在当前目录初始化npm
    > npm init

    * 查看npm的所有已下载的包
    > npm version

    * 在当前目录安装npm 包
    > npm i/install 包名

    * 安装包并添加到当前项目依赖中
    >npm install  包名  --save

    * 下载当前项目指定依赖的包
    > npm install 

    * 全局安装包
    > npm install 包名 -g  

    * 在当前目录删除 一个npm 包
    > npm r/remove 包名

    * 设置镜像源
    > npm config set registrt 地址 

* npm 搜索包流程：
当npm 在使用模块名来引入模块时 搜索目录下 npm_modules文件夹中的内容，会从当前目录搜索向上搜索到磁盘根目录，无则报错

## Buffer 缓冲区(结构类似数组)
* Buffer为NodeJS内置对象
* 数组无法存储二进制数据(图片，音乐，视频)，Buffer可以存储二进制数据(display in Hex) 每个元素范围为00 - FF，占用一个字节，并且拥有一块连续的内存空间，支持字符编码：`UTF-8 base64 ascii`

* 创建一个指定大小的Buffer(已废弃，不推荐)
```javascript
let buff = new Buffer(数字) //字节大小
```
* 从内存中 分配一个指定大小且连续的空间给Buffer对象，其中数据为全0，确定后无法修改大小
```javascript
let buff = Buffer.alloc(字节数)
```

* 从内存中 分配一个指定大小且连续的空间给Buffer对象，其中数据不为空，可能含有敏感数据
```javascript
let buff = Buffer.allocUnsafe(字节数)
```

* 通过索引操作Buffer中的内容, Buffer中数字输出一定是10进制  
```javascript
buff[0] = xx;
...
```

* 将一个字符串保存至Buffer
```javascript
let str = "this is a string";
let buff = Buffer.from(str)
let text = buff.write('text') // 返回写入的字节数
```

## Node与文件系统
* Node通过fs模块与文件系统进行的交互
```javascript
const fs = require("fs");
```
* 同步/异步 文件操作：不适合大文件的写入，性能差，容易导致内存溢出
    * 同步 文件打开 ，返回文件描述符，错误则抛出异常，需要不断监听fd来达到同步的效果
        ```javascript
        let fd = fs.openSync("文件相对路径", 读写模式, 可选linux权限)
        ```

    * 同步 文件写入 ， 
        ```javascript
        fs.writeSync(文件描述符, 写入内容, [内容写入的位置, 编码])
        ```

    * 异步文件打开，若写文件不存在，自动创建该文件，无返回值，结果(fd)在回调函数参数中，
         ```javascript
        fs.open("文件相对路径", 读写模式, callback(error,fd){
            if(error){
                error handle
            }else{
                file handle
            }
        })
        ```
    
    * 异步文件写入
         ```javascript
        fs.write(文件描述符, 写入内容, [内容写入的位置, 编码],callback(error, written, string){
            
        })
        ```

    * 关闭同步文件(必须)
        ```javascript
        fs.closeSync(文件描述符)
        ```
    * 关闭异步文件(必须)
        ```javascript
        fs.close(文件描述符, callback(error){
            if(error){
                error handle...
            }       
        })
        ```

    * 同/异步简单文件写入
        ```javascript
        fs.writeFileSync(文件路径, data,[可选设置])

        fs.writeFile(文件路径,data, [encoding:"",mode:"", flag:"a"], callback(error){
            //flag:"a" 为向文件中追加内容
        })
        ```
    * 同/异步简单文件读取, 读取返回值 data 为 Buffer 对象
        ```javascript

        let data = fs.readFileSync(文件路径, [可选设置])

        fs.readFile(文件路径, [可选设置], callback(error, data){
            // handle data/error
            if(error){
                //handle error
            }
            else{
               //handle data 
            }
        })
        ```

    ##  文件系统fs其它操作
* 验证文件是否存在(同步),返回布尔值
        ```JavaScript
        let isExixts = fs.existsSync(path)
        ```
    * 获取文件信息
        ```JavaScript
        fs.stat(path,callback(error, stat){
            //handle error/stat                                                                      ′
        })
        ```
## NodeJs流

Stream 是一种抽象接口，在nodejs中http服务器的request对象就是一个stream的实现,Stream类似于双方建立的数据传输通道，

所有Stream对象，都是EventEmitter类的实例，Stream对象常用的事件

* data 当有数据可读取的时候触发
* end 没有更多数据的时候触发
* error 读写Stream时发生错误时触发
* finish 所有数据写入完成，在发生之前触发

> 流式文件使用与读取较大的文件，可以分多次读取到内存中 

* 流式文件写入，创建可写流

  ```javascript
  let writeStream = fs.createWriteStream(文件路径, [可选参数]);
  writeStream.write(data);
  writeStream.write(data);
  writeStream.write(data);
  ...
  ```

* 监听流的事件(打开/关闭)来执行相应操作

  ```javascript
  writeStream.on("open", function(){
      //open handle
  })
  writeStream.on("close", function(){
      //close handle
  })
  ```

* 监听一个一次性的事件

  ```javascript
  writeStream.once("open/close", function(){
      //open handle
  })
  ```

* 关闭流

  ```javascript
  writeStream.close(); //关闭出端流
  writeStream.end();  //关闭入端流
  })
  ```

* 流式文件读取，创建可读流

  ```javascript
      let readStream = fs.createReadStream(文件路径, [可选参数]);
  ```

* 读取可读流数据，必须绑定 data事件,可能读取多次，每次以  65536字节 块 进行读取 在读取完成后自动关闭文件

  ```javascript
  let _fs = require('fs')
  // 创建可读流
  let readFileStream = _fs.createReadStream('fs.js')
  let data = ''
  
  // 设置可读流的文件编码
  readFileStream.setEncoding('utf-8')
  // 设置处理流的事件的回调函数
  readFileStream.on('data', function (_data) {
    data += _data
  })
  
  readFileStream.on('end', function () {
    console.log(data)
    console.log('读取完毕')
  })
  ```

* 管道流：免监听，提供了一个从输出流到输入流的机制 

  ```js
  readStreamFile.pipe(writeStreamFile)
  ```

* 链式流：冲一个输出流读取数据，创建多个流来，来层层过滤输出流的数据，换句话说，就是将流形成一条链，下一个流接收上一个流的数据，一般来操作管道流，常用于压缩和解压数据

  ```js
  // 使用zlib与链式管道流进行压缩数据
  let _fs = require('fs')
  let _zlib = require('zlib')
  
  // 压缩文件
  _fs.createReadStream('copy.txt')
    .pipe(_zlib.createGzip()) // 对输出的流数据使用zlib进行压缩
    .pipe(_fs.createWriteStream('copy.txt.gz'))
  
  console.log('压缩完毕')
  ```

  

## NodeJs 服务器

* node.js本身就是服务器，不需要web容器

## 使用NodeJs创建一个Http服务器

* 返回文本类型：
  * text/plain : 纯文本类型
  * text/html : html文本类型

```js
let _httpServe =  require('http') // 引入http模块
let port = 3302
// 创建一个http服务
_httpServe.createServer(function (request, response) {
  // 定义http头信息，报告http状态码以及内容格式,text/plain 为纯文本类型
  response.writeHead(200, {
    'Content-TypeL':'text/plain'
  })
  // 向客户端发送数据
  response.end('This is a simple Http server')
})
  .listen(port) // 监听端口号

console.log('服务器已经创建')
```

## 使用NodeJs读取文件

* 同步读取：

  ```js
  let _fs = require('fs')
  
  let _data = _fs.readFileSync('http_server.js')
  console.log(_data.toString())
  
  console.log('结束')
  ```

* 异步读取：

  ```js
  let _fs = require('fs')
  
  let _data = _fs.readFile('http_server.js', (err,data) => {
    console.log(data.toString())
  })
  
  
  console.log('结束')
  ```

## NodeJs 事件

NodeJs是一个单进程单线程的应用程序，谷歌V8引擎提供了异步执行回调函数的接口，通过这些接口可以处理大量的并发请求，性能较高

NodeJs的所有事件机制，都是基于设计模式中的**观察者模式**来实现，

* NodeJs处理事件模型：
  1. 应用程序接收到请求
  2. 关闭当前的请求连接，处理这个请求
  3. 将请求放入处理队列中进行处理
  4. 处理完毕返回给发送请求用户

NodeJs的服务器一直为处理请求，不等待任何的读写操作，性能高，成为 **非阻塞式的I/O 或者 事件驱动I/O**

NodeJs使用event模块，对EventEmitter类进行实例化来绑定和监听事件

监听事件，使用`.on()`方法

绑定事件，使用`.emit()` 方法

```js
let _EventEmitter = require('events').EventEmitter
let _event = new _EventEmitter()
_event.on('request',function () {
  console.log('request')
})
_event.emit('request')
console.log(_event)
```

NodeJs里面所有`异步的I/O操作`，都会在完成时发送一个事件到`事件队列`

