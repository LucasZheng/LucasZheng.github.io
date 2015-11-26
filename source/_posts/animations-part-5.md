title: Web Animation API（五） 
description: 让我们来谈一谈未来的Web Animation API
date: 2015-11-27 22:52:13
categories:
- javascript

tags: [Animation]
---
最后我们来看看WAAPI中的路径问题。<!-- more -->

{% jsfiddle 4c993Lzv %}

上面这个例子需要在最新的Chrome浏览器中查看，`motion-path`定义了动画元素运行的路径：
```css
#motioner {
  motion-path: path("M200 200 S 200.5 200.1 348.7 184.4z");
}
```
`motion-offset`设置了元素运行的起点和终点，用WAAPI实现如下：
```javascript
var m = document.getElementById('motioner');
m.animate([
  { motionOffset: 0 },
  { motionOffset: '100%' }
], 1000);
```
css实现：
```javascript
#motioner {
  animation: path-animation 1s;
}
@keyframes path-animation {
  0% { 
    motion-offset: 0;
  }
  100% { 
    motion-offset: '100%';
  }
}
```

{% jsfiddle 08qpcycp %}

最后一个属性是`motion-rotation`， 它定义了元素在运行轨迹上的旋转特性。

{% jsfiddle 18p3suje %}

