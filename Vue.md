# Vue 简介
* Vue 遵循MVVM 模式 ，动态显示数据
* 代码简洁，适合移动/PC开发
* 本身关注UI，带有UI库，可引入第三方库和插件
* 融合了Google公司的angular.js的模板和数据绑定技术，以及Facebook公司的React的组件化和虚拟DOM技术

# MVVM模式
* Model View  View Model
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
## v-cloak 放置模板内容闪现
* 处理页面刷新时，因渲染延时导致的模板内容出现在页面上的问题，需要配合css
    ```html
    <div v-cloak>{{message}}</div>
    ```
    ```css
    [v-cloak]{
        display:none;
    }
    ```
## 缩写
* 在工程化构建单页应用(SPA)的时候，一些指令能被缩写成另一种形式

    * v-bind:attr="prop"          --->     :attr="prop"
    * v-bind:[attr]="prop"        --->     :[attr]="prop"
    * v-on:click="callback"      --->     @:click="callback"
    * v-on:[event]="callback"   --->     @:[event]="callback"

## 计算属性和监听器

### 计算属性
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

### 监听器
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
                <div v-bind:class = "[ { activeClass : isActive } , errorClass, ...]"></div>
                ```

* 绑定内联样式 v-bind:style = "" ，可传递一个js对象或者数组
    * 绑定一个对象{} ，内部样式名 为驼峰式命名 ，其值为 vm对象的属性 ,如果传递进一个多重值，默认使用最后一个
        ```html
            <div v-bind:style = "{color : colorStyle , fontSize: fontSize + 'px'}"></div>
        ```   
    * 直接绑定一个vm对象属性 
        ```javascript
            data: {
                styleObj : {
                    color : 'red',
                    fontSize : '12px',
                    ...
                }
            }
        ```
        ```html
            <div v-bind:style = "styleObj"></div>
        ```
    * 使用数组，绑定多个样式对象
        ```javascript
            data: {
                activeStyle : {
                    color : 'green',
                    fontSize : '12px',
                    ...
                },
                activeError : {
                    color : 'red',
                    fontSize : '15px',
                    ...
                }
            }
        ```
        ```html
            <div v-bind:style = "[activeStyle, activeError]"></div>
        ```

# 条件渲染 v-if & v-else-if & v-else 
* 使用真值选择渲染一块内容 ，v-else-if & v-else  必须紧跟 v-if
    ```html
    <h1 v-if = "真值">This is True</h1>
    <h1 v-else-if = "真值">This is True</h1>
    <h1 v-else> This is False </h1>
    ```
* 在组件模板  template 上使用v-if，选择渲染模板， template 标签不会出现

    ```html
    <template v-if = "真值">
        <h1 >This is True</h1>
    </template>
    ```
* 使用key管理重复元素
    * 默认情况下，如果使用 v-if 和 v-else 来切换组件中，如果有相同的元素，切换时 ，只替换他们不相同的值，不会重新渲染DOM对象
    ```html
    <template v-if = "真值">
        <h1 >This is True</h1>
    </template>
    <template v-else>
        <h1 >This is false</h1>
    </template>
    ```
    * 使用key来 完全独立渲染 2个拥有相同元素的模板 ，切换时 ，2个元素完全重新渲染，数据会被清除 ，但没有key 属性的则会被高效利用
    ```html
    <template v-if = "真值">
        <h1 key="h1-true" >This is True</h1>
    </template>
    <template v-else>
        <h1 key="h1-false" >This is False</h1>
    </template>
    ```
* v-show 与 v-if 的区别
    * v-show 与v-if同样是执行条件渲染
    * v-show 渲染的DOM元素会永久保留在DOM树中，真值只是 切换 该元素的显示样式 display
    * v-if 则是按照真值来渲染DOM ，以真值决定是否出现在DOM树中

# 列表渲染 v-for
* 使用v-for渲染一个列表 在使用数组进行迭代时， 第二个参数 index 可迭代出索引 
* 最好使用v-bind:key来 跟踪每个生成节点的身份 , key的值最好来自v-for所迭代的index ，并且最好为 数值或者字符串 ，
* 由于Vue系统机制，key 在 元素属性中无法通过{{}} 来访问 ，建议 再使用v-bind:index 传入每个迭代元素的索引
* 在v-for 字符串中，可直接调用父域中的methods方法
* 在同一节点中 v-for 的优先级 比 v-if高 , 不推荐在同一元素上同时使用两者
    ```javascript
    data: {
        items: [
            { message: 'Foo' },
            { message: 'Bar' },
            ...
        ]
    }
    ```
    > 在此处使用 item of items 和 item in items 为相同结果，但在js中 for in 和 for of 截然不同，前者遍历对应数组的key，后者遍历对应数组的value
    ```html
    <ul>
        <li v-for="(item, index) in items" v-bind:key="index ">
           {{index}} -  {{item}}
        </li>
    </ul>  
    ```
* 使用v-for 迭代 一个对象属性时 ， 第二个 参数 propName 可以迭代出 每个prop的key , 第三个参数 index 可迭代出索引
    ```javascript
    data: {
        object : {
            name: 'ljp',
            sex : 'male',
            age : '18',
            ...
        }
    }
    ```
    ```html
    <ul>
        <li v-for="(prop, propName) in object" :key="index ">
          {{propName}} :  {{prop}}
        </li>
    </ul>  
    ```
    > 如果改变数组、对象当做数据的位置 ，Vue不会改变DOM的顺序，而是再其索引对应的DOM上重新渲染 

* 在组件上使用v-for ， 其中v-bind:key是必须的 , 
    * 推荐将迭代出的数据与组件属性prop进行绑定，这样做会进一步解除 组件与数据之间的耦合，这样在其他场合可以通过 访问 组件的属性 来 再次访问 迭代出的数据
    ```html
    <my-component
        v-for="(item, index) in items"
        v-bind:item="item"
        v-bind:index="index"
        v-bind:key="item.id"
    ></my-component>
    ```

# 检测更新状态

* 当使用一下方法改变数组数据时，会触发Vue的视图更新
    * push()
    * pop()
    * shift()
    * unshift()
    * splice()
    * sort()
    * reverse()

* 当调用如一下一些非改变原数组，而返回新数组的方法时，会触发视图更新，但Vue使用高效的操作，使用同原来数组大小的数组来替换，使得避免DOM被重新渲染，而是重新赋予新数据
    * filter()
    * concat()
    * slice()

* 监听实例数据 data 变化，并触发渲染
    * vm对象中的data属性可以引用外部的数据对象，并指向同一内存单元，包括对其更改的响应
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

# Vue事件处理
* 使用 v-on:事件="" 指令 ，可以在触发事件时运行一些js代码 , 或者直接调用methods中的callback
    ```javascript
    ...
    data : {
        count : 0
    },
    methods : {
        calllback : function (event) {
            count += 1;
        }
    }
    ```
    ```html
    <button v-on:click="count += 1 ">加1</button>
    <span>{{ count }}</span>
    <button v-on:click="callback">加1</button>
    <span>{{ count }}</span>
    ```
*  直接在内联中调用方法,传入指定参数，如果需要使用事件，在内联中将$event传入到callback即可在方法中访问event
   ```javascript
    ...
    data : {
        count : 0
    },
    methods : {
        calllback : function (message, event) {
            event.preventDefault;
            alert(message);
        }
    }
    ```
    ```html
    <button v-on:click="callback('hello', $event)">hello</button>
    ```
* 事件修饰符，用于例如阻止默认事件，阻止事件冒泡 等 , 需要注意修饰顺序 ，在冒泡结束后才会触发默认事件

    * .stop      在当前元素位置，阻止事件冒泡
    * .prevent  阻止默认事件，一般阻止表单的提交，或者链接 导致的刷新页面
    * .capture  当事件捕获到达当前元素的时候，先执行.capture指定的callback，再继续传播捕获事件
    * .self        只在当前元素触发事件时才会调用callback
    * .once      当前元素只触发一次事件
    * .passive   不阻止默认事件，例如滚动，与prevent冲突
        >   浏览器只有等内核线程执行到事件监听器对应的JavaScript代码时，才能知道内部是否会调用      preventDefault函数来阻止事件的默认行为，所以浏览器本身是没有办法对这种场景进行优化的。这种场景下，用户的手势事件无法快速产生，会导致页面无法快速执行滑动逻辑，从而让用户感觉到页面卡顿。  
            每次事件产生，浏览器都会去查询一下是否有preventDefault阻止该次事件的默认动作。我们加上passive就是为了告诉浏览器，不用查询了，我们没用preventDefault阻止默认动作。
            在滚动监听过程中，移动每个像素都会产生一次事件，每次都使用内核线程查询prevent会使滑动卡顿。我们通过passive将内核线程查询跳过，可以大大提升滑动的流畅度。    

    ```html
    <button v-on:事件.事件修饰1.事件修饰2...="callback">hello</button>
    <button v-on:click.stop.prevent="callback">hello</button>
    ```
    * 在事件捕获中，当捕获到达触发事件的元素时，先执行事件回调，再执行捕获回调，然后再进行冒泡，当冒泡使得上层的元素的回调触发全部完成之后，才会执行该元素的默认事件

* 按键修饰符，
    * Vue提供了以下常用功能键按键的别名
        * .enter
        * .tab
        * .delete (捕获“删除”和“退格”键)
        * .esc
        * .space
        * .up
        * .down
        * .left
        * .right 
            * 当事件为click时，
                * .left为 鼠标左键 
                * .right为 鼠标右键
            * 事件为keyxx键盘事件时
                * .left为 方向左键  ←
                * .right为 方向右键  →
        * .middle 只使用与鼠标事件



    * 通过增加全局属性来自定义别名
        ```javascript
        vm.config.keyCodes.F1 = 112;
        ```
    * Vue提供了以下常用系统功能按键的别名，
        * .ctrl
        * .alt
        * .shift
        * .meta 在windows系统上对应Win键，在mac系统上对应command键

        > 此类按键在与其他按键组合keyup的时候，需要按住系统功能按键，再触发普通按键keyup才会触发相应事件

    ```html
    <input v-on:按键动作.按键码1.按键码2="callback">
    <input v-on:keyup.ctrl.67 = "copy" >
    <input v-on:click.ctrl= "submit" > ctrl + 鼠标左键
    ```
    * .exact 修饰符，允许精准控制一个按键事件
        ```html
        <input v-on:click.ctrl.exact= "submit" > 只有 Ctrl 键被按下时才会触发

        ```

# 表单双向绑定
* 使用v-model 加入表单元素实现 表单元素数据与Vue对象状态(数据)的双向绑定
    * v-model会忽略绑定元素中绑定属性的初始值，将vm实例作为数据的来源

    *  text / textarea  
        * 绑定value属性  并可 抛出 input 事件 
        * textarea 文本插值不再生效
    * checkbox 可抛出 change 事件
        * 单个复选框 则可绑定 checked 布尔值
        * 多个复选框 则绑定value 到同一个数组 
    * radio 抛出 change 事件 只绑定 value
    * select 则 绑定 value 可抛出 change事件 
        * 如果v-model没有匹配到相应option标签的value ，则选择框会处于空状态
        * 多选时(属性增加multiple) ，将选择的option的value绑定到一个数组
        * 推荐使用 v-for 来渲染option
    ```html
    <input type="checkbox" v-model="items"> 
    <input type="checkbox" v-model="isSelect"> 
    <select v-model="selected">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
    </select>
    ```
* 双向绑定修饰符
    * .layz 懒同步，在input 的input事件结束后 change事件时同步
    * .number 将双向绑定的值自动转换为数值类型，因为input默认number类型中的value仍然是String
    * .trim 自动清除输入两侧的空格

# Vue组件基础
* 使用Vue对象.component()构建一个新组件，均为全局组件
    * 与Vue实例接收相同的选项，但只有根vm实例才拥有el属性，每个组件各自独立
    * 在组件中，data属性必须为一个返回值为对象的函数  而不是一个对象，因此每个组件可以获得自己独立的数据
    ```javascript
    Vue.component('自定义标签名' ,{
        data:function () {
            return {
                xxx : xxx
            }
        }
        prop:['自定义属性名'],
        template: 'html标签 ',  //其中可直接通过{{自定义属性名}}访问绑定的数据
       
    })
    ```
* 通过prop向子组件传递数据
    * prop可以自定义一个 attribute ，在模板中可访问这个attribute
    * 在组件外部 ，可以通过v-bind绑定该attribute 来实现vm数据的传递
    * 不要使用过多的 自定义 attribute 来接收数据，尽量 在组件模板中进行访问一个集中数据的对象
    * 组件模板，只能有单个根元素
    ```javascript
    Vue.component('test', {
        props: ['page'],
        template: ' \
                        <div>\
                            <h3>{{ page.title }}</h3> \
                            <p>{{page.content}}</p> \
                       </div>  '
    })
    ```
    ```html
    <test v-bind:page="pageData"></test>
    ```

* 组件自定义事件 
    * 用于访问/操作 根实例中的data内数据   
    ```html
        <div>
            <h3>{{ page.title }}</h3> 
            <p>{{page.content}}</p> 
            <button v-on:自定义事件="JS语句">增大</button>
        </div> 
    ```
    * 或者使用 $emit('event')，来触发该事件，以调用根实例方法
        ```html
            <div>
                <button v-on:click="$emit('event')">增大</button>
            </div> 
        ```
        * 使用$emit()第二个参数  在父组件中使用$event 来访问这个参数
            * 子模板中，当 button 发送click时，触发自定义事件enlarge-text，并且这个事件抛出0.1这个值
                ```html
                <button v-on:click="$emit('enlarge-text', 0.1)">Enlarge text</button>
                ```
            * 根模板中，使用v-on监听enlarge-text事件，并且使用$event来接收enlarge-text事件触发所抛出的值
                ```html
                <blog-post
                ...
                v-on:enlarge-text="postFontSize += $event"
                ></blog-post>
                ```
        * 如果这个事件处理回调函数，则该函数的第一个参数则是子组件中$emit的第二个参数
            ```html
                <blog-post
                ...
                v-on:enlarge-text="enlarge-text">
                </blog-post>
            ```
            ```JavaScript
                methods: {
                    onEnlargeText: function (enlargeAmount) {
                        this.postFontSize += enlargeAmount
                    }
                }
            ```
    * 在组件上使用v-model双向绑定
        * 对于input绑定v-model来说相当于监听input事件
            ```html
            <input
            v-bind:value="searchText"
            v-on:input="searchText = $event.target.value">
            ```
        * 在组件中使用双向绑定，实际上使用$emit()的事件抛出
        ```javascript
        props: ['value'],
        template: `
            <input 
            v-bind:value="value"
            v-on:input="$emit('input', $event.target.value)">
        `
        ```

* 使用is attribute实现动态组件



# Vue组件高级

## 组件名命名规范
* Vue.component的第一个参数，字符串构成
* 推荐字母全小写且必须包含一个连字符 例如todo-list
##  局部注册
* 通过局部注册的组件在 子组件中均不可用
* 通过定义普通JavaScript对象来自定义组件，然后再根实例中于components属性内进行添加
    ```javascript
    var component1 = {...}
    var component2 = {...}
    var component3 = {...}
    var vm = new Vue({
        ...
        components : {
            '组件名' : 组件JS对象,
            'component-3' : component3
        }
    })
    ```
* 如果要使这种方式能在子组件中使用则在子组件对象内加入子组件
    ```javascript
    var component1 = {//...}
    var component2 = {
        components : {
            'component-1' :  component1
        }
    }
    var component3 = {//...}
    ```
## Prop
* HTML中对attribute的大小写不敏感，但浏览器最终会全部解析成小写字母
* 在定义组件时，prop对象内的attribute 在使用 驼峰式命名时，在父组件中使用该属性应当使用全小写并用 '-' 分隔开
    ```javascript
    ...
    props: ['todoItems'],
    ...
    ```
    ```html
    <component v-bind:todo-item="data"></component>
    ```
* 以对象的形式 对每个prop 进行类型验证，如果传入的数据类型错误，浏览器给予控制台提示
    * 每个prop 允许多个数据类型
    * prop 验证会在当前组件创建之前就进行验证，prop对验证组件自身数据域不起作用
    * null 和 undefined 会通过任何类型的验证
    * required 详细制定某个prop是否必要
    * default 定义prop 在没有接受数据 时的默认值
        * 带有默认对象的prop 同样使用返回对象的函数
    * 设定一个验证规则函数
    ```javascript
    ...
    props: {
        todoItems : [Array, Object...] ,
        propA : {
            type: [Number,...],
            required : true
        },
        propB : {
            type: Number,
            default : 200
        },
        propB : {
            type: Object,
            default : function () {
                return {
                    message : 'AMD YES'
                }
            }
        },
        propC : {
            validatFunction : function (isTrue){
                return  isTrue === true 
            }
        }
    ```
    * 验证prop 中 的valid 是否为 某个函数创建
    ```javascript
    function Validator (value){
        this.value = value
    }
    Vue.component('componentA', {
        prop : {
            valid : Validator
        }
    })
    ```
* 从组件元素行间 传入非prop中定义的 attribute , 这个 attribute 会被自动添加在组件模板的 根元素上
    ```html
    <bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
    ```

* 传入布尔值，当使用v-bind绑定一个attribute时，在没有任何值的情况下，均为true 
    ```html
    <!-- 这时不需要使用v-bind: -->
    <component is-display></component>
    ```
* 传入一个对象的所有属性，这会使这个对象的每一个属性都绑定成为一个attribute
    ```html
    <component v-bind="object"></component>
    ```
    等同于
    ```html
    <component 
        v-bind:prop1="object.prop1"
        v-bind:prop2="object.prop2">
    </component>
    ```
* 单向数据流
    * 所有的prop的传递都是父组件向子组件进行传递 ，这样可以有效的控制数据的流向，数据的使用清晰明了
    * 最佳的做法是将prop定义的attribute中传递进来的值在组件的data 或者 computed 对象内进行处理
        ```javascript
        props : {
            initData : Array
        } ,
        data: function () {
            return {
                initItem: this.initData
            }
        }
        computed : {
            computedData : function () {
                return {
                    initItem: this.initData
                }
            }
        }
        ```
* 替换/合并已有的 Attribute
    * 如果在组件内根元素拥有class或者style的情况下，在父组件中也加入不同的class或style，则渲染时会将他们合并到一起
    * 如果是其他的attribute ，则会直接替换
    ```html 
    <!-- 子组件模板 ,假设这个组件名为custom-input-->
    <input type="text" class="input-class-a"/>
    <!-- 父级组件模板 -->
    <custom-input type="passowrd" class="input-class-b"></custom-input>
    <!-- 渲染后的DOM-->
    <input type="password" class="input-class-a input-class-b"/>
    ```



* 禁用 attribute 的继承
    * 在组件内添加inheritAttrs: false 来禁止在父组件上对子组件进行attribute的添加，子组件不会自动获得在内联中获得的attribute
        ```JavaScript
        Vue.component('component', {
            inheritAttrs: false,
            // ...
        })
        ```
        > 注意 inheritAttrs: false 选项不会影响 style 和 class 的绑定。
        
    * 在禁止后，可以手动在组件内添加$attrs 来访问指定设置的attribute
        * 使用v-bind="$attrs"在子组件上继承父组件身上的 attribute 
        ```html
        <!-- 子组件模板 -->
        <div v-bind="$attrs">
            <input v-bind="$attrs">
            <!-- 虽然 inheritAttrs: false 但是通过这个方式能够手动进行传递attribute-->
        </div>      
        ```
    > 注意 这里的 数组和对象 均为 父组件的引用传入 ，改变这些值意味着改变父组件中的数据

## 自定义事件
* 与prop不同，事件名需要完全匹配才会触发相应事件，不存在驼峰到-的转换，但html中会对所有标签属性名进行小写转换，包括v-on，所以推荐使用- 连接命名

* 自定义组件的v-model的双向绑定属性
    * 使用组件model选项来指定双向绑定的prop，这个prop 仍然要在prop选项中添加
        ```javascript
        Vue.component('base-checkbox', {
            model: {
                prop: 'checked',    //指定绑定的prop
                event: 'change'     //指定触发更新的事件
            },
            props: {
                checked: Boolean 
            },
            template: `
                <input
                type="checkbox"
                v-bind:checked="checked"
                v-on:change="$emit('change', $event.target.checked)">
            `
        })
        ```
* 绑定原生事件到组件
    * 在通常情况下，父组件 在子组件根元素中 监听子组件触发的原生事件而发散的($emit())的自定义事件 ，调用父组件方法
    * 父组件 无法直接监听 子组件的js原生事件，可以通过事件修饰符 .native 来解决
        ```html
        <!-- 当点击到该组件区域时，添加修饰符.native 才会触发onClick-->
        <component v-on:click.native="onClick"></component>
        ```
* 简化实现 父组件 与 子组件的多个双向绑定 .sync
    * 一般情况下，在子组件内对prop进行与父组件prop进行同步 发散自定义事件父组件监听来更新
    * 使用了带.sync修饰符的v-bind仍然可以做到这一点
    ```html
    <!-- 传统自定义事件双向绑定 -->
    <text-document
    v-bind:title="doc.title"
    v-on:update:title="doc.title = $event">
    </text-document>

    <!-- 上下两者的功能是等效的 -->

    <!-- title.sync作为监听器 监听title的改变，并同步到父组件域的item.title中 -->
    <component v-bind:title.sync="item.title"></component>
    <!--  将item内所有的prop都添加各自的监听，相当于双向绑定所有prop ，""中只能是字面量，不能使用{ ... }直接传递一个对象-->
    <text-document v-bind.sync="item"></text-document>
    ```

## 插槽slot

* 通过插槽从父组件向子组件分发内容 ，且可访问插槽所在组件的父组件属性，不可访问插槽所在组件的prop属性
    * 父组件模板内
        ```html
        <todo-item v-bind:items="todoList">
            <p>此标签在父组件中创建，使用slot插入进这个子组件模板中{{父组件属性}}</p>
            <component-a></component-a>
            ...
        </todo-item>
        ```
    * 子组件模板内
        ```javascript
        template: ` 
            <div>
                <slot></slot>  p标签 其他组件 等会在渲染组件时，在此出现
            </div>
        `
        ```
* 设置插槽预备内容(默认值)
    * 直接在slot标签内容内写入默认值，若父组件不传递插值 ，则该默认值不会被替换
        ```html
        <button type="submit">
            <slot>Submit</slot>
        </button>
        ```
* 带有标识的插槽，实现多个插槽
    * 使用slot的特殊attribute name来区分不同的插槽，在name没有进行赋值的时候，name默认值为default
    * 在父组件中对相应插槽提供内容的时候，在template标签使用v-slot:name 指令，并包裹内容
    * 没有使用v-slot指令，或者没有v-slot指令参数 的template标签将视为默认插槽的内容，或者直接指定为v-slot:default
    * v-slot 只能添加在 template标签 上 ，或者只有默认插槽的组件上
    ```html
    <!-- 子组件模板 -->
    <div class="container">
        <header>
            <slot name="header"></slot>
        </header>
        <main>
            <!-- 默认插槽-->
            <slot></slot>
        </main>
        <footer>
            <slot name="footer"></slot>
        </footer>
    </div>
    <!-- 父组件提供内容 -->
    <component>
        <template v-slot:header>
            <!-- header内容 -->
        </template>
        <template v-slot:default>
            <!-- 默认插槽main的内容 -->
        </template>
        <template v-slot:footer>
            <!-- footer内容 -->
        </template>
    </component>
    ```
* 开辟插槽作用域
    * 显然，在父组件上的给子组件传递的插槽内容不能对子组件的prop或者data进行访问
    * 可以通过在子组件的slot标签上绑定prop 向上传递prop到 插槽作用域中 进行使用
    * 要使用传递出来的prop，需要在父组件上的子组件上 使用template 标签对slot标签进行包裹
    * 为其在template的v-slot:name指令 赋予一个prop名
    * 此时向上传递的子组件prop将保存在 template创建的prop中
    ```html
    <!-- 子组件模板 -->
    <span>
        <slot v-bind:user="user">
            {{ user.lastName }}
        </slot>
    </span>
    <!-- 父组件使用子组件 -->
    <current-user>
        <template v-slot:default="slotProps">
            {{ slotProps.user.firstName }}
        </template>
    </current-user>
    <!-- 被提供内容只默认插槽，可将v-slot加入到子组件内联中-->
    <current-user v-slot="slotProps">
            {{ slotProps.user.firstName }}
    </current-user>
    ```
* 插槽 prop 作用域解构，符合解构的，简化部分代码，可使用动态插槽名
     ```html
    <!-- 子组件模板 -->
    <span>
        <slot v-bind:user="user">
            {{ user.lastName }}
        </slot>
    </span>
    <!-- 解构赋值，将变量重命名 -->
    <current-user>
        <template v-slot:default="{ user : person }">
            {{ user.firstName }}
        </template>
    </current-user>
    <!-- 设置默认值-->
    <current-user v-slot="{ user = { firstName : 'John'}}">
            {{ user.firstName }}
    </current-user>
    <!-- 动态插槽值 -->
     <current-user v-slot=[propName]>
            {{ user.firstName }}
    </current-user>
    ```
* 当指令只有参数的时候(:后面的值) 插槽指令可缩写为 # ，可替换一切v-slot:
    * 
    ```html
    <!-- 子组件模板 -->
    <span>
        <slot v-bind:user="user">
            {{ user.lastName }}
        </slot>
        <template #header>
            <!-- header内容 -->
        </template>
    </span>
    <!-- 父组件使用子组件 -->
    <current-user>
        <template  #default="slotProps">
            {{ slotProps.user.firstName }}
        </template>
    </current-user>
    <!-- 这样写会触发警告-->
    <current-user #="slotProps">
            {{ slotProps.user.firstName }}
    </current-user>
    ```
* 使用插槽控制子组件的v-for的迭代样式
    ```html
    <!-- 子组件模板 -->
    <ul>
        <li
            v-for="(todo,index) in todoList"
            v-bind:key="index">
            <!-- 将todo向上绑定 -->
            <slot name="todo" v-bind:todo="todo">
            <!-- 设定默认显示为 todo.text -->
            {{ todo.text }}
            </slot>
        </li>
    </ul>
    <!-- 父组件内容 -->
    <todo-list>
        <template #todo="{ todo }">
        <!-- 这里可针对template进行样式/排版的的自定义-->
            {{todo.text}}
        </template>
    </todo-item>
    ```
## 动态组件、异步组件
### 动态组件
* is 属性
    * is用于解除html的模板嵌套限制 (例如ul中除了li而其他都不是合法元素的情况)，
    * is指示当前元素、组件的内容为其他内容
        ```html
        <!-- 父组件模板中 -->
        <ul>
            <!-- 在渲染时，li将被名为my-component的组件标签替换-->
            <li is='my-component'></li>
        </ul>
        ```
    * 动态的使用组件，在某个自定义标签上，用is来指出存在的子组件
        ```html
        <!-- 此处custom-component可以为任何合法值-->
        <custom-component is="my-component-a"></custom-component>
        ```
    * 可通过这种方法，将is绑定到父域的一个data中，再绑定click事件，对组件进行切换
        * 这种切换本质上为重新渲染关联使用is的组件，无法缓存已修改的组件的状态
        ```html
        <!-- componentName决定 该处内容-->
        <custom-component v-bind:is="componentName"></custom-component>
        ```
    * 使用keep-alive标签包裹，缓存使用is 属性进行切换的组件状态
        ```html
        <!-- keep-alive 中的组件不会因切换而被重新初始化-->
        <keep-alive>
            <custom-component v-bind:is="componentName"></custom-component>
        </keep-alive>
        ```
### 异步组件
* 异步组件用于接收从远程服务器发送到本地的组件，
* 这个组件只在需要被渲染的时候才会触发该组件的工厂函数，并且将会对其进行缓存
* Vue 允许以一个 异步 类似Promise构造器函数 的工厂函数的形式来定义一个组件
    ```JavaScript
    //此处 resolve 和 reject 均为函数类型
    Vue.component('async-component', function (resolve, reject) {
        setTimeout(function () {
            // 向 `resolve` 回调函数 中 传递组件的定义
            resolve({
            prop:[prop,...],
            ...
            template: '<div>I am async!</div>'
            })
            // 使用reject处理无法接受到组件内容而加载失败的情况
            reject(reason) 
        }, 1000)
    })
    ```
* 告知webpack 通过 ajax方式加载此组件
    ```JavaScript
    Vue.component('async-webpack-example', function (resolve) {
    // 这个特殊的 `require` 语法将会告诉 webpack
    // 自动将你的构建代码切割成多个包，这些包
    // 会通过 Ajax 请求加载
        require(['./my-async-component'], resolve)
    })
    ```
* 直接使用import 
    * 在工厂函数中返回一个Promise对象，以访问指定组件文件，或者在局部定义组件的位置返回一个Promise对象
    ```JavaScript
    Vue.component(
    'async-webpack-example',
    // 这个 `import` 函数会返回一个 `Promise` 对象。
    () => import('./my-async-component')
    )
    //父组件局部定义中
    new Vue({
    // ...
    components: {
        'my-component': () => import('./my-async-component')
    }
    })
    ```
    * 处理加载状态，在局部定义组件时，工厂函数可以以一下格式处理 组件 请求的状态
    ```JavaScript
    const AsyncComponent = () => ({
        // 需要加载的组件 (应该是一个 `Promise` 对象)
        component: import('./MyComponent.vue'),
        // 异步组件加载时使用的组件
        loading: LoadingComponent,
        // 加载失败时使用的组件
        error: ErrorComponent,
        // 展示加载时组件的延时时间。默认值是 200 (毫秒)
        delay: 200,
        // 如果提供了超时时间且组件加载也超时了，
        // 则使用加载失败时使用的组件。默认值是：`Infinity`
        timeout: 3000
    })
    ```
## 边界处理
* 访问根实例
    * 在根实例的所有子组件中，通过$root来访问其根组件的 data 计算属性，方法 等prop，包括读写
    ```javascript
    //子组件代码中
    let root = this.$root.rootData;
    this.$root.rootData = 2;
    this.$root.rootMedthod();
    ```
* 访问父级组件实例  
    * 所有子组件可以通过$root来访问其父组件内的 众多类型的prop 
    ```javascript
    //子组件代码中
    let parent = this.$parent.parentData;
    this.$parent.parentData = 2;
    this.$parent.parentMedthod();
    ```
* 访问子组件实例或子元素
    * 在父组件中访问子组件实例或者子组件prop，在子组件元素 中加入 ref attribute
    ```html
    <component ref="componentData"></component>
    ```
    ```javascript
    //父组件JS代码中，访问子组件实例
    let componentData = this.$refs.componentData
    ```  
    * 使用ref直接获取到DOM元素，$refs 得到的则是DOM对象
    ```html
    <input ref="input">
    ```
    *  v-for 的元素上使用 ref ，$refs.name 会返回一个数组

* 依赖注入
    * 当所有的子组件均需要使用父组件的方法获取信息，或者操作数据，显然使用$parent进行层级传递拥有较高的耦合
    * Vue为组件提供了 provide 和 inject选项 ，provide为后代组件提供数据/方法，inject则是指定接收那些方法
    * 依赖注入相对于$parent而言 ，使用$parent将暴露整个父级Vue实例，而依赖注入仅仅暴露方法/数据
    ```javascript
    //父级组件中，使用provide选项以返回值为对象 将自身方法暴露给后代
    provide: function () {
        return {
            getMap: this.getMap
        }
    }
    //父级组件的后代子组件中 ，每个组件均可以添加inject选项 来获得需要的方法
    inject: ['getMap']
    created:function(){
        this.getMap(...)
    }
    ```
* 程序化的事件监听
    * 除了直接在元素上使用v-on以外，Vue的实例也提供类似原生JS的事件监听方式
    * vue实例提供3种不同效果的监听器，事件名可以是从$emit发散的事件，也可以是实例本身的生命周期事件
        * $on(事件名, callback) 监听事件
        * $once(事件名, callback) 监听一次事件
        * $off(事件名, callback)  取消一个事件监听
    ```javascript
    mounted: function () {
        var picker = new Pikaday({
            field: this.$refs.input,
            format: 'YYYY-MM-DD'
        })
        //监听一次组件钩子函数，并在销毁组件同时清除对象
        this.$once('hook:beforeDestroy', function () {
            picker.destroy()
        })
    }
    ```
* 组件递归
    * 最典型的递归案例：父子递归，文件目录树
    ```html
    <!-- 首先父组件<tree-folder>的模板如下 -->
    <p>
    <span>{{ folder.name }}</span>
    <tree-folder-contents :children="folder.children"/>
    </p>
    <!--子组件<tree-folder-contents>模板如下 -->
    <ul>
    <li v-for="child in children">
        <tree-folder v-if="child.children" :folder="child"/>
        <span v-else>{{ child.name }}</span>
    </li>
    </ul>
    ```
    * 会发现，在互相渲染时，他们互相为对方的父级/子级 ，形成悖论 ，但在全局组件声明情况下，不会悖论会自动解开
    * 在 使用模块系统 或者 异步引入组件的情况下 ，悖论会成立，组件无法正常渲染
        * 使用生命周期函数延迟注册
            ```javascript
            //父组件<tree-folder>实例中
            beforeCreate: function () {
            this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue').default
            }
            ```
        * 在父实例中使用异步导入
            ```javascript
            //父组件<tree-folder>实例中
            components: {
            TreeFolderContents: () => import('./tree-folder-contents.vue')
            }
            ```
    * 使用v-once attribute 标记只需要渲染一次的静态元素
# Vue 过渡 以及 动画
* Vue 提供了 transition 的封装组件，可以使用 transition 标签包裹 以下节点 并产生过渡效果
    * 条件渲染 (使用 v-if)
    * 条件展示 (使用 v-show)
    * 动态组件
    * 组件根节点
* transition只能 同一时间渲染多个节点中的一个
* transition 提供一个name的prop来指定过渡前后的样式
    * name指定的对应css类的前缀名，不设置name将默认按照v-开头的css为准
    * 使用v-if的子组件在执行过渡的同时也会触发组件的动画钩子函数
    * 若系统找不到对应的过渡样式/动画,则执行单帧切换
    * 在隐藏过渡动画执行完成后系统会对该DOM对象 进行删除
    * 控制过渡样式的类名，默认以v-开头，或者以transition元素中的name开头，有以下可以控制切换的类
        * 控制 隐藏到显示 的过程 v-enter-active ，使用此类来定义在 隐藏到显示 过程中的 时间 延迟 和 曲线函数 ，必须定义
            * v-enter  定义 进入动作 开始的状态 ，一般与 v-leave-to相同
            * v-enter-to 定义 进入动作 结束的状态
        * 控制 显示到隐藏 的过程 v-leave-active ，使用此类来定义 在 显示到隐藏 过程中的 时间 延迟 和 曲线函数，必须定义
            * v-leave  定义 离开动作 开始的状态，一般与 v-enter-to相同
            * v-leave-to 定义 进入动作 结束的状态 
        * 一般来说，定义其中一套过渡(指 显示到隐藏 和 隐藏到显示 )即可完成切换过渡，为定义的另外2个类会作借鉴当前元素的默认样式
        ```html
        <transition name="fade">
            <p v-if="show">hello</p>
        </transition>
        ```
        ```css
        .fade-enter-active, .fade-leave-active {
            transition: opacity .5s;
        }
        .fade-enter, .fade-leave-to 
            opacity: 0;
        }
        ```
    * 执行动画
        * 只需 在v-enter-active/v-leave-active 中分别定义相反的动画，即可执行以动画为基础的过渡
        ```css
        .bounce-enter-active {
            animation: bounce-in .5s;
        }
        .bounce-leave-active {
            animation: bounce-in .5s reverse;
        }
        @keyframes bounce-in {
            0% {
                transform: scale(0);
            }
            50% {
                transform: scale(1.5);
            }
            100% {
                transform: scale(1);
            }
        }
        ```
* 自定义过渡类名，适用于与第三方的库进行结合
    * 通过添加一下attribute，自定义过渡类名，优先级均高于默认v-开头的类
        * enter-class
        * enter-active-class
        * enter-to-class (2.1.8+)
        * leave-class
        * leave-active-class
        * leave-to-class (2.1.8+)
        ```html
        <!-- 此处name为自定义，目的为能在 -->
        <transition
            name="custom-classes-transition" 
            enter-active-class="animated tada"
            leave-active-class="animated bounceOutRight"
        >
        ```
* 控制过渡的总体时间
    * 一般来讲 Vue会在 transitionend 或 animationend 事件后删除该过渡效果的DOM(对隐藏的操作而言)
    * 然而可以通过在transition 组件上的 duration prop 来在transitionend 或 animationend 触发之后(也就是css定义的过渡动画完成之后)，延迟删除该DOM元素，或显示该元素最终状态
        ```html
        <transition :duration="1000">...</transition>
        <transition :duration="{ enter: 500, leave: 800 }">...</transition>
        ```
* JavaScript钩子，使用JS的方式控制元素的动画
    * 通过监听动画/过渡生命周期
    * 对于仅仅使用JS来完成动画/过渡的元素，在attribute上添加v-bind:css="false" 使得在渲染时跳过css的检查，并且可以避免css的影响
        ```html
        <transition
        v-on:before-enter="beforeEnter"
        v-on:enter="enter"
        v-on:after-enter="afterEnter"
        v-on:enter-cancelled="enterCancelled"

        v-on:before-leave="beforeLeave"
        v-on:leave="leave"
        v-on:after-leave="afterLeave"
        v-on:leave-cancelled="leaveCancelled"
        >
        <!-- ... -->
        </transition>
        ```
        ```javascript
        methods: {
            // 进入中
            beforeEnter: function (el) {
                // ...
            },
            // 当与 CSS 结合使用时
            // 回调函数 done 是可选的
            enter: function (el, done) {
                // ...
                done()
            },
            afterEnter: function (el) {
                // ...
            },
            enterCancelled: function (el) {
                // ...
            },

            // --------
            // 离开时
            // --------

            beforeLeave: function (el) {
                // ...
            },
            // 当与 CSS 结合使用时
            // 回调函数 done 是可选的
            leave: function (el, done) {
                // ...
                done()
            },
            afterLeave: function (el) {
                // ...
            },
            // leaveCancelled 只用于 v-show 中
            leaveCancelled: function (el) {
                // ...
            }
            }
        ```
* 页面元素初始渲染的过渡
    * 使用appear attribute开启并设置 初始渲染的过渡
    * 整个appear拓展 attribute 与基础过渡类似，均为自定义样式
    * 同样也支持JS动画，使用js钩子函数
        ```html
        <transition
            appear
            appear-class="custom-appear-class"
            appear-to-class="custom-appear-to-class" (2.1.8+)
            appear-active-class="custom-appear-active-class"

            v-on:before-appear="customBeforeAppearHook"
            v-on:appear="customAppearHook"
            v-on:after-appear="customAfterAppearHook"
            v-on:appear-cancelled="customAppearCancelledHook"
            >
            <!-- ... -->
        </transition>
        ```
* 多个元素的过渡
    * 在transition内 多个 不同 原生 标签使用v-if/v-else-if/v-else进行切换，不用使用key attribute
    ```html
    <transition>
        <table v-if="items.length > 0">
            <!-- ... -->
        </table>
        <p v-else>Sorry, no items found.</p>
    </transition>
    ```
    * 但是在相同的 原生标签之间切换，需要使用不同的key attribute 值 来标记他们，才会实习过渡效果
    * 注意2个按钮都被重新绘制，即一个离开过渡的时候另一个开始进入过渡，这是 transition 的默认行为： 进入和离开同时发生
    ```html
    <transition>
        <button v-if="isEditing" key="save">
            Save
        </button>
        <button v-else key="edit">
            Edit
        </button>
    </transition>
    <!-- 或者可以重写为 -->
    <!-- 通过不同的key来设置不同的内容 -->
    <transition>
        <button  :key="isEditing">
            {{isEditing? 'Save':'Edit'}}
        </button>
    </transition>
    ```
    * 过渡模式
        * 如果不希望 进入和离开的过渡同时发生，在transition上的mode prop中进行自定义设置
            * in-out 先让新元素进入过渡，完成后旧元素才能离开
            * out-in 先让旧元素离开过渡，完成后新元素才能进入
        * 注意2个按钮都被重新绘制，即一个离开过渡的时候另一个开始进入过渡，这是 transition 的默认行为： 进入和离开同时发生
        ```html
        <transition name='fade' mode='in-out'>
            <button v-if="isEditing" key="save">
                Save
            </button>
            <button v-else key="edit">
                Edit
            </button>
        </transition>
        ```
        * 如果使用多个v-if来切换元素，可用计算属性将其简化
        ```html
        <transition>
            <button v-if="docState === 'saved'" key="saved">
                编辑
            </button>
            <button v-if="docState === 'edited'" key="edited">
                保存
            </button>
            <button v-if="docState === 'editing'" key="editing">
                取消
            </button>
        </transition> 
        <!-- 可以改写为 -->
        <transition>
            <button :key="docState">
                {{btnMessage}}
            </button>
        </transition>
        ```
        ```JavaScript
        ...
        computed:{
            btnMessage:function (){
                switch(this.docState){
                    case 'saved': return '编辑'
                    case 'edited': return '保存'
                    case 'editing': return '取消'
                }
            }
        }
        ```
* 拥有多个组件的过渡
    * 多个组件中每个组件不需要key attribute ,而是使用实现动态组件的 is attribute
    * 通过改变is指明的组件名来切换组件，实习过渡效果

* 列表过渡
    * transition只适用于渲染一个元素的过渡切换，要渲染多个由v-for迭代的组件或者元素，需要使用transition-group组件
    * transition-group组件内部需要有一个真实的元素：默认为span，所有子项目都在这一span标签内部，可以通过在transition-group组件上的tag prop进行指向为其他元素
    * 过渡模式(过渡的先后顺序)在其中不可用，因为不存在切换元素
    * 被v-for迭代的元素必须绑定唯一的key attribute
    * 组件上name prop 指定的样式前缀的样式会应用在各个迭代项上
    ```html
    <!-- css样式与普通过渡样式组相同 -->
    <transition-group name="list" >
        <li v-for="(item, index) in todoItems" v-bind:key="item" v-bind:title="item">
            {{showNumber ? (index+1 + ' - ') : ' * '}}  {{item}}
            <button v-if="canDelete" v-on:click="itemDelete(index)"> 删除 </button>
        </li>
    </transition-group>
    ```
* 列表的排序过渡(列表子项目之间的位置过渡)
    * Vue内部 使用了一个叫 FLIP 简单的动画队列使用 transforms 将元素从之前的位置平滑过渡新的位置
    * 排序过渡会将列表子项之间的位置变动进行过渡处理，要使用到v-move 这个新class v-为前缀，设置name可将其替换
    * 在使用v-move的同时，在v-leave-active类中需要添加绝对定位

# 混入(MixIn)
* 混入提供了非常灵活的方式，使用外部定义的对象进行混入任意组件选项，其外部定义的对象直接添加到组件内部的选项中
* 使用Vue.extend()定义一个混入的组件对象
    ```javascript
    var mixObj = {
        created:function(){
            this.hello()
        },
        methods:{
            hello:function(){
                console.log('hello')
            }
        }
    }
    // 定义ComponentDemo为混入组件
    var ComponentDemo = Vue.extend({
        mixins = [mixObj]
    })
    //将ComponentDemo实例化
    var componentA = new ComponentDemo () 
    ```
* 混入对象数据会与现有组件的数据合并，但当数据产生冲突时，保留原组件数据
    * 同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。
    * 值为对象的选项，都将合并为一个对象，键名冲突时，最终使用原组件的键值
    ```javascript
    var mixObj = {
        data:function(){
            return {
                message:'no',
                status:true
            }
        },
        created:function(){
            console.log('mixin call')
        }
    }
    new Vue({
        mixins:[mixObj],
        data:function(){
            return {
                message:'yes',

            }
        },
        created:function(){
            console.log('original call')
        }
    })
    //合并后的data： {message:'no', status:true}
    //mixin call => original call
    ```
* 全局混入，将混入对象混入到Vue顶级对象中，他会影响混入之后的每一个Vue实例
    ```JavaScript
    Vue.mixin({
        mounted:function(){
            this.$data.status ? console.log(this.$data.status) : null          
        }
    })
    new Vue({
        data:function(){
            return {
                status:true
            }
        }
    })
    //当实例被挂载时 ， => true
    ```
* 自定义混入选项合并策略
    * 自定义选项使用默认策略，如果想改变策略，需要向Vue.config.optionMergeStrategies中添加一个方法：
        ```javascript
        Vue.config.optionMergeStrategies.custonOption = function(toValue, fromValue){
            //返回合并后的内容
        }
        ```
    * 当然可以将各个选项的默认策略做为自定义策略的参照
        ```javascript
        Vue.config.optionMergeStrategies.custonOption = Vue.config.optionMergeStrategies.methods
        //示例
        //将merge策略指定为默认的computed选项合并策略
        const merge = Vue.config.optionMergeStrategies.computed
        Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
            if (!toVal) return fromVal //如果目标值不存在，则返回新值
            if (!fromVal) return toVal  //如果新值不存在 ，则返回目标值
            return {    //两个都存在，处理冲突
                getters: merge(toVal.getters, fromVal.getters),
                state: merge(toVal.state, fromVal.state),
                actions: merge(toVal.actions, fromVal.actions)
            }
        }
        ```
# 自定义指令
* 全局注册自定义指令，使用顶级对象Vue.directive()
    ```javascript
        // 注册一个全局自定义指令 `v-focus`
    Vue.directive('focus', {
        // 当被绑定的元素插入到 DOM 中时……
        inserted: function (el) { //el为操作的元素DOM
            // 聚焦元素
            el.focus()
        },
            //更多钩子函数的处理
    })
    //或者不使用钩子函数
    Vue.directive('focus', function(el,binding){
        el.style... //修改DOM对象
        //不触发任何钩子，而直接执行的语句

    })
    ```
* 在组件中局部注册，在组件的directives选项中添加对象
    ```javascript
     // 注册一个组件局部自定义指令 `v-focus`
    directives:{
        focus:{
            inserted:function(el){
                el.focus();
            },
            //更多钩子函数的处理
        }
    }
    ```
* 指令钩子函数，定义在自定义指令内部
    * 指令提供以下钩子函数：
        * bind 在元素进行绑定时调用，只执行一次
        * unbind 在元素与数据解绑时调用
        * inserted 在元素被插入父节点的时候调用
        * update 组件的虚拟节点更新时候调用
        * componentUpdated 所在虚拟子节点以及兄弟虚拟节点更新时调用
    * 指令钩子函数的参数
        * el 触发钩子函数的元素DOM对象
        * binding 一个对象，包括：
            * name 指令名 v-的后半段
            * value 指令绑定值 ，可以是任何JS合法值(字符串、数值、对象、数组等)
            * oldValue 指令前一个绑定的值，仅仅在updata和componentUpdated中使用
            * expression 指令的具体内容，JS表达式
        * vnode 当前元素对应的虚拟节点对象
        * oldVnode 当前元素的更新之前的虚拟节点对象，仅仅在updata和componentUpdated中使用
    > 注意：除了el对象之外，所有的参数均只读，不能被重新赋值  
* 自定义指令的动态参数
    * 自定义指令的参数可使用[]直接访问组件中的字面量
        ```html
        <p v-pin:[direction]="200">I am pinned onto the page at 200px to the left.</p>
        ```
        ```javascript
        //父组件中
        data: function () {
            return {
            direction: 'left'
            }
        },
        //子组件中
        directives:{
            pin:{
                bind:function(el,binding){
                    el.style.postion = "fixed"
                    let whichSide = binding.arg == 'top'? 'top':'left'
                    el.sytle[whichSide] = binding.value + 'px'
                }
            }
        }
        ```
# 过滤器
* Vue 允许使用自定义过滤器，过滤器可用于两个位置：双花括号内、v-bind表达式中，并使用管道符号| 作为间隔
    ```html
    <div>{{message  | filterName}}</div>
    <div v-bind="message  |  filterName"></div>
    ```
* 在组件选项内定义自定义过滤器，当全局过滤器和局部过滤器重名时，会采用局部过滤器。
    ```js
    //局部定义过滤器
    filters: {
        capitalize: function (value) {
            if (!value) return ''
            value = value.toString()
            // 返回第一个字母大写加上后续内容的拼接字符串
            return value.charAt(0).toUpperCase() + value.slice(1)
        }
    }
    //全局定义过滤器
    Vue.filter('capitalize', function (value) {
        if (!value) return ''
        value = value.toString()
        return value.charAt(0).toUpperCase() + value.slice(1)
    })
    ```
* 过滤器函数总接收表达式的值 (之前的操作链的结果) 作为第一个参数。
    * 在上述例子中，capitalize 过滤器函数将会收到 message 的值作为第一个参数。
    * 过滤器可串联，从左往右依次进行过滤操作
    * 过滤器本质为JS函数，当然也能接受参数，但每个过滤器的第一个参数均为管道左端传递的值
    ```html
    {{message | filter1(arg2,arg3, ...) | filter2 | ...}}
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


# 使用脚手架搭建Vue项目
使用Vue-cli搭建基础Vue结构
项目结构：
    * build ：存放打包所使用的webpack文件
    * config 存放运行时的服务器设置文件
    * node_modules 存放依赖
    * src 项目源代码
        * assets
        * components 存放Vue子组件
        * App.vue 为根组件代码
            * data 在所有组件内部 
                * data必须为返回值为对象的函数！！！目的是为了返回一份数据拷贝，因为可能在多处调用同一组件，为了不让多处的组件共享同一data对象，只能返回函数。
        * main.js webpack的打包入口js文件，同时也是 实例化整个Vue项目的js文件
    * static 放置静态资源
    * index.html SPA的主html 文件

编码结构：
    * 与非工程化构建Vue应用不同，工程化的Vue项目将所有组件都放入一个文件夹中统一管理，类似JS模块
    * 工程化的 组件.vue文件将组件代码分成三段：组件模板、JS代码、组件样式
    * 每个组件都有自身独立的样式，互不干扰
    * 在每个子组件.vue中，JS代码均暴露一个对象： 构成当前组件的所有选项
    * 在根组件.vue中，JS代码将引用子组件的所有暴露的对象，添加到组件自身选项components中，同样也暴露根组件的选项，
    * 在main.js中，通过引入Vue对象和根组件选项对象，将根组件实例化，并将递归实例化所有组件
    * 通过main.js打包好的js文件放入HTML给浏览器执行

## 打包Vue项目
