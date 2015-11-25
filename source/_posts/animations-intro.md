title: Web Animation API（一） 
description: 让我们来谈一谈未来的Web Animation API
date: 2015-11-25 22:52:13
categories:
- javascript

tags: [Animation]
---
在接下来的几篇文章中我们将讨论一下web浏览器自身实现的Animation API，长话短说，让我们直接进入正文：<!-- more -->
#### 什么是Web Animations API
Web Animations API 是什么？它主要实现什么功能？
在过去几年中，由于得到css和javascript的支持，动画应用得到更多的重视。但每种动画实现方式都有它自身的有缺点：
+ 浏览器可以运用硬件加速来提升动画的流畅性，但是样式规则需要在css中定义，并且需要javascript来获取动态变化的值；
+ `requestAnimationFrame`已经得到很好的支持，并且浏览器在动画运行时可以进行优化。但是如果有其他javascript运行时，动画仍然有可能出现卡顿，同时也需要大量的数学运算；
+ `setInterval`可以用来实现动画效果，但它并不准确而且卡顿现象也比较突出；
+ `jQuery.animate()`可以让开发者轻松实现动画效果，但同样也会有性能方面的影响；
+ 其他一下库，如`Velocity`和`GreenSock`对动画的性能进行了很多优化，尽管如此，它们仍需要加载类库并进行长期维护。

总的来说，我们希望浏览器能够对它支持的越多越好。Web Animations API就是在浏览器层面对动画进行实现。

Web Animations API刚出来不久，目前只在Chrome和Firefox得到支持，接下来我们看看它实际上能给我们开发者提供什么功能。

#### 创建一个基本的动画
和CSS Animation 相比，WAAPI能够让开发者对动画进行更多的控制。下面我们通过这个API创建一个基本的动画：
```javascript
var player = document.getElementById('toAnimate').animate([
    { transform: 'scale(1)', opacity: 1, offset: 0 },
    { transform: 'scale(.5)', opacity: .5, offset: .3 },
    { transform: 'scale(.667)', opacity: .667, offset: .7875 },
    { transform: 'scale(.6)', opacity: .6, offset: 1 }
  ], {
    duration: 700, //milliseconds
    easing: 'ease-in-out', //'linear', a bezier curve, etc.
    delay: 10, //milliseconds
    iterations: Infinity, //or a number
    direction: 'alternate', //'normal', 'reverse', etc.
    fill: 'forwards' //'backwards', 'both', 'none', 'auto'
  });
```
作为对比，用css实现方式如下：
```css
@keyframes emphasis {
  0% {
    transform: scale(1); 
    opacity: 1; 
  }
  39% {
    transform: scale(.5); 
    opacity: .5; 
  }
  78.75% {
    transform: scale(.667); 
    opacity: .667; 
  }
  100% {
    transform: scale(.6);
    opacity: .6; 
  }
}
#toAnimate {
  animation: emphasis 700ms ease-in-out 10ms infinite alternate
forwards;
}
```
我们依次解释下代码的含义。
```javascript
var player = document.getElementById('toAnimate').animate()
```
我们找到需要实现动画的dom节点并调用animate方法，由于animate是新实现的方法，在使用前你需要先测试是否有这个方法，或者使用[polyfill](https://github.com/web-animations/web-animations-js).
`animate`接收两个参数，一个是数组格式的keyframe效果，一个是动画效果的持续时间及其他参数。于css实现的动画不同，这里可以用一个变量来应用当前的动画，在接下来的应用中，你可以重用或修改这个引用的动画效果。
下面这个图是分别用WAAPI和CSS实现的相同动画，左边是那一列是WAAPI实现的效果，右边是css实现的效果。
{% jsfiddle zt30wxk4 %}
