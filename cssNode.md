 # css层叠样式表
Cascading Style Sheets

*  规则
*  选择器{声明块}
*  css属性+ css属性值 组成的键值对

## 读取选择器的顺序：
*  从右往左
## 定位
* relative：相对于原来位置移动，元素设置此属性之后仍然处在文档流中，不影响其他元素的布局
    元素相对于原来位置偏移，元素的宽高都没变
*  absolute:  元素会脱离文档流，如果设置偏移量，会影响其他元素的位置定位。
                 元素在没有定义宽度的情况下，宽度由元素里面的内容决定，效果和用float方法一样。
                 若不设置top之类属性，则相对于  离自己最近的元素 定位
    1. 在父元素没有设置相对定位或绝对定位的情况下，元素相对于根元素定位（即html元素）
    2. 父元素设置了相对定位或绝对定位，元素会相对于离自己最近的设置了相对或绝对定位的 **父元素** 进行定位（或者说离自己最近的不是static的父元素进行定位，因为元素默认是static。

* 定位宽度限制问题：
    * 当父级元素 或者 外层元素 拥有定位时，在子元素不设置宽度的情况下
    * 子元素使用相对定位(relative)：子元素的宽度受父级元素的限制
    * 子元素使用绝对定位(absolute)：子元素的宽度由子元素的内容撑开，不受限制
## 选择器：
* id    "div#box1"
* 元素 p    
* 类 class   "div.box1"
* 通配符 *
* 后代 div span
* 分组 span,p 

### 基本选择器扩展
#### 直接后代选择器 >
* #div1 > .inner {...}   
    * 只能选择自己后1代的元素
#### 直接相邻兄弟选择器 +
* #div1 .first + .second {...}  
    * 匹配一个 在.first元素 之后的 且class为.second的兄弟元素
#### 通用兄弟选择器 ~
* #div1  .first ~ .second {...} 
    * 匹配所有 在.first元素 之后 且class为.second的元素

### 属性选择器
#### 存在值属性选择器:
*  [name] {...}
    * 匹配所有 带有name属性 的元素
* [name="2333"] {...}
    * 匹配所有 name属性值为2333 的元素
* [name~="2333"]{...}
    *  匹配所有 以空格分隔的name属性值 列表中 含有 2333 的元素    <div name="2333 2444"></div>
*  .div1[name] {...} 
    * 匹配所有 class为.div1 且带有name属性的元素
*  .div1[name="2333"] {...}
    * 匹配所有 class为.div1 且name属性值为233的元素
#### 子串值属性选择器：
* [name|=value]
    * 匹配所有 name属性值 以 value- 开头 的元素
* [name^=value]
    * 匹配所有 name属性值 以 value 开头 的元素
* [name$=value]
    * 匹配所有 name属性值 以 value 结尾 的元素
* [name*=value]
    * 匹配所有 name属性值 包含 value 的元素

### 伪类、伪元素选择器：(LVHA lovehate)

#### 链接伪类选择器(只适用于a标签):
* :link       匹配链接元素未点击的装状态

* (href中填写javasctipt:;不会触发下面2个伪类)
* :visited  匹配链接元素被点击后的状态，只有3个属性能被:visited应用
    * color、background、border-color
* :target 代表一个以id为值的URI 作为目标锚点的状态

#### 动态伪类(适用于所有元素):
* :active 匹配所有元素被点住的状态
* :hover 匹配所有元素被鼠标移入的状态
  
> 问：为什么一定要遵循 lvha的顺序来书写代码?
答：因为:link  和 :visited 能将标签的所有状态全部涵盖，即使触发特殊情况，如果将:link 和 :visited 置于 :active 和 :hover 之后，将直接改变a标签的状态

#### 表单伪类选择器
* :enable     匹配可编辑表单的元素
* :disable    匹配被禁用的表单元素
* :checked 匹配被选中的复选框元素
* :focus      匹配获得焦点的表单元素
  
####  结构伪类选择器
构伪类的index 从 1 开始计数

* .div element:nth-child(n)    
        在.div中匹配第n个element类型的元素,若此位置为非element元素，则不会被选中
* element:first-child   选中元素的第一个子元素 
* element:nth-last-child(n)     与nth-child(n)相反，倒序匹配
* .div element:only-child    匹配element 当.div下只有一个元素,且这个元素是element类型的时候


