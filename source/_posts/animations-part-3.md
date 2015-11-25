title: Web Animation API（三） 
description: 让我们来谈一谈未来的Web Animation API
date: 2015-11-26 22:52:13
categories:
- javascript

tags: [Animation]
---
上篇文章中我们谈论了WAAPI的各种状态，下面我们看看如何操作多个动画。<!-- more -->
#### 同一个元素上多个动画效果

{% jsfiddle 7yrLon6t %}

在上面的例子中，每个矩形方块都应用了三种动画效果，你可以在每个元素上多次调用`animate()`，这跟css中允许多个动画效果一样：

```css
#toAnimate {
  animation: pulse 1s, activate 3000ms, have-fun-with-it 2.5s;
}
@keyframes pulse { /* ... */ }
@keyframes activate { /* ... */ }
@keyframes have-fun-with-it { /* ... */ }
```
用WAAPI实现如下：
```javascript
var animated = document.getElementById('toAnimate');
var pulseKeyframes, //defined the keyframes here.
    activateKeyframes,
    haveFunKeyframes;
var pulse = animated.animate(pulseKeyframes, 1000); //the second
parameter as a number is a valid shorthand for duration
var activate = animated.animate(activateKeyframes, 3000);
var haveFunWithIt = animated.animate(haveFunKeyframes, 2500);
```
在上面的例子中，可以改变每一个动画对象的状态等。

