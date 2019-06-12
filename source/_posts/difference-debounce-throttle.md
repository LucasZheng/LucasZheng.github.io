title: debounce和throttle的区别
description: 理解debounce和throttle之间的差异
date: 2015-10-11 22:52:13
categories:
- javascript

tags: [performance]
---
debounce和throttle常被用来处理DOM相关事件，通过限制事件的执行频率来提升性能。但它们之间也有使用场景的差别。<!-- more -->
debounce和throttle是两种相似（但不同！）的技术，用于控制允许函数随时间执行的次数。

#### debounce
debounce会将一系列连续发生的事件合并成一个事件执行。比如，想象一下，你在电梯里，门开始关闭，突然另一个人试图进入电梯，电梯没有启动，门再次打开。现在又有另一个人进入电梯，电梯再次打开，直到没有人进入电梯才开始运行。
![](/images/debounce.png)

从上图可以看出，事件处理始终是在一系列连续事件结束后执行，那能不能在连续事件一开始发生的时候就执行呢，如下：
![](/images/debounce_immediately.png)

在`underscore.js`里面有个`immediate`选项可以实现这个功能。

debounce使用场景：resize事件、用户连续输入且发送请求

resize：
```javascript
$(window).on('resize', _.debounce(function() {
  // Do expensive things
}, 100));
```

#### throttle
throttle与debounce的区别在于，throttle保证在固定的时间里事件处理函数能够执行一次。比如，用户向下滚动页面，需要不断计算距离页面底部的距离，如果解决页面底部，则发送请求获取数据，且渲染页面。此时，debounce不适合用来处理这种情景。通过throttle可以让事件处理函数每隔xx毫秒执行一次计算。

```javascript
$("body").on('scroll', _.throttle(function() {
  // Do expensive things
}, 100));
```
