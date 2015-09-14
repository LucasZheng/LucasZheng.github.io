title: 一行css代码实现全屏显示
description: 使用css中的vh实现页面的全屏展示
date: 2015-09-15 22:27:46

categories: 
- css

tags: [css, vh]

---
在前端开发中我们可能会遇到这种情况，页面展示的内容比较少，而又要想要当前页面能够全屏展示，比如当内容不足以展示一屏时，footer始终处于当前viewport的最下方<!-- more -->，这时比较常见的做法就是在html和body标签上都加上`height:100%`来实现。
还有一种个性化的需求就是，我们想让页面中的个别section能够始终占据一定百分比的高度，这时我们往往就需要使用data-attributes来实现，比如使用`data-autosize="0.6"`，然后通过javascript计算来赋予动态的高度。
但现在我们可以只通过一行css代码来满足上述功能，那就是`.section{height:100vh}`，vh即viewport height，1vh＝1%的浏览器视窗高度。通过这个属性浏览器会自动帮你计算当前的视窗高度，并赋予section对应的高度。
这个属性非常有用，假如你想除了第一个section外其他的section都具有100%的视窗高度，你可以这样写：
```css
.section {height: 100vh;}
.section--first {height: 90vh;}
```
现在主流浏览器对这个属性都支持得比较好，包括IE9+，你也可以通过[caniuse.com](http://caniuse.com/#feat=viewport-units)来查询具体浏览器的支持情况。当然如果一些低版本的浏览器如果不支持这个属性的话，也可以通过[polyfill](https://github.com/saabi/vminpoly)来实现。

