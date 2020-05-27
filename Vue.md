# Vue 简介
* Vue 遵循MVVM 模式 ，动态显示数据
* 代码简洁，适合移动/PC开发
* 本身关注UI，带有UI库，可引入第三方库和插件
* 融合了Google公司的angular.js的模板和数据绑定技术，以及Facebook公司的React的组件化和虚拟DOM技术

# MVVM模式
* Model View  Virtual Model
    * Model 指数据模型，接受数据
    * View 指生成的视图模板，生成DOM对象
    * View Model 指的是 连接前者2个的视图模型，包括DOM事件监视以及数据绑定

# 使用Vue模板语法(基础)

* 在{{}} 以及 v-xxx="" 内部，均支持JavaScript单个表达式 ，例如三目运算以及数组方法 ，无法支持带有{}的判断表达式

* 属性动态参数
    * 在类似 v-bind 等指令中，需要使用标签属性来绑定对应数据，可将 参数 代替为标签属性，以便动态替换绑定的属性，
    参数是定义在数据模型中的，可更改。
    * 重要的是，参数不能带有大小字符，浏览器会默认将参数解析为小写字母，然后再vm属性中寻找该值

        ```html
        <a v-bind:[attribute_name]="url" ></a>
        ``` 
        ```JavaScript
        var vm = new Vue({
            ...
            data:{ 
                attribute_name: 'href',
                url:'xxx.com'
            }
        })
        ```

* 引入Vue.js并创建Vue 配置对象 app ,设置其参数，data中的所有数据均为app中的属性 app.prop ， 其中数据在指定的元素中使用{{prop}}进行访问 ，{{}}中为js语法
    ```javascript
    let app = new Vue({
        el:"元素选择器",
        data:{
            message: ...
            ...
        }
    })
    ```
* 使用v-once 将其加入对应元素行间 ，将内部插值部分只渲染一次，不会响应更新
    ```html
    <span v-once>{{message}}</span> message不会接受更新
    ```
* 使用v-html = "html代码"， 将元素替换成 相应的解析的html标签，但无法实现复合渲染
    ```javascript
    var vm = new Vue({
        ...
        data:{
            rawHtml:'<li>this is a li <a>link</a></li>'
        }
    })
    ```
    ```html
    <span v-html="rawHtml">{{rawHtml}}</span>   
    ```
    > 此时span标签变为rawHtml内容中的li标签 ，其中复合的a标签被省略，且内部插值会被全部忽略

## 定义元素行间属性，无法使用{{}}语法

* 使用v-bind:属性 将HTML元素标签属性 与Vue模型对象的data 数据绑定
    ```html
    <span v-bind:title = "message"></span>
    ```
* 在元素行间中使用v-if = 布尔值 来控制该元素的显示
    ```html
    <span v-if = "boolenValue"></span>
    ```
* 使用 v-for=" 子项 in 数组" 加入元素属性来遍历app.data中的数据到页面中
    ```html
    <li v-for="item in items">
        {{item}}
    </li>
    ```
* 使用 v-on:事件 = "回调函数" 加入指定元素的属性来 监听该元素的事件，回调函数在app.methods中声明
    ```html
    <button v-on:click="show">显示/隐藏</button>
    ```
* 使用v-model 加入表单元素实现 表单元素数据与Vue对象状态(数据)的双向绑定
    * 单行文本框 text 绑定value属性 
    * 复选框:如果是一个复选框 则可绑定 布尔值和 内容值(具体要参照绑定的数据类型)，如果是多个则绑定内容值 到同一个数组
    ```html
    <input type="checkbox" v-model="items"> 
    <input type="checkbox" v-model="isSelect"> 
    ```
## 缩写
* 在工程化构建单页应用(SPA)的时候，一些指令能被缩写成另一种形式

    * v-bind:attr="prop"          --->     :attr="prop"
    * v-bind:[attr]="prop"        --->     :[attr]="prop"
    * v-on:click="callback"      --->     @:click="callback"
    * v-on:[event]="callback"   --->     @:[event]="callback"

## 计算属性和监听器

