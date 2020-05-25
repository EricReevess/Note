# jQuery

# jQuery核心
* $ 和 jQuery
    * jQuery.js向外暴露的就是 $ 和 jQuery 
* jQuery对象
    * 执行 jQuery 后 返回的对象为jQuery对象

# $
* 作为函数使用 $(参数)
    * 函数 作为参数，在DOM加载完毕后执行回调函数
    * 选择器字符串 作为参数，查找匹配标签，封装成jQuery对象并返回
    * DOM 对象 作为参数，将DOM对象封装成jQuery对象返回
    * html标签字符串 作为参数， 创建该标签并封装为jQuery对象
* 作为对象使用 $.method
    * $.each() 遍历数组
    * $.trim() 去除字符串两端空格