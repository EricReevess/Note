# 父子组件之间传递值的方法

1. 使用props
2. 子组件使用`$emit()`触发自定义事件，父组件在子组件标签上使用`@事件名="回调函数"`监听事件，在回调函数中的第一个参数为`$emit()`中传递的参数
3. 使用`this.$parement` 和 `this.$children` 传递：`this.$parent/children`可以获取到一个`父组件对象` 或者 一个`子组件数组`，对象中可以找到对应的状态数据
4. 使用`this.$refs.XXX`访问使用ref="XXX" attribute的组件对象，得到data，这种方法只能从父级组件访问子级组件

# 非父子组件之间的传值方式

1. 事件总线：建立一个公共的`js`文件，专门用来传递消息

   1. 建立一个`bus.js`文件并创建一个`Vue`实例，

      ```js
      import Vue from 'vue'
      export default new Vue
      ```

   2. 在要使用事件总线的组件中 import `bus.js`

   3. 发送数据的组件使用`bus.$emit(自定义事件名,传递值)`来触发事件

      ```js
      methods: {
          sendMsgWithBus(){
              bus.$emit('busMsg','bus msg ')
          }
      }
      ```

   4. 接收数据的组件在 `mounted(){}`中 使用`bus.$on(自定义事件名,回调函数)`,回调函数第一个参数为传递值

      ```js
      mounted() {
          bus.$on('busMsg',this.receiveBusMsg)
      }
      ```

2. `$attrs / listeners`

   * `$attrs`传递值

     1. 将数据绑定到中间组件标签上

        ```js
        //App 组件中
        <Parent :appMsg="appMsg"/>
        ```

     2. 在中间组件中对接收数据的组件 绑定`$attrs`

        ```vue
        //Parent组件中
        <Child v-bind="$attrs"/>
        ```

     3. 在接收数据的组件中，使用`this.$attrs.XXX`获得数据，如果目标的props中有预设属性，则在`attrs`中同名的将会被忽略掉

        ```js
        mounted() {
            console.log(this.$attrs.appMsg)
        }
        ```

   * `$listeners`传递上层的监听事件

     1. 假如有A B C  三个组件， 他们的分级为:祖A-父B-子C，现在祖组件A上只能给他的子组件B添加监听，但在作为父的B组件中，可以使用`this.$listeners`获取到A组件在其身上绑定的监听

     2. 要使监听事件跨级传递，需要将`$listeners`绑定在他的子组件C上，这样C组件使用`this.$emit()`来触发祖组件A上的事件

        ```vue
        //A 组件中，监听B组件触发的事件showChildMsg
        <B @showChildMsg="showChildMsg"/>
        //B 组件中，将 $listeners 绑定到 C组件上
        <C :msg="'hello'" @showMsg="showMsg" v-on="$listeners"/>
        //C 组件中，触发事件的函数
        methods: {
            sendMsg() {
                this.$emit('showChildMsg','this is a msg')
            }
        },
        ```

        

3. `vuex`

# 路由

1. 通过`router-link`标签设置链接进行跳转，以及设置参数，命名路由

   ```js
   //router的js文件中
   const router = [
       {
       path:'./home/:id',
       name:'home'，
       //.....
       }
   ]
   ```

   ```vue
   <!-- vue组件中 -->
   <router-link to="/home/2">Home</router-link>
   <router-view></router-view>
   ```

   

2. 通过编程式路由进行跳转：在回调函数中使用`this.$router.push({path:'路径'})`，使用`path`的时候，使用`query`来传递参数，使用`name`的时候，用`params`来传递参数

    ```vue
   <button @click="toHome">Home</button>
   methods: {
       toHome() {
       	this.$router.push({path: './home',query:{name:'ljp'}})
       	this.$router.push({name: 'home',params:{name:'ljp'}})
       }
   },
   ```

3. 导航守卫

   进入路由后所触发的钩子函数

   ```js
   //入口文件main.js中
   router.beforeEach((to,from,next)=>{
       //to代表目标路由
       //from代表发起跳转的路由
       //必须调用next()，才会运行路由跳转
   })
   ```

# Element UI 使用

看文档

# Axios的使用

