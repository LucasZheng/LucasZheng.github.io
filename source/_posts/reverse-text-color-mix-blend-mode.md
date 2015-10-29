title: 根据背景色自动改变文字的颜色
description: 用css来根据不同的背景色调整对应的文字颜色
date: 2015-10-29 22:52:13
categories:
- css

tags: [css3]
---
设计人员往往会根据背景色来决定文字的颜色，如果背景为黑色，文字就会用相应的反色调。如果背景比较亮，则文字常用黑色。因为文字和背景色之间的颜色对比能够让阅读更加容易。<!-- more -->
在Sass中我们如何根据给定的背景色来决定文字的颜色呢？
假如我们有如下的HTML:
```html
<p class="notification notification-confirm">Confirmation</p>
<p class="notification notification-warning">Warning</p>
<p class="notification notification-alert">Alert</p>
```

我们有不同背景颜色的提醒条，我们可以用一个function来计算出对应合适的文字颜色：
```scss
@function set-notification-text-color($color) {
  @if (lightness($color) > 50) {
    @return #000000; // Lighter backgorund, return dark color
  } @else {
    @return #ffffff; // Darker background, return light color
  }
}
```

`lightness()`是Sass中定义的一个function，它返回当前颜色的亮度（0-100），0是最暗的，100代表最亮。
下面看下完整的css代码：
```scss
$notification-confirm: hsla(101, 72%, 37%, 1);  // Green
$notification-warning: #ffc53a;                 // Yellow
$notification-alert: rgb(172, 34, 34);          // Red

%notification {
  border-radius: 10px;
  display: block;
  font-size: 1.5em;
  font-family: sans-serif;
  padding: 1em 2em;
  margin: 1em auto;
  width: 30%;
  text-align: center;
}

.notification {
  @extend %notification;
}
.notification-confirm {
  background: $notification-confirm;
  color: set-notification-text-color($notification-confirm);
}
.notification-warning {
  background: $notification-warning;
  color: set-notification-text-color($notification-warning);
}
.notification-alert {
  background: $notification-alert;
  color: set-notification-text-color($notification-alert);
}
```

如果我们改变背景色，对应的文字颜色也会相应的进行更新。

在css3中，`mix-blend-mode`属性也可以根据背景色自动的改变文字的颜色，但这个属性目前还没有得到浏览器的普遍支持，下面这个例子需要打开chrome的experimental开关：
{% jsfiddle ad716vz2 html,css,result %}
