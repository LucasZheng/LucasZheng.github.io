title: Sass 样式指南
description: Sass style guide
date: 2015-07-12 16:32:18

categories:
- css

tags: [css3, css, sass]
---
随着越来越多的人使用Sass, 我们需要考虑如何让我们的写法更加规范正确。[CSS style guides](https://css-tricks.com/css-style-guides/) 的内容比较全面，因此我们可以使用其中的某些写法准则来应用到Sass中。<!-- more -->  
下面是我自己总结的一些规范，或许对你写Sass有一些帮助。如果你需要更多的例子，[Sass Guidelines](http://sass-guidelin.es/) 或许更加适合你。  
这篇文章是关于Sass写法规范的一些基本准则，但作为css的延伸，你需要先遵循上面链接中的css写法基本规范。  
#### 将@extend放在第一位
```css
.weather {
  @extend %module; 
  ...
}
```
`@extend`可以让你继承其他类的样式，把它放在最前面方便我们写的规则重置它里面的某些样式。如果你还不清楚什么时候使用`@extend`，什么时候使用`@include`，[a nice job of differentiating the two](http://csswizardry.com/2014/11/when-to-use-extend-when-to-use-a-mixin/)，你可以参考下这片文章。  
#### 将@include放置在其次的位置
```css
.weather {
  @extend %module; 
  @include transition(all 0.3s ease-out);
  ...
}
```
下一步是引入mixins和其他的一些functions，同样这种写法也方便我们重置某些样式。
#### 接着是"Regular"样式
```css
.weather {
  @extend %module;
  @include transition(all 0.3s ease-out);
  background: LightCyan;
  ...
}
```
同样将外部的样式放置在`@extend`和`@include`后方便我们重置某些样式。
#### 接着是伪类和伪元素样式
```css
.weather {
  @extend %module;
  @include transition(all 0.3s ease-out);
  background: LightCyan;
  &:hover {
    background: DarkCyan;
  }
  &::before {
    content: "";
    display: block;
  }
  ...
}
```
伪类和伪元素直接和当前元素相关，所以我们应该将它们放置在其他选择器之前。
#### 将其他选择器置后
```css
.weather {
  @extend %module; 
  @include transition(all 0.3s ease);
  background: LightCyan;
  &:hover {
    background: DarkCyan;
  }
  &::before {
    content: "";
    display: block;
  }
  > h3 {
    @include transform(rotate(90deg));
    border-bottom: 1px solid white;
  }
}
```
#### 不要在Sass中针对浏览器加前缀
浏览器前缀随着浏览器版本的不断更新可能不再需要，所以如果你在编译Sass时使用[Autoprefixer](https://css-tricks.com/autoprefixer/)， 它会自动帮你添加浏览器前缀。
#### 嵌套不要超过三层
```css
.weather {
  .cities {
    li {
      // no more!
    }
  }
}
```
嵌套超过三层的话，样式就不好复用，也难以阅读和理解。
#### 嵌套代码不要超过50行
嵌套代码如果超过50行的话，可能在某些小界面的代码编辑器上就显示不全，当然一开始阅读起来也会有一定困难。
#### 以组件方式组织代码
这点上没有强制规定，但我们将代码以组件方式组织，也方便我们的阅读和以后的维护。
#### 以正确的顺序引入外部文件
```css
/* Vendor Dependencies */
@import "compass";

/* Authored Dependencies */
@import "global/colors";
@import "global/mixins";

/* Patterns */
@import "global/tabs";
@import "global/modals";

/* Sections */
@import "global/header";
@import "global/footer";
```
#### 将代码按组件颗粒化
```css
...

@import "global/header/header/";
@import "global/header/logo/";
@import "global/header/dropdowns/";
@import "global/header/nav/";
@import "global/header/really-specific-thingy/";
```
这种方式方便我们只引入需要的组件样式，其他不相关的代码就不会被加载进来。