* 配置拦截器：

  1. 在项目`src`下建立一个`api`文件夹

  2. 在`api`文件夹下创建一个`config.js`配置文件

  3. 引入`axios`，配置 超时时间 请求拦截器和响应拦截器：

     ```js
     import axios from 'axios'
     
     const service = axios.create({
       // 请求超时时间
       timeout: 3000
     })
     // 请求拦截器,promise对象
     service.interceptors.request.use(
       config => {
         return config
       },
       err => {
         console.log(err)
       }
     )
     // 响应拦截器
     service.interceptors.response.use(
       // 拦截响应数据,根据不同状态码响应不同操作
       response => {
         const res = {}
         res.status = response.status // 接收状态码,可进行判断
         res.data = response.data
         return res
       },
       err => {
         console.log(err)
       })
     ```

  4. 将配置好的`axios` 挂载到`Vue`的显式原型`prototype`上，在任何Vue实例中都可以通过`this.$httpRequest`来发送ajax请求

     ```js
     import HttpHandler from './api/config'
     Vue.prototype.$httpRequest = HttpHandler // 将axios实例拦截器挂载到Vue上,访问this.$httpRequest
     // 组件中
     mounted:{
         this.$httpRequest.get('url').then(
         res => {},
         err => {}
         )
     }
     
     ```

     

# Mock.JS模拟后台

# 使用Mock.js模拟后台接口

* 作用，生成随机数据，拦截ajax请求

* 使用：

  1. 创建一个mock模块文件` mock.js`

  2. 配置mock选项，包括返回数据的延时，拦截的`url`和返回响应数据格式的函数等

     ```js
     import Mock from 'mockjs'
     import HomeDataApi from './homeDataApi'
     // 设置延时 1-3秒
     Mock.setup({
       timeout: '1000-3000'
     })
     // 使用正则匹配带有 /home/getHomeData 的请求
     Mock.mock(/\/home\/getHomeData/, HomeDataApi.getHomeData())
     ```

  3. 编写一个返回值为服务器的响应数据格式的函数

     ```js
     import Mock from 'mockjs'
     export default {
       // mock请求的回调函数
       getHomeData: () => {
         return {
           code: 2000,
           data: {
             videoData: [
               {
                 title: Mock.Random.title(),
                 value: Mock.Random.float(500, 1000, 0, 9)
               },
               {
                 title: Mock.Random.title(),
                 value: Mock.Random.float(500, 1000, 0, 9)
               },
               {
                 title: Mock.Random.title(),
                 value: Mock.Random.float(500, 1000, 0, 9)
               },
               {
                 title: Mock.Random.title(),
                 value: Mock.Random.float(500, 1000, 0, 9)
               }
             ]
           }
         }
       }
     }
     ```

  4. 在入口文件 main.js中引入以上与mock相关的文件



# Echarts 的使用

* 编写一个组件，在组件中引入echarts，封装`echarts`
* 创建选项变量，为不同类型的图表设置不同的选项
* 在`assets`文件夹中创建文件夹专门存放图表的样式`js`文件
* 用`props`接收父组件传递的数据
  * 图表信息数据
  * 图表类型
* 方法：
  * 初始化图表方法，通过类型创建不同图表
  * 初始化图表数据方法，通过类型渲染不同数据

# 项目技术选型

* `Vue`
* `Vuex`
* `Vue-Router`
* `axios`
* Echarts
* CSS预处理器：`Sass`
* `ESlint` 代码风格检查
* `Babel`ES语法转换

# 项目页面设计

## 页面样式

* 页面头部：
  * `Header`下方使用标签进行记录访问历史，并与侧边菜单栏具有相同路由效果
    * 处于对应页面，标签背景为蓝色，否则为白色
  * `Header`内有隐藏，显示侧边菜单栏按钮，和显示当前所在的位置
  * `Header`右侧显示用户姓名和头像、下拉菜单

* 字体：
  * 首选字体： `"PingFang SC"` ，
  * 备选字体：` Helvetica, Arial, sans-serif`
* 首页
  * 使用`Echart`展示各种数据图表
  * 展示当前登录用户基本信息
* 项目管理页、用户管理页面
  * 主要使用`ElementUI`组件构建 展示 以及编辑数据界面



# 前端实现权限管理思路

* 使用动态路由

  后台区分不同用户的权限，返回一组为用户生成的路由数据

  在用户名密码验证成功后，发送一个对应权限的路由数据请求

* 更改路由表
  * 将不同的路由进行分类：公共部分和个人部分
* 

# 项目功能

* 登陆功能

* 折叠菜单功能

* 记录菜单历史功能

* 主页面：可视化展示数据功能
  * 项目成交量
  * 用户订单总数
  * 订单总额
  
* 用户管理功能

  * 新增用户
  * 搜索用户
  * 更新用户信息
  * 删除用户
  * 用户权限管理
  * 分页用户列表

* 项目管理功能
  * 上传项目
  * 更新项目
  * 下载项目
  * 查看现有项目
  
  





