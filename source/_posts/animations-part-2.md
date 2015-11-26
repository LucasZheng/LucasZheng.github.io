title: Web Animation API（二） 
description: 让我们来谈一谈未来的Web Animation API
date: 2015-11-25 23:52:13
categories:
- javascript

tags: [Animation]
---
在上篇文章中我们已经知道如何创建一个基本的动画，下面我们将会探讨下这个api中的状态控制，回调和时间线<!-- more -->
#### 状态控制
当你调用`element.animate()`时，就会返回一个`AnimationPlayer`的对象，并且开始执行动画。可以通过检测只读属性`playState`来查看当前的状态，总共有5个状态。我们也可以通过调用其他四个方法来修改当前的动画状态：
```javascript
var player = element.animate(/* ... */);
console.log(player.playState); //"running"

player.pause(); //"paused"
player.play();  //"running"
player.cancel(); //"idle"... jump to original state
player.finish(); //"finished"...jump to end state
```
{% jsfiddle s7dzb5c2 %}

在上面的例子中，有一个`2x`的按钮，点击它就会改变当前的动画频率，这是通过读写`playbackRate`属性来实现的。
```javascript
var player = element.animate(/* ... */);
console.log(player.playbackRate); //1

player.playbackRate = 2; //double speed, can also be decimal to slow it
down.
```
在css中当动画结束时会触发一个事件，同样，在WAAPI中，当动画结束或直接调用`finish()`函数时也会有一个`onfinish`函数来进行处理。当然在一个无限循环的动画中没有结束事件，它的`playbackRate`也不可能为0。下面这个例子使用`onfinish`来展示动画结束时的状态：

{% jsfiddle t7vdxane %}

每个AnimationPlayer有两个关于时间的读写属性：`currentTime`和`startTime`，现在我们先看看第一个属性。
`currentTime`返回当前动画运行的时间。最大值是：`delay + (duration * iterations)`， 无限循环的动画没有最大值。
```javascript
var player = element.animate([
  {opacity: 1}, {opacity: 0}
], {
  duration: 1000,
  delay: 500,
  iterations: 3
});

player.onfinish = function() {
  console.log(player.currentTime); // 3500
};
```
由于`currentTime`是可写的，因此我们可以通过指定某个点来查看动画效果，它也同样可以让我们来保持两个动画的同步，如下面这个例子：

{% jsfiddle zz09fpwn %}

同样你也可以使用`reverse()`来产生相反的动画效果，但当相反的动画结束时，`currentTime`将会是0。

{% jsfiddle 1gup2944 %}
