title: css的模块化
date: 2015-03-22 10:59:50
description: 如何让我们的css实现模块化，如何在项目中编写高效、可扩展和可维护性的css代码
categories: 
- css

tags: [css, modular, BEM]
---
前段时间项目组的同事在做tech share时分享了一种新的css代码写法，名称叫BEM。下来了解了下，其实就是我们长期以来希望实现css模块化的一种约定规范。<!-- more -->，与此同时，我也查阅了一些其它相关css模块化的文章，因此就结合自己的看法记录在这里。  
css代码在整个前端资源中是比较松散的，大家对css代码没有形成一个统一的规范，大家各自按照自己喜欢的规范来编写代码，只要最终实现的视觉效果一致就ok。所以在一个项目中最终各个开发人员写出来的css代码有很大的差异。随着javascript实现模块化进行得如火如荼的同时，一些开发人员或者大的前端团队也希望能够对css进行模块化，让css代码实现更高的可维护性。在这一发展过程中，出现了诸如：`OOCSS`、`ACSS`、`SMACSS`和`BEM`几种规范，下面就大概讲下它们的规范细节。  
#### OOCSS
OOCSS是Object Oriented CSS的简称，从字面上看，它是采用了面向对象的思想。它的两个主要原则是：  
+ *将结构和样式分开*
将结构和样式分开，可以使你复用样式规则。比如，你不能直接在`.button`上添加渐变样式，而是定义一个渐变规则来让`.button`继承。  
+ *将容器和内容分开*
这意味着你的css规则不能嵌套很深，应该直接给最深层次的元素应用一个class，比如不要这样：
```css
ul li.list-item {
    float: left;
}
```
而是：
```css
.list-item {
    float: left;
}
```
#### ACSS
ACSS是Atomic CSS的简称。这是一种颗粒度更小的命名规则。如下面可以复用的class：
```css
.mt-20 {
    margin-top: 20px;
}

/* Or */
.fl {
    float: left;
}
```
对每一个选择器只应用一个规则，在最大程度上进行复用。比如下面的代码：  
```html
<p>The way we write CSS has changed a lot in last few years, and the abbreviation [...]</p>
<a href="/article/">More→</a>
```
我们想让p元素inline，常规下我们会为p元素新单独写一个inline的样式规则，但如果我们之前有如下的样式规则：

```css
.di {
    display: inline;
}
```
则我们可以直接将它赋给p元素就可以了：
```html
<p class="di">The way we write CSS has changed a lot in last few years, and the abbreviation [...]</p>
<a href="/article/">More→</a>
```
这种命名规范在大的站点中比较实用，它的目录结构一般如下(采用了sass预编译)：
```
atomic-structuring/
├── atoms
│   ├── _button.scss
│   ├── _flag.scss
│   ├── _grids.scss
│   └── _media.scss
├── molecules
│   ├── _banner.scss
│   ├── _custom-post.scss
│   ├── _footer-nav.scss
│   └── _heading-group.scss
├── quarks
│   ├── _links.scss
│   ├── _lists.scss
│   ├── _paragraphs.scss
│   └── _tables.scss
└── utilities
    ├── _base-spacing.scss
    ├── _clearfix.scss
    └── _reset.scss
```
#### SMACSS
SMACSS是Scalable and Modular Architecture for CSS的简称。它和上面讲的规范大同小异，但它更多在于指导你如何写出更加优秀的css代码。你可以通过阅读[ SMACSS website](http://smacss.com/)来了解更多的信息。
#### BEM
BEM代表`blcok`，`element`和`modifier`，它是前端css一种新的命名方法。这种class的命名方法能够让你的css代码更加语义化。这种约定的命名规则非常适合开发大项目的团队。  
命名约定如下： 
 ```css
 .block {}
 .block__element {}
 .block--modifier {}
 ```
 使用`--`和`__`的原因就是你可以在block中使用`-`，如：  
 ```css
 .site-search {} /* Block */
 .site-search__field {} /* Element */
 .site-search--full {} /* Modifier */
 ```
 从BEM给class命名的这种规范可以容易看出当前元素的作用。通过阅读HTML中的一些class，你就能够大概了解DOM片段的结构，一些元素可能是一个组件，一些是构成这些组件的子元素，还有一些是当前组件的修饰符。像如下的代码：
 ```css
 .person {}
 .person__hand {}
 .person--female {}
 .person--female__hand {}
 .person__hand--left {}
 ```
 最顶层的block是** person **，它的子元素有** hand **，同时** person ** 还有一个修饰符** female **，它的下面也有子元素** hand **。如果我们采用常规的css来写，就像下面这样：
 ```css
 .person {}
 .hand {}
 .female {}
 .female-hand {}
 .left-hand {}
 ```
这也可以实现页面样式，但各个class之间就毫无关联。比如`.female`代表什么female呢？`.hand`代表钟表的hand？通过BEM我们就能够明白大概的含义。  
拿上面`.site-search`举例，我们常规的命名如下：
```html
<form class="site-search  full">
    <input type="text" class="field">
    <input type="Submit" value ="Search" class="button">
</form>
```
这种class并没有给我们更多的信息，如果用BEM我们可以改成如下：  
```html
<form class="site-search  site-search--full">
    <input type="text" class="site-search__field">
    <input type="Submit" value ="Search" class="site-search__button">
</form>
```
我们可以看出`.site-search`是一个block，它有一个子元素`.site-search__field`，同时我们也可以看出blcok有一个修饰符`.site-search--full`。  
也许有人会说BEM这种风格太难看了，确实如此，但任何事情都有利和弊两方面。它所带来的作用远比它的外观更重要。而且现在大多编辑器都有自动补全的功能，且gzip完全可以忽略这点增加的代码量。
现在前端开发人员大多喜欢用sass或less来提高css代码的编写效率，同样BEM也很容易嵌套在这些语言中，以sass为例：  
```sass
/* Sass 3.3+ */
.object {
    color: red;
    &__descendant {
        color: black;
    }
}

/* Generates:
.object {
    color: red;
}
.object__descendant {
    color: black
}
*/
```

** 使用or不使用BEM **
BEM所带来的效果是显而易见的，在团队中代码变得更加容易维护，当然也并不是任意class都要遵从BEM的规范，比如：
```css
.caps { text-transform: uppercase; }
```
这个css就不属于任何一个分类，它仅仅是一个单独的样式规则。再比如：
```css
.site-logo {}
```
它也不属于任何一个分类。但如果我们的logo是在header中，则可以用BEM规则：
```css
.header {}
.header__logo {}
```
但这没有必要，BEM的原则就是知道某个元素属于哪个相关的类别。其中使用BEM最难的一点就是要知道什么时候使用它，什么时候不使用它。
#### 如何选择
你可以选择你感兴趣的某种方案，或者是混合使用。对于小的站点，我们不太关注css代码的质量，但对于大的项目，你就需要选择一种合适的方案，让你的css代码在以后可以更容易维护。


