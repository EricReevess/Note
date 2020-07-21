# 父子组件之间传递值的方法

1. 使用props
2. 子组件使用`$emit()`触发自定义事件，父组件在子组件标签上使用`@事件名="回调函数"`监听事件，在回调函数中的第一个参数为`$emit()`中传递的参数
3. 使用`this.$parement` 和 `this.$children` 传递：`this.$parent/children`可以获取到一个`父组件对象` 或者 一个`子组件数组`，对象中可以找到对应的状态数据
4. 使用`this.refs.XXX`访问使用ref="XXX" attribute的组件对象，得到data

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

        ```js
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

        ```js
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

   