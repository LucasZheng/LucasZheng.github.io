title: Web Animation API（四） 
description: 让我们来谈一谈未来的Web Animation API
date: 2015-11-26 22:52:13
categories:
- javascript

tags: [Animation]
---
我们继续讨论WAAPI中的多个动画效果，在polyfill中提供了`group`和`sequence`两个功能。下面主要讨论这两个功能的实现。<!-- more -->
#### KeyframeEffects
`KeyframeEffects`接受三个参数：实现动画的元素，一个数组格式的动画帧效果和时间相关参数。这些参数在我们之前使用`element.animate()`时已经见过了。我们新定义的这个`KeyframeEffects`并不好马上执行，只是创建一个引用。
```javascript
var elem = document.getElementById('toAnimate');
var timings = {
  duration: 1000,
  fill: 'both'
}
var keyframes = [
  { opacity: 1 }.
  { opacity: 0 }
];

var effect = new KeyframeEffect(elem, keyframes, timings);
```
#### GroupEffects
尽管在浏览器中没有得到支持，但在[polyfill](https://github.com/web-animations/web-animations-js)中提供一个`group`的功能，能够同时播放多个动画。`GroupEffects`这个功能在新的版本中会实现，它可以让多个`KeyframeEffects`同步执行。

{% jsfiddle 1et0faek %}

js:
```javascript
var elem = document.getElementById('toAnimate');
var elem2 = document.getElementById('toAnimate2');
var timings = {
  duration: 1000
}
var keyframes = [
  { opacity: 1 }.
  { opacity: 0 }
];

var kEffects = [
  new KeyframeEffect(elem, keyframes, timings),
  new KeyframeEffect(elem2, keyframes, timings)
];
var group = new GroupEffect(kEffects);
document.timeline.play(group);
```
#### SequenceEffects
和`GroupEffect`相似，`SequenceEffect`可以让动画顺序执行，当然你也可以同时应用这两个功能来实现很酷的动画效果。

{% jsfiddle gs1tdz16 %}

