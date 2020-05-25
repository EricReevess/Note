# Canvas 
* Canvas 是 苹果公司为Webkit引入的绘图API
# 使用
* Canvas不兼容IE9之前的浏览器，需要增加替换内容在标签内部，
* 支持Canvas 的浏览器会忽略标签内的内容
* 不支持Canvas 的浏览器会显示标签内部的内容
# 绘图
* Canvas绘图顺序决定了其中图形的层叠关系：最新的图形出现在最顶层,出现小数会向上取整
* Canvas 本质为png格式的图片
## 绘制矩形
* canvas 原生只支持矩形绘制，其他所有图形都需要生成至少一条路径
```javascript
    let canvas = ...；
    if(canvas.getContext){
        let context = canvas.getContext("2d");
        ...
    }
```
* 绘制一个填充矩形，默认为黑色填充
```javascript
    context.fillRect(x,y,width,height)
```

* 绘制一个边框矩形，默认为黑色边框
```javascript
    context.strokeRect(x,y,width,height)
```

> 注意：此时绘制的边框矩形的边框宽度为2px，如果需要绘制1px边框宽度的矩形，则需要将偏移量均加上0.5 

* 设置填充/边框颜色
```javascript
    context.fillStyle = "#fff"
    context.strokeStyle = "#fff"
```

* 设置边框宽度,宽度从内向外增加
```JavaScript
    context.lineWidth = number;
``` 

* 设置线条连接处样式 round:圆角 bevel:斜角 miter:直角
```JavaScript
    context.lineJoin = "round";
``` 
* 设置线条末端样式   
    * butt :  方形结尾 
    * round : 圆形结尾 
    * square : 正方形结尾，正方形单边长为线条宽度
```JavaScript
    context.lineCap = "butt"
```  

## 绘制路径
* 清空路径容器，开始准备顺序存储新的路径
    ```JavaScript
    context.beginPath();
    ``` 
* 封闭路径，将开始点与结束点连接
    ```JavaScript
    context.closePath();
    ```
* 设置路径起点位置
    ```JavaScript
    context.moveTo = (x,y)
    ``` 
* 装入矩形绘制路径，左上角开始，左上角结束
    ```JavaScript
    context.rect(x, y, width, height);
    ``` 
* 设置线条
    ```JavaScript
    context.linTo(x,y)
    ```
* 设置圆弧(默认顺时针路径)
    ```JavaScript
    context.arc(x,y,半径,起始弧度,结束弧度,逆/顺时针);
    ```
* 以3个坐标+半径来确定圆弧
    * 使用3个坐标绘制出一个角(以x1,y1为角的定点)，在角的范围内绘制一个圆弧，两端分别与角的两条边相切
    ```JavaScript
    context.moveTo(x0,y0);
    context.arcTo(x1, y1, x2, y2, 半径);
    ```
* 二次贝塞尔曲线,与坐标绘制圆弧类似，但半径被固定
    ```JavaScript
    context.moveTo(x0,y0);
    context.quadraticCurveTo(x1, y1, x2, y2);
    ```
* 三次贝塞尔曲线,半径被固定
    ```JavaScript
    context.moveTo(x0,y0);
    context.quadraticCurveTo(x1, y1, x2, y2, x3, y3);
    ```
## 绘制文字
* 绘制填充文字
    ```JavaScript
    context.font = "像素大小 字体";
    context.fillText("文本", x, y);
    ```
* 绘制描边文字
    ```JavaScript
    context.font = "像素大小 字体";
    context.strokeText("文本", x, y);
    ```
* 设置文本对齐方式，以及基线位置
    ```JavaScript
    context.textAlign = "对齐方式" //start(默认) center end
    context.textBaseline = "基线位置" // top middle bottom(默认)
    ```
* 获取某个文字的相关信息
```javascript
let textInfoObj = ctx.measureText("某个已绘制的字符串"));
```
* 绘制文字阴影
```javascript
ctx.shadowColor = '#c8d6e5';
ctx.shadowOffsetX = 3;
ctx.shadowOffsetY = 3;
```

## 渲染路径
* 使用填充渲染
    ```JavaScript
    context.fill(); 
    ``` 
* 使用描边渲染
    ```JavaScript
    context.stroke();
    ``` 

### 绘制步骤
* 创建路径起始点
    ```JavaScript
    context.beginPath();
    context.moveTo = (x,y)
    ``` 
* 使用画图命令画出点，这些点会相连形成路径
    ```JavaScript
    context.lineTo = (x,y)
    ``` 
* 将路径封闭 ，将最后一个点设置到moveTo的位置或者使用closePath自动封闭区域，或者使用fill填充渲染，会自动闭合区域
    ```JavaScript
    context.closePath();
    ``` 

* 渲染图形
    ```JavaScript
    context.fill(); 
    context.stroke();
    ``` 

## 存储、恢复 路径(出入样式栈)
* 将 路径容器 中的 样式容器 内所有样式压入栈中
    ```JavaScript
    contex.fillStyle = "xxx";
    ...
    context.save();
    ```
* 将样式栈中内容覆盖到 路径容器 中的 样式容器 中
    ```JavaScript
    context.restore();
    ```