* .div element:nth-of-type(n)   在.div中匹配第n个element类型的元素
* element:first-of-type()   选中元素的第一个element元素 
* element:nth-last-of-type(n)     与nth-type(n)相反，倒序匹配
* .div element:only-of-type    匹配element 当.div下只有一个element元素的时候


>    注意：  
.div element:nth-of-type(n) 以元素为中心
举个例子，给div span p h1 等多个的不同的元素都加上相同的class="exp"
.div .exp:nth-of-type(1) 不会匹配 第一个class为exp的元素，而是匹配所有class为exp的元素
但是多个相同且符合规则的元素 只会 选中 第一个

* element:not(选择器)   排除参数中选择器选中的element元素，然后匹配 其余的element元素 

> 举个例子   div > a:not(:last-of-type){...} 匹配div中 除了最后一个a标签以外的所有a标签

​    

* element:empty{...}    匹配内容(innerHtml)为真空(无任何字符)的element类型的元素  

#### 伪元素选择器
利用css代码，在元素前后 创建一个 不存在 于DOM树中的元素,内容使用content: 指定
一个元素只能有前后 2个 伪元素

* element::after{width:"50px",height:"50px" content:""}    
    在element类元素之后创建一个名为element::after的 **伪元素**
* element::before{...}
    在element类元素之前创建一个伪元素
* element::first-letter{...}    匹配element元素 内文本的第一个字符 
* element::first-line{...}    匹配element元素 内文本的第一行
* element::selection{...}    匹配element元素 内文本被选中时的状态

#### CSS声明的优先级
* 选择器的特殊性
* 选择器的特殊性由选择器本身组件确定，特殊值用4个部分表述 0,0,0,0
* 1,0,0,0 为最高特殊性 ，若选择器产生冲突，以最高特殊性选择器来渲染,特殊性可叠加，但不产生进位 例如 0,100,0,0
* 对于选择器中给定ID属性， 为 0,1,0,0
* 对于选择器中给定class类属性、属性选择、伪类、加0,0,1,0
* 对于选择器中给定的各个元素以及 元素:伪类  ， 加 0,0,0,1
* 通配符选择器 为 0,0,0,0
* 结合符 “,” 不会影响特殊性
* 元素的行间样式 特殊性 均为最高 1,0,0,0
* 继承无特殊性, 能被通配符选择器改变
* 重要声明
    * !important 加入某个css声明的结束分号之前 ，放入重要声明容器中，使得此声明被重新考虑，在容器内部比较冲突的重要声明， 选择特殊性高的进行 渲染。

## 自定义字体
```css
@font-face{
    font:family:...
    src:url(...)
}
```
将改变浏览器的默认字体，消除对本地字体的依赖

* 在css伪类选择器中使用Font-Awesome字体图标(提前引入外部css)
```css
element::before {
        font-family: FontAwesome;
        content: '\f006';
    }
```
## 文字排版
* 强制文字在一行显示：white-space:nowrap 
* 溢出显示省略号 ...  : text-overflow:ellipsis;

## 盒模型样式
* 盒模型阴影 box-shadow: x y  阴影大小 样色 外/内 ，
    可叠加多个阴影


* 图片在盒模型中居中方案：
```css
    fatherBox::after{
        content:"";
        display:inline-block;
        height:100%;
        vertical-align:middle;
    }
    img{
        vertical-align:middle;
    }
```

* box-sizing 
    * border-box : 宽高不变，内容区域受padding和border的宽度影响，如果超出 指定的宽和高 会将其容器撑大
    * content-box ： 内容宽高不变，padding 和 border 在外影响容器

* 禁止浏览器出现滚动条：
```css
html,body{
    height:100%;
    overflow:hidden;
}
```
## 长度单位

* em：相对于元素 内 的字体大小来计算， 1em = 1 font-size px(浏览器默认100%比例下为16px);
    会根据字体的大小而改变
* rem：相对于根元素的字体大小来计算


## BFC

* BFC(Block formatting context), 块级别格式化上下文，一个独立的渲染区域，只有块级的元素参与，内部规定其如何布局，而且这个区域不影响外部区域,
* BFC是CSS中元素的一个隐含的属性，可以为一个元素开启BFC
    *一般使用与 解决 子元素浮动后  父级元素高度塌陷问题 ，开启BFC可让 父元素内部子元素进行浮动排列，并且撑开父级元素的高度

