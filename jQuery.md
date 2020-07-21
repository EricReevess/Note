# jQuery

# jQuery核心
* $ 和 jQuery
    * jQuery.js向外暴露的就是 $ 和 jQuery 
* jQuery对象
    * 执行 jQuery 后 返回的对象为jQuery对象

# $
* 作为函数使用 $(参数)
    * 函数 作为参数，在DOM加载完毕后 自动执行回调函数
    * 选择器字符串 作为参数，查找匹配标签，封装成jQuery对象并返回
    * DOM 对象 作为参数，将DOM对象封装成jQuery对象返回
    * html标签字符串 作为参数， 创建该标签并封装为jQuery对象
* 作为对象使用 $.method
    * $.each() 遍历数组
    * $.trim() 去除字符串两端空格

# jQuery 选择器(具体参考文档)
* css系列规范选择器
* 过滤选择器
    * element:not(.box)
* 表单选择器

# jQuery 对象操作对象属性(包括样式)
* 获取属性值
    ```JavaScript
    let value = $(selector).attr(属性名);
    ```
* 设置一个或多个(默认和自定义)属性(所有符合条件的元素)
    而prop()操作 属性值为 布尔值 的属性 例如checked等表单状态 而且只能设置元素默认的属性，无法设置自定义属性
    ```JavaScript
    $(selector).attr({
        属性名:属性值
    });
    ```
> attr() 适合操作属性值为 字符串 的属性 ，且attr()可处理自定义属性 
> prop()  适合操作属性值为 布尔值 的属性 例如checked等表单状态  且只能设置元素默认的属性，无法设置自定义属性
* 获取/设置css样式
    ```JavaScript
    $(selector).css(样式名,值);
    let value = $(selector).css("样式名","样式值");
    $(selector).css({
        样式名:"样式值"
    });
    ```
* 移除所有class
    ```JavaScript
    $(selector).removeClass("样式名")
    ```
* 获取标签内所有内容，包括子元素
    ```JavaScript
    let string = $(selector).html();
    ```
* 获取/设置input标签的值
    ```JavaScript
    let string = $(selector).val();
    $(selector).val(value);
    ```
* 给元素添加/移除样式
    ```JavaScript
    $(selector).removeClass("样式名");
    $(selector).addClass("样式名");
    ```

* 获取相对于视窗的位置，返回一个对象{top:xx , left: xx}
    ```JavaScript
    let posObj = $(selector).offset();
    ```
* 获取相对于父元素的位置，返回一个对象{top:xx , left: xx}
    ```JavaScript
    let posObj = $(selector).position();
    ```
* 设置相对于视窗的位置 
    ```JavaScript
    $(selector).offset({
                top: 200,
                left: 200
            });
    ```
* 获取/设置div和页面滚动条的坐标,页面滚动条在html上
    ```JavaScript
    $(selector).scrollTop();
    ```

* 获取元素内容的尺寸
    ```JavaScript
    $(selector).width();
    $(selector).height();
    ```
* 获取元素内部尺寸:内容+ padding
    ```JavaScript
    $(selector).innerWidth();
    $(selector).innerHeight();
    ```
* 获取元素整个尺寸:内容+ padding + border + margin(true)
    ```JavaScript
    $(selector).outerWidth(true/false);
    $(selector).outerHeight(true/false);
    ```

# jQuery 对象过滤方法

* 过滤特定条件的元素，返回jq对象
    ```JavaScript
    $(selector).filter('[属性][属性=属性值]');
    $(selector).not('[属性=属性值]');
    ```
* 过滤特定后代的元素，返回父元素
    ```JavaScript
    $(selector).has('标签名');
    ```

# jQuery 节点操作

* 在元素节点中的后端插入节点
    ```JavaScript
    $(目标标签选择器).append(插入的标签选择器/标签字符串);
    $(插入的标签选择器/标签字符串).appendTo(目标标签选择器);
    ```
* 在元素节点中的前端插入节点
    ```JavaScript
    $(目标标签选择器).prepend(插入的标签选择器/标签字符串);
    $(插入的标签选择器/标签字符串).prependTo(目标标签选择器);
    ```
* 在元素节点之 前/后 插入节点
    ```JavaScript
    $(目标标签选择器).before(插入的标签选择器/标签字符串);
    $(目标标签选择器).after(插入的标签选择器/标签字符串);
    ```

# jQuery 时间处理

* 绑定事件(多个)
    ```JavaScript
        $(selector).on("事件1 事件2 ...",callback)
    ```

* 绑定鼠标悬停事件
    ```JavaScript
        $(selector).hover(function(){
            // 移入的效果
        },function(){
            // 移出的效果
        });
    ```

* 解除元素的事件监听
    ```JavaScript
        $(selector).off(); //解除所有事件监听
        $(selector).off('事件1 事件2'); //解除所有事件监听
    ```
> 补充
* event中的鼠标位置：
    * client 相对于视口的坐标
    * page 相对于页面的坐标
    * offset 相对于当前事件元素的坐标
* 阻止事件行为
    * 阻止事件冒泡:阻止子元素触发事件并冒泡给父级元素，触发父级元素的事件
        * event.stopPropagation()
    * 阻止默认事件 
        * event.preventDefault()
> 
    如何区别 mouseover和mouseout？
    mouseover在移入子元素的时候，子元素进行了事件冒泡，触发了父元素mouseover ，同样mouseout也是如此
    mouseenter 在移入当前元素的子元素时候，子元素不会对事件进行冒泡，则不会触发第二次事件