* 使用栈的绘图流程
    1. 使用save()将 初始化 样式存入栈中
    2. 设置当前容器绘图的样式，并使用beginPath清空路径容器，重新开始绘制路径
    3. 绘图完成，使用restore()恢复初始化样式
    > 相当于save() 与 restore()之间会形成样式的作用域，必须成对出现

## Canvas Transform(变换)(相对于上一个变换)
* 移动Canvas的原点到另一个位置, 会改变原点位置
```JavaScript
    context.translate(x1, y1);
```
* 以当前translate(x,y)变换后的原点进行旋转，且不是moveTo定义的原点进行选择
```JavaScript
    context.translate(x0, y0);
    context.rotate(弧度);   
```
* 先旋转(rotate) 再位移 (translate) (不推荐，会导致坐标系旋转)
    1. 以画布右上角原点进行旋转整个坐标系
    2. 然后再按照旋转后的坐标系进行位移
* 先位移 (translate) 再 旋转(rotate)
    1. 将 旋转点 移动到 位移 (translate) 定义的位置
    2. 进行旋转，但整体坐标系不变

* 以位移(translate) 定义的位置为原点进行缩放，或者先缩放再位移
```JavaScript
    context.translate(x0, y0);
    context.rotate(弧度);
```
* 缩放 画布像素(scale),默认以画布左上角原点进行缩放在绘制矩形之后使用缩放，矩形不会因此变化
    ```JavaScript
    context.scale(i, j);
    ```

## 清除图像
* 清除矩形区域
```JavaScript
context.clearRect(x,y,width,height)
```

## Canvas 引入图片
* 需要等待图片加载完成后
```JavaScript
let imgObj = new Image();
imgObj.src="图片路径";
imgObj.onload = function(){
    context.drawImage(imgObj, x , y ,width,height)
}

```
##  使用Canvas背景
将图片作为画笔的填充内容
```JavaScript
let pattern = context.createPattern(imgObj,"(no-)repeat(-x/y)")；
context.fillStyle = pattern
```

## Canvas渐变
### 线性渐变
* 从x1 y1 渐变到 x2 y2  ，返回一个对象，给对象增加颜色：
    * 位置的范围从 0 到 1.0 ，为颜色在两点之间的相对位置
```JavaScript
let linear = context.createLinearGradient(x1, y1, x2, y2);
linear.addColorStop(位置, 颜色);
...
```

### 径向渐变
* 从以 x1,y1为圆心 半径为 r1d 的圆  ，到 以 x2 y2 为圆心 半径为r2的圆之间的圆环为渐变的区域
``` JavaScript
let radial = context.createRadialGradient(x1, y1,r1, x2, y2, r2);
radial.addColorStop(位置, 颜色);
...
```
## 像素操作
* 获取某个点开始的指定宽度的像素区域数据对象
    ```JavaScript
    let imgDataObj = context.getImageData(x, y, width, heigt)
    ```

* 该返回对象包含
    * 所在区域的宽度和高度
    * 一个data数组(标识出实际块的物理像素点个数)
        * 列出每10000个像素点的实际颜色的RGBA

* 创建一个 像素数据对象  , 默认为透明(内部data数组全0)
    ```JavaScript
    context.createImageData(width,height);
    ```

* 获取到某个点的像素信息, x 和y 为 相对于当前ImageData对象原点 所在的位置， width为ImageData对象的宽度
    ```JavaScript
    let pixleObj = imgDataObj.data[(y*width + x)]
    ```
* 通过像素操作改变一块像素区域的透明度：
    ```javascript
    let imgDataObj = context.getImageData(x, y, width, heigt)
    for(var i = 0; i< imgDataObj.data.length; i++){
        imgDataObj.data[i*4 + 3] = 100 ;
    }
    context.putImageData(imgDataObj , x1 , y1)
    ```

## 全局透明度
```JavaScript
context.globalAlpha = [0-1] 小数
```

## 覆盖合成
* 当多个图形层叠在一起的时候，使用.globalCompositeOperation = "value"处理重叠区域
```JavaScript
context.globalCompositeOperation = "value";
```
* 处理方式
    * source-over(默认) 最新绘制的图形在最上层

    * source-in 只保留最新绘制的图形 与 前一个绘制的图形重叠的部分，最新绘制图形在上层
    * source-out 只保留最新绘制的图形  与 前一个绘制的图形重叠 之外的部分，最新绘制图形在上层
    * source-atop 除去 最新绘制的图形 在 前一个绘制的图形 之外的部分，最新绘制的图形在上

    * destination-over 前一个绘制的图形在最上层
    * destination-in 只保留 前一个绘制的图形与 最新绘制的 图形重叠的部分，前一个绘制绘制的图形在上
    * destination-out 只保留 前一个绘制的图形 与 最新绘制的图形 重叠 之外的部分，前一个绘制绘制的图形在上
    * destination-atop 除去 前一个绘制的图形 在 最新绘制的图形 之外的部分 , 前一个绘制绘制的图形在上

## 导出图形(Canvas元素)
* 将当前cavans画布图像导出为url
```javascript
let url = drawing.toDataURL();
```
* 检测当前鼠标指针处像素是否在 当前容器 绘制的图形上
```javascript
if(context.isPointInPath(x,y)){
    //do something...
}
```