* BFC布局规则
    * 开启BFC的元素不会被float元素所覆盖,且可以包裹float元素
    * 开启BFC的父元素的外边距不会与子元素重叠(不会因为子元素的外边距改变父元素位置)
    * BFC就是页面上一个隔离的独立容器，容器子元素不会影响容器外部的其他元素
    * 内部的Box会在垂直方向，一个接一个排布
    * BFC的区域不会与float的元素的margin重叠
    * 内部box的垂直方向间距由margin 确定，且相邻的margin 会发生重叠
    * 计算BFC高度时，浮动box参与计算

* 如何BFC的开启
     * 根元素是默认开启BFC的

     * 开启浮动float(失去指定宽度)

     * clear:left / right / both  (开启后浏览器默认为元素添加外边距) 

     * position 为绝对定位：absolute 或 fixed

     * overflow 不为 visible时 ：auto 、hidden (常用方式)

     * display 为 inline-block , table-cell , table-caption , flex , inline-flex
       
    * 使用::after伪类(完美方案)

        * ```css
          .box::after {
              content: "";      使其最后元素内容为空
              display: block;   默认为行内元素，不独占一行，所以使其独占一行
              clear: both;        清除浮动，完成效果
          }  
          ```

    * 或者将其::after 和 ::before 生成一个类.clearfix ,同时解决：高度塌陷和外边距重叠

        * ```css
            .clearfix::after,.clearfix::before{
                content: "";
                display: table;
                clear: both;
            }
            ```
## 背景图片
* CSS3的背景图片默认是从padding-box开始渲染，且平铺加重复
* background-origin 可以改变渲染起始点
* background-clip 可以设置从某个起始点剪切背景图片

## CSS3 过渡
* transition-property: 属性名称  指定某个属性产生过渡 可用,隔开  值为all 则应用所有属性
* transition-duartion: 时间  指定当前元素过渡的时间 可用,隔开
* 过渡完成事件："transitionend"  只要有几个过渡发生，就会触发几次事件，在过渡动画完成之前，将元素display设置为none 则不会触发该事件

>   注意：
* transition 在元素首次渲染结束前是不会触发动画的（包括使用JS立即渲染,会等待js操作dom完成后才会触发动画，可用window.onload 来进行异步渲染）
* transition 在切换型变换中，如果两个切换的类型不相同 或位置不同，则不会触发函数：
  
    > 例如：打开开关时触发 rotate 以及 scale  关闭开开关时候只触发 rotate ，则不会触发过渡动画
## 2D 变形 
* transform 修改块级元素的坐标空间
    * skew( deg )斜切
## 3D 变换
### 景深
* 原理：控制灭点的位置，景深越大，灭点越远，元素变形越小
    > perspective ,作用于要产生3D效果的父级元素上
    * perspective-origin 设置基点位置，默认为两个50%，即中心点
    
    * transform: rotate3d(0,1,0,30deg) : 按照x/y/z轴，旋转deg度
    * transform: translate3d(x,y,z) 按照x/y/z轴进行移动 其中z轴为类似放大/缩小效果
    * transform: perspective(xx px) [rotate3d(0,1,0,30deg) ...] 在每个元素中单独设置        perspective ，后跟变换内容，并且perspective 必须放置最前面
### 景深叠加 
* 尽量避免景深叠加
### 层次:  
* 父级元素开启后即可产生真正的3D平面，子元素处于父元素的3D空间内,不可继承
  
    > transform-style: preserve-3d 
## 动画
* 属性 
    * animation : 动画名  动画时长  延迟时间  动画初始/结束状态 动画当前状态 动画执行次数  动画执行方向   动画变换方式

    * animation-name 动画名
    * animation-duration 动画时长
    * animation--timing-function 动画效果 默认ease 
        > steps(步数,start/end)  步数:将一个关键帧周期分成若干部分 
         start 的执行点为第一步执行结束的位置，到倒数第一步后停止(会将结尾执行完毕)
         end 的执行点则为第一步尚未开始的位置，到倒数第一步前停止(不会执行最后一步)，默认为end
    * animation-direction 动画方向，若值为alternate，则来回播放动画，若值为reverse，则反转关键帧次序，以及动画效果
    * animation-delay 动画延迟时间
    * animation-iteration-count 动画执行次数 从一个方向重复执行关键帧
    * animation-fill-mode: 参照动画第一和最后的关键帧，确定在动画执行前后的状态 值为both 则 动画开始和结束时元素的状态与动画的第一和最后一帧保持一致
    * animation-play-state: 控制动画状态，默认为running

