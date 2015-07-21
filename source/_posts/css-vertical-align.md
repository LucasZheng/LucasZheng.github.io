title: css中的垂直居中
description: css中最简单的垂直居中写法
date: 2015-07-21 21:54:52

categories:
- css

tags: [css3, css]
---
css垂直居中有很多种写法，但最常见的是我们不知道容器的准确高度，而要让子元素垂直居中，这篇文章介绍一种最简单的写法，仅用3行代码让你的元素垂直居中。<!-- more -->
css的`transform`常用来旋转或缩放元素，但它的`translateY`我们可以用来垂直居中元素。写法如下：
```css
.element {
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}
```
这就是所需要的代码，我们不需要设置父元素的高度或位置，但需要注意的是`transform`在IE9及以上才会得到支持。  
为了使它成为我们项目中更加通用的功能，我们可以设置一个Mixin:  
```css
/* Mixin */
@mixin vertical-align {
  position: relative;
  top: 50%;
  -webkit-transform: translateY(-50%);
  -ms-transform: translateY(-50%);
  transform: translateY(-50%);
}

.element p {
  @include vertical-align;
}

/* Placeholder selector */
%vertical-align {
  position: relative;
  top: 50%;
  -webkit-transform: translateY(-50%);
  -ms-transform: translateY(-50%);
  transform: translateY(-50%);
}

.element p {
  @extend %vertical-align;
}
```
如果你的项目中使用了`autoprefixer`，则不需要写任何浏览器前缀。  
如果使用这种方法出现垂直居中元素模糊的情况，可以用如下方法解决：
```css
.parent-element {
  -webkit-transform-style: preserve-3d;
  -moz-transform-style: preserve-3d;
  transform-style: preserve-3d;
}

.element {
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}
```