## 计算属性
* 使用视图模型的计算属性 app.computed:{} ，用于 将data中的数据模型进行计算，必须使用return返回
    
    ```JavaScript
    computed:{
        属性1(){
            //计算data中的数据，this为顶级对象vm
            return xxx;
        }
        属性2 : function(){
            return xxx;
        }
    }
    ```
    * 计算属性的实质是调用了对应名 vm属性的一个getter 方法，计算属性默认只有 getter
    * 计算属性的实质是监听 计算时引用的 属性的变化
    * 计算属性在第一次计算后会对结果进行缓存
    * 如果参与计算的属性值发生变化，则会触发计算属性 重新进行 计算，更新缓存
    * 与 methods中的function 相比较，方法不会对上次计算的值进行缓存
    * 与 watch中的监视属性来触发callback 相比较 ，计算属性 在监听多个应用属性的时候拥有更少的代码

    * 计算属性的setter，在必要情况下，可自行设置setter,将改变更新到所引用的属性上
        ```JavaScript
        computed:{
            属性1:{
                get:function(){
                    return xxx+xxxx;
                },
                set:function(newValue){
                    this.xxx = newValue.xxx;
                    this.xxxx = newValue.xxxx;
                }
            }
        }
        //在外部使用vm.属性1 = string则可触发set方法
        ```

## 监听器
* 使用视图模型vm的监视属性 app.watch:{} ， 用于监视data中 数据变化，提供新旧数据
* watch适用于响应数据的变化，从而执行异步的或者开销比较大的操作，  因为能得到 新、旧两个值 ，对自定义监听有了更多的响应操作
    ```javascript
    ...
    watch:{
        data中的属性值 :function(newVal , oldVal){
            //当监视的值发送改变时，执行此回调函数 this为顶级对象vm
        }
    }
    ```
# 绑定Class 以及 Style
* Vue.js对 v-bind:class = "表达式" 进行了增强 , 并且能够在 自定义组件 中使用

    * 绑定对象，动态切换class，若原标签设有class ，则在基础上进行增加
        * 在内联中绑定一个对象, 冒号坐标为样式名字符串，右边为控制真值属性
            ```html
            <div v-bind:class = "{ active : isActive}"></div>
            或者
            <div v-bind:class = "{ 'active': isActive}"></div>
            或者
            <div v-bind:class = "{ 'active': isActive , error : isError , ...}"></div>
            ```
        > 注意，此处的 active 仅仅是指样式名，类型为字符串

        * 绑定一个在data中添加的属性对象
            ```javascript
            data:{
                classObj:{
                    active : isActive,
                    error : isError,
                    running : false
                }
            }
            ```
            ```html
            <div v-bind:class = "classObj"></div>
            ```
        * 绑定一个返回值为 对象的计算属性 
            ```javascript
            data:{
                isActive : true,
                isError : false,
            },
            computed:{
                return {
                    active : isActive && !isError,
                    error : isError
                }
            }
            ```
            ```html
            <div v-bind:class = "classObj"></div>
            ```
    * 绑定数组
        * 直接绑定一个数组，其中元素为 data中字符串的属性
            ```javascript
            data:{
                activeClass: 'active',
                errorClass: 'error',
                ...
            }
            ```
            ```html
            <div v-bind:class = "[activeClass, errorClass, ...]"></div>
            ```
            * 利用布尔值，在数组内部使用三元运算符
                ```html
                <div v-bind:class = "[ isActive ?  activeClass : '', errorClass, ...]"></div>
                ```
            * 利用布尔值，在数组内部混合使用对象   
                ```html
                <div v-bind:class = "[ { activeClass : isActive } : '', errorClass, ...]"></div>
                ```
# Vue组件
* 使用Vue对象.component()构建一个新组件
    ```javascript
    Vue.component('自定义标签名' ,{
        template: 'html标签 ',  //其中可直接通过{{自定义属性名}}访问绑定的数据
        prop:['自定义属性名']
    })
    ```

* 监听实例数据 data 变化，并触发渲染
    * vm对象中的data属性可以引用外部的数据对象，并指向同一内存单元，包括对其更改的响应，
    * Vue只对在创建时就已经存在于data中的属性变量 进行监听，若属性在创建vm对象之后加入，则不会进行监听
        ```javascript
        var data = {
            ...
        }
        var vm = new Vue({
            ...
            data:data
        })
        ```
        * 或者使用 Object.freeze() 阻止对数据对象更改一切监听
        ```javascript
        var data = {
            ...
        };
        data.freeze();
        var vm = new Vue({
            ...
            data:data   //此时data内属性改变，Vue无法监视变化，也不会使页面刷新
        });
        ```
* vm对象方法，暴露自身固有的属性
    * 通过访问以$开头的属性来访问自身固有属性
        ```javascript
        var data = {
            ...
        }
        var vm = new Vue({
            ...
            data:data
        })
        var vm.$data 
        console.log(vm.$data == data ) // true
        ```

# Vue生命周期 钩子函数
* 比如 created 钩子函数，执行在vm实例被创建后执行的代码，其中this指向vm对象
   ```javascript
    var vm = new Vue({
        ...
        created:function(){
            console.log(this) // vm
        }
    })
    ``` 
* 在vm的属性或者各种callback函数上，不要使用箭头函数，因为会因为this而无法找到对象