## flex布局(弹性盒模型)
> display:flex (新)  display:-webkit-box(老) 应用在容器元素上的布局设置，使得内部元素呈 类似浮动排列 ，但所有子元素不会使用浮动，而且都处于一行 ，
 父元素宽度小于子元素宽度之和时，会重新给子元素的宽度赋予均分父元素的宽度 
 ### flex容器
 #### 容器的布局方向
* -webkit-box-orient(老):
    * horizontal 设置主轴为水平方向 
    * vertical 设置主轴为垂直方向 
* flex-direction(新):
    * row 设置主轴为行
    * column 设置主轴为列
#### 容器内元素的的排列方向
*  -webkit-box-direction 
    >改变容器内部排列主轴的的正方向，不会改变整体子元素当前的位置
    * normal 正常顺序排列
    * reverse   反方向顺序排列 

* flex-direction(新): 
    > 可能会改变整体子元素当前的位置，如果整体子元素宽度小于父元素宽度(拥有富裕空间时)，反方向排列会从父级的对边开始
    *  row/row-reverse 设置行中排列方向
    *  column/column-reverse 设置列中排列方向
#### 富裕空间管理

> 管理主轴(布局方向规定的轴) 的富裕空间

* -webkit-box-pack (老版)

    * start 将富裕空间放置在所有元素右边/下边
    * end  将富裕空间放置在元素左边/上边
    * center 将富裕空间平分到元素两侧
    * justify 将富裕空间平分到子元素之间

* justify-content (新版)

    * flex-start  将富裕空间放置在主轴的正方向，且所有在元素之后
    * flex-end  将富裕空间放置在主轴的正方向，且在所有元素之前
    * center     将富裕空间平分到元素两侧
    * space-between 将富裕空间平分到子元素之间
    * space-around (新增) 将富裕空间平分到子元素的两侧，且元素之间间距不会重叠
    * space-evenly  将富裕空间平分到包括的边框和子元素之间

> 管理侧轴(垂直于主轴)的富裕空间,侧轴的水平和垂直状态只有一个方向，

* -webkit-box-align (老版)

    > 主轴的正反方向不影响侧轴的富裕空间

    * start 将富裕空间放置在所有元素的下边/右边
    * end 将富裕空间放置在所有元素的上边/左边
    * center 将富裕空间放置在所有元素两侧

* align-items(新版)

    * flex-start  将富裕空间放置在所有元素的下边/右边
    * flex-end  将富裕空间放置在所有元素的上边/左边
    * center     将富裕空间放置在所有元素两侧
    * baseline  通过行高确定的基线来确定富裕空间的分配,将所有子元素中的文本对齐到一行(可能会错开对齐)
    * stretch 等高布局，将 没有设置高度 的元素拉伸到整个富裕空间中

* align-self 
    > 设置于每个子元素上，在不换行 (nowrap) 的情况下，管理每个子元素的附轴富裕空间  
    * flex-end 当前元素的富裕空间放置在附轴正方向，元素之前
    * flex-start 当前元素的富裕空间放置在附轴正方向，元素之后
    * center 富裕空间在当前元素两侧
    * auto 


#### 弹性空间管理

* -webkit-box-flex : 1 / 2 / 3 ...... (老版) 默认值为0

* flex-grow : 1 / 2 / 3 ...... (新版) 默认值为0

> 将主轴上的 富裕空间 按比例平均分配到每个子元素中,应用于每个子元素上，实际意义为 分配到每个元素的权重
>
> > 若富裕空间剩余200px宽度 ，子元素权重为 4,1,1,1,1 ，每一份的宽度分别为 200/(4+1+1+1+1) = 200/8 = 12.5 , 子元素在自身基础上获得的宽度分别为为 50 12.5 12.5 12.5 12.5 


