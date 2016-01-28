title: 用CSS3中的伪类实现自定义有序列表样式 
description: 用css3中before伪类元素实现自定义有序列表样式
date: 2016-01-28 22:52:13
categories:
- css

tags: [css3]
---
当我们用`ol`标签写一组列表时，浏览器会自动给我们在每个列表前生成一个递增的数字，但往往这个数字的样式不是我们所想要的，下面介绍一种可以用css3的`::before`属性来自定义列表前缀及样式。<!-- more -->
首先我们可以看下浏览器给我们生成的有序列表默认样式：
![](/images/ol_default.jpg)

具体代码实现如下：
{% jsfiddle oeh4bL0g result,html,css %}

在css中为`counter-increment`属性自定义一个值，可以任意命名，同时给`::before`伪类中的`counter`赋以相同的名称，在这个伪类定义中还可以任意添加其他css样式。
当前例子中只是简单的给我们产生了列表编号，如果我们想要实现如`2.`这种样式的话，我们可以这样写：`counter(step-counter) ".";`，里面可以写你所想要的任何内容。
用这种伪类实现的方式可以减少我们模版中的DOM节点，让css来为我们实现想要的样式，同时也能保证代码的简洁性。
最后提一点，`::before` 和 `counter-increment`在各个浏览器中支持都比较好，详见[::before](http://caniuse.com/#search=%3A%3Abefore) 和 [counter-increment](http://caniuse.com/#search=counter-increment)。



