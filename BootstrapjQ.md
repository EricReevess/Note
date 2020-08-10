## 使用 Bootstrap 网格并排放置元素

Bootstrap 具有一套基于 12 列的响应式栅格系统————可以轻松实现将多个元素放入一行并指定它们的相对宽度。 Bootstrap 的大部分 class 属性都可以应用在 `div`元素上。

Bootstrap 的列宽度属性取决于用户的屏幕宽度。 比如，手机有着窄屏幕而笔记本电脑有者更大的屏幕.

就拿 Bootstrap 的 `col-md-*`class 来说。 在这里， `md`表示 medium （中等的）， 而 `*`是一个数字，说明了这个元素占有多少个列宽度。这个例子就是指定了中等大小屏幕（例如笔记本电脑）下元素所占的列宽度。

所有文本输入类的元素如 `<input>`，`<textarea>`和 `<select>`只要设置 `.form-control`class 就会占满100%的宽度

## 使用 jQuery 配合 class 选择器选择元素

我们如何使所有的`button`标签有弹性的动画效果？我们用`$("button")`选取所有的`button`标签，并用`.addClass("animated bounce");`给其添加一些 CSS 属性。

jQuery 的`.addClass()`方法用来给标签添加类。

## 使用 jQuery 禁用元素

你还能用 jQuery 改变 HTML 标签的非 CSS 属性,例如:能禁用按钮。

当你禁用按钮时，它将变成灰色并无法点击。

jQuery 有一个`.prop()`方法，你可以用其调整标签的属性。

下面的代码效果是禁用所有的按钮：

```js
$("button").prop("disabled", true);
```

## Sass：用 Mixins 创建可重用 CSS

在 Sass 中，`mixin`是一组 CSS 声明，可以在整个样式表中重复使用。

CSS 的新功能需要一段时间适配后，所有浏览器后才能完全使用。随着浏览器的不断升级，使用这些 CSS 规则时可能需要添加浏览器前缀。比如 "box-shadow":

```scss
div {
 -webkit-box-shadow: 0px 0px 4px #fff;
 -moz-box-shadow: 0px 0px 4px #fff;
 -ms-box-shadow: 0px 0px 4px #fff;
 box-shadow: 0px 0px 4px #fff;
}
```

对于所有具有`box-shadow`属性的元素重写此规则，或者更改每个值以测试不同的效果，需要花费大量的精力。

`Mixins`就像 CSS 的函数。以下是一个例子：

```scss
@mixin box-shadow($x, $y, $blur, $c){
 -webkit-box-shadow: $x, $y, $blur, $c;
 -moz-box-shadow: $x, $y, $blur, $c;
 -ms-box-shadow: $x, $y, $blur, $c;
 box-shadow: $x, $y, $blur, $c;
}
```

定义以`@mixin`开头，后跟自定义名称。参数（`$x`，`$y`，`$blur`，以及上例中的`$c`是可选的。

现在，只要在需要的地方使用`@include`调用上面定义的`mixin`，就可以自动添加好所有的浏览器前缀：`mixin`使用`@include`指令调用：

```scss
div {
 @include box-shadow(0px, 0px, 4px, #fff);
}
```

## Sass：使用 @if 和 @else 为你的样式添加逻辑

Sass 中的`@if`指令对于测试特定情况非常有用--它的工作方式与 JavaScript中的`if`语句类似。

```scss
@mixin make-bold($bool) {
 @if $bool == true {
  font-weight: bold;
 }
}
```

类似 JavaScript，你可以在 Sass 中使用`@else if`和`@else`添加更多条件：

```scss
@mixin text-effect($val) {
 @if $val == danger {
  color: red;
 }
 @else if $val == alert {
  color: yellow;
 }
 @else if $val == success {
  color: green;
 }
 @else {
  color: black;
 }
}
```

## Sass：使用 @for 创建一个 Sass 循环

你可以在 Sass 中使用`@for`循环，它的表现类似与 JavaScript 中的`for`循环。

`@for`以两种方式使用："start through end" 或 "start to end"。主要区别在于“开始结束” *排除* 结束号码，而“开始结束” *包括* 结束号码。

这是一个开始 **到** 结束示例：

> @for $i from 1 through 12 {
>  .col-#{$i} { width: 100%/12 * $i; }
> }

`#{$i}`部分是将变量（`i`）与文本组合成字符串的语法。当 Sass 文件转换为 CSS 时，它看起来像这样：

> .col-1 {
>  width: 8.33333%;
> }
>
> .col-2 {
>  width: 16.66667%;
> }
>
> ......
>
> .col-12 {
>  width: 100%;
> }

这是创建网格布局的有效方法。现在，你有 12 个可用作 CSS classes 的列宽选项。

## Sass：使用 @while 当条件满足时样式生效

Sass 中的`@while`指令与 JavaScript 中的`while`类似。只要满足条件，它就会一直创建 CSS 代码。

`@for`挑战提供了一个创建简单网格系统的示例。这也适用于`@while`。

> $x: 1;
> @while $x< 13 {
>  .col-#{$x} { width: 100%/12 * $x;}
>  $x: $x + 1;
> }

首先，定义变量`$x`并将其设置为 1。接下来，使用`@while`指令，当`$x`小于 13 时创建网格系统 。

在设置`width`的 CSS 规则后，`$x`增加 1 以避免无限循环。