* flex-shrink : 0(content) / 1(默认) / 2  ...
> flex-shrink 设置每个元素的收缩权重，在父级元素的宽度/高度 小于了所有子元素的宽度/高度 时候 触发，权重越高 收缩程度越大 默认值为1
> 收缩原则：  
父元素被定义的宽度200px ， 5个子元素 都被定义 为 50px ，则 溢出的宽度为 ： 5*50 - 200 = 50 px  
5个子元素的权重分别为 1:1:1:1:4 ，
所以权重为1的子元素收缩宽度为： 1/8  * 50 = 6.25 px
权重为4的子元素收缩宽度为          4/8  * 50 = 25 px

* flex-basis 
    * xx px / xx %   描述一个宽度
    * 0 / content  基于元素的内容撑开元素的宽/高
    * auto 默认值

> flex-basis 指定 子元素在主轴方向上的初始大小,flex-basis 拥有比width/height更高的优先级，  
但优先级比max/min-width/height 要低


* flex: 拉伸权重 收缩权重  初始大小 (默认值为0)
>  flex 集中简写属性

#### 换行(新版本)

* flex-wrap 
  

控制容器为多行或者多列，且能控制侧轴的方向将多行多列向 
    
    * nowrap (默认) 不进行换行，附轴的方向向下
    * wrap 进行换行，且每一行直接平分附轴的富裕空间
* wrap-reverse 进行换行，附轴方向向上，平分空间给行
  
* align-content  
  

控制分配容器附轴的富裕空间 (必须预先开启flex-wrap:wrap 换行才会生效)
    
    * flex-start  将富裕空间放置在 附轴 的正方向，且所有在元素之后
    * flex-end  将富裕空间放置在 附轴 的正方向，且在所有元素之前
    * center     将 附轴 富裕空间平分到元素两侧
    * space-between 将 附轴 富裕空间平分到子元素之间
    * space-around 将 附轴 富裕空间平分到子元素的两侧，且元素之间间距不会重叠
    * stretch 默认值，将没有高度的子元素拉伸到整个富裕空间

> 注意 ： 如果父级元素的宽度小于了 子元素的宽度 ，子元素的宽度会被压缩成 父元素的宽度

* flex-flow
  
    flex-flow 为flex-direction 和 flex-wrap 的缩写形式 
    
    flex-flow: 主轴位置-方向   是否换行
#### 行间排序

* order 设置与子元素上 数值越大，元素排列越靠后


flex 总结：
* 使用display:flex 开启弹性盒模型
* 空间排列操作
    * 若要 改变排列主轴  以及 主轴的方向，使用flex-direction
    * 若需要 平均将元素排列在一行/一列，使用 justify-content:space-between等
    * 若需要将 富裕空间平分到元素整体两侧，使用 align-items:center
    * 若要对其中一个子元素的富裕空间操作，使用 align-self
    * 如果要进行换行 以及 改变换行轴的方向，使用 flex-wrap:wrap-reserve

    * 如果要操作富裕空间在 行与行 之间的效果(平分或者在一侧)，使用align-content，需要开启换行(废话)

    * 如果要使用一句话同时设置主轴以及是否换行，使用 flex-flow

    * 如果要一步设置 弹性盒模型内项目 为 等分布局  ，则使用 flex:1  ,
        * flex:1 将产生3个效果：  
          flex-grow:1;       项目的增长权重全设置为1，所有项目平分富裕空间
          flex-shrink:1;      将项目的缩小权重设置为1，所有项目都按照0的基本宽/高度进行缩小
          flex-basis:0%;    将项目的基本宽/高度设置为 0 ，实际宽度 = 0 + flex-grow设置的权重而获得的宽度   

* 空间详细操作
    * 若要对某个子元素富裕空间的分配做一定调整，使用flex-grow:权重
    * 若要对某个子元素的收缩程度进行调整，使用flex-shrink:权重
    * 若要设置一个元素的初始大小，使用flex-basis:宽度px ，content
    * 若要设置某个子元素的到指定顺序，使用 order  

### 响应式布局方案
#### CSS3 媒体查询(核心)

* 媒体查询选择器
```css
@media 媒介类型1 and  (min/max-width : xx px  ) and (......) and ......{
    选择器{
        样式...
    }
    .
    .
}
```
* 关键字
    * and   连接多个媒体属性 和 媒体类型
    * only  浏览器兼容性有关：方式老版浏览器不支持媒体属性查询，而直接应用到给定样式
        * 老版本只支持 媒体类型 ，不支持带 媒体属性 的查询
        * 使用only可以使老版浏览器不会直接匹配到样式
    * ,     或，连接多个媒体属性 和 媒体类型  
    * not   条件取反 

