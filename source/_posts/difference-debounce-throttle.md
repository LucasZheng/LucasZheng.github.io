title: debounce和throttle的区别 
description: 理解debounce和throttle之间的差异
date: 2015-10-11 22:52:13
categories:
- javascript

tags: [performance]
---
debounce和throttle常被用来处理DOM相关事件，通过限制事件的执行频率来提升性能。但它们之间也有使用场景的差别。<!-- more -->
throttle强调的是函数间隔多长时间执行一次。比如说在10秒中函数执行1000次，但是如果你通过throttle设置每隔100毫秒执行一次的话，那么它就最多会执行100次。
debounce则是在指定时间内如果没有执行该函数的话，则马上执行该函数，比如说100毫秒后如果还没有执行处理函数，那么就在100毫秒后马上执行。比如一个函数在3秒内执行1000次，然后停止执行。如果你通过debounce设置函数的执行时间为100毫秒，那么该函数只会在3.1秒的时候执行一次，在debounce设置的100毫秒内由于会触发函数的执行，因此它不断的重置这个timer。
#### 使用场景
这两个函数常被用来处理DOM事件，比如滚动页面和resize页面大小。比如你在一个元素上绑定了scroll事件，想让元素向下滚动5000px，并且触发100次以上的scroll事件，如果你的处理函数涉及很大的计算量的话，你就会发现在滚动时页面会存在一定的性能问题，如果此时你减少scroll事件的执行次数，那么性能就会得到很大的提升。
常用的场景：
+ 等到resize结束时执行处理函数
+ 等用户停止输入时触发ajax调用
+ 考虑scroll的位置并设置最多50毫秒执行一次scroll事件
+ 当你在页面上拖动元素时

#### 如何使用
这两个函数在`Underscore`和`Lodash`中都得到了实现。即使你在项目中不会使用到这两个库，你也可以把它们抽出来放在你自己的项目中使用。
jQuery使用：
```javascript
$("body").on('scroll', _.throttle(function() {
  // Do expensive things
}, 100));
```

resize：
```javascript
$(window).on('resize', _.debounce(function() {
  // Do expensive things
}, 100));
```