* 媒体类型
    * all :  所有媒体(默认)
    * screen : 彩色屏幕
    * print : 打印预览的时候应用
    * projection  :  手持设备
    * tv 电视
    * braille : 盲文触觉设备
    * tty 不适用于像素的设备

* 媒体属性
    * min-width : xx px  当浏览器窗口 >= xx px 的时候触发规定样式
                                当浏览器窗口 最小 为xx px的时候，触发样式
    * max-width : xx px 当浏览器窗口 <=  xx px 的时候触发规定样式  
                                当浏览器窗口 最大 为xx px时，触发样式
    
    
        >   max-----> | <------min
    * max/min-device-width : xx px : 根据当前设备 分辨率 来判断以应用样式
    * -webkit-max/min-device-pixel-ratio  PC:1 移动端: 根据机型略有不同
    * orientation : portrait/ landscape 
        * portrait : 竖屏，当宽度笔高度小的时候，触发样式
        * landscape : 横屏，当宽度笔高度大的时候，触发样式
#### CSS 像素比
 > 因为移动端屏幕像素的差异，不同尺寸的屏幕，1个像素显示的大小不相同，所以应该使用css像素比来进行自适应，每一款设备都有最佳像素比，称之为完美视口
 * 使用meta标签设置视口(viewport)大小，device-width为当前设备的宽度，将当前 初始化缩放调整为1

 ```html
 <meta name="viewport" content="width=device-width, initial-scale=1.0" >
 ```
* 单位 vw (viewport width) 视口宽度
    100vw = 一个视口的宽度 
    1vw = 1%的视口宽度
* vw适配
> 浏览器中的字体大小最小为12px ，不能设置比12px更小的字体像素，否则默认为12px，0除外

### 多列布局
* 栏目宽度 ： column-width:xx px  指定每一栏的宽度
* 栏目列数 ： column-count : 1/2/3.....  指定一共有多少列 
* 栏目间距 ： column-gap : xx px   指定栏目直接的间隔
* 栏目分割线 ： column-rule : xxpx solid color  设置栏目之间的分割线

### Less
* Less是一门CSS的预处理语言，是CSS的增强版，语法大体和CSS一致，但添加了对CSS的扩展，
浏览器无法直接指向Less代码，需要转换,Less语法允许 将CSS的声明进行嵌套
#### CSS 原生变量设置
* 声明
> --变量名: 值;
* 使用 
属性名 : var(变量名);
* 函数
    * 计算函数 ：calc(表达式)
#### Less 变量语法

```less
@变量名 : 值(像素，颜色等);
@变量名2: 类名
.box{
    width:@变量名
    background:url("@{变量名3}/1.png");

    > 子元素{
        //选择直接后代
    }
    &:hover{
        //给当前元素加上hover &代表上面的.box
    }
    &-ok{
        //相当于.box-ok
    }
}
```
* 数值直接进行运算
```less
.box{
    width : 100px + 100px;
    hight : 100px * 3;
    ...
}
```
* 导入外部css
```less
@import "other.less"
...
```

* 继承.box的属性值，并进行扩展
    ```less
    .box2 : extend(.box){
        
    }
    ```
* 将.box的样式进行引用
    ```less
    .box3{
        .box();  
    }
    ```
* 使用变量作为类名
    ```less
    .@{变量名2}{
        ...
    }
    ```
* 创建混合函数，用于在其他类中调用，多用于公共类，加上()不会被解析为css，
    ```less
    .类名(@参数1,@参数2,....){ 
        width:@参数1
        ...
    }
    .类名(@参数1: 100px,@参数2:200px, ...){  //设置默认值
        width:@参数1
        ...
    }
    .div{
        .类名(100px,200px);  //按照顺序顺序
        .类名(@参数1:100px, @参数2:200px, ...)  //按照键值对传递
    }
    ```
* 变量重名时，会优先使用最近的变量
* 函数
    * average (参数1, 参数2,......) 将参数综合后得到一个平均值 ，参数可以使 颜色 或者数值类型
    * darken(颜色, 百分比) , 设置颜色加深的程度