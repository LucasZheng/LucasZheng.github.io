title: CSS的Code review
description: Css的code review都做些什么，有什么好的工具可以帮助我们来review
date: 2015-09-20 22:52:13
categories:
- css

tags: [css, code review]
---
许多产品的代码在发布之前都要进行code review，要么是同事之间进行review，要么是借助第三方工具进行检测，code review能让我们的代码更加健壮，减少产品的bug出现。<!-- more -->
#### 为什么css要进行review
由于css的写法有很大的灵活性，很多人质疑为什么要花大量的时间和精力来放在css的review上，这可能会延迟产品的发布。
同时，code
review往往是让其他人以他们自己的角度来review代码的规范性和正确性，而这也是人工检测与机器检测之间的差别，因为机器检测往往只针对规范性检测，而不能保证代码逻辑的正确性。
#### 保证样式的正确性
首先要保证我们写的css代码样式符合设计师的mockup，如果是响应式设计，css还必须根据各个breakpoint写不同的样式代码。

#### 样式的一致性
这要求我们对产品的css写法要保持一致性，包括命名规范和文档注释，这有利于将来我们对代码的维护与增强。
在review这块前，我们需要先了解如下的问题：
+ 这个产品开发是否有对应的代码规范，如果有，review的代码是否符合规范？
+ 代码在必要的地方是否有注释？代码中比较难理解的元素、属性以及hacks是否有对应的注释，为什么这样写？

**可用的工具**
+ [CSS Lint](http://csslint.net/) 可用对代码中常见的语法错误及代码规范进行检测，它也可用作为[Grunt](https://github.com/gruntjs/grunt-contrib-csslint)和[Gulp](https://www.npmjs.com/package/gulp-csslint)的一个task来运行。
+ [CSS Style Guides](https://css-tricks.com/css-style-guides/)可用借鉴这个style guide来修改，从而形成你自己项目的style guide。
+ [What makes for good docs](http://metafizzy.co/blog/what-makes-for-good-docs/) 

#### 浏览器的兼容性
上面的检测完成后，我们就需要关注下代码对浏览器的兼容性。
检测前需要了解的事情：
+ 这个产品需要满足在哪些设备和哪些浏览器上运行？我们的测试中是否都有对应的检测？
+ 这个站点是否有统计功能？如果有，我们可用拿到一些统计数据，得知哪些浏览器是我们重点测试的对象。
+ 是否需要对某些设备或浏览器写特殊的`hacks`？如果是，那么可否用另外一种方式来避免hacks？

**可用的工具**
+ [Can I Use](http://caniuse.com/) 可用借助这个查询某个css属性是否在特定的浏览器版本上得到实现。
+ [Ghostlab](http://vanamco.com/ghostlab/) 可用在不同设备的浏览器上同步进行测试
+ 跨浏览器测试服务 [Cross Browser Testing](http://crossbrowsertesting.com/) 或者 [Browserstack](https://www.browserstack.com/)

#### 特殊检测
现在我们需要对一些特定元素进行检测，看看是否可用重构他们：
检测前需要了解的事情：
+ 是否有使用ID？如果有，能否用class代替？规范中是否有明确说明？
+ 是否有使用`!important`？如果有，是否可用重构来避免使用它？
+ 某些元素是否针对浏览器写上对应的前缀？
+ css中的代码是否有模块化区分？

**可用的工具**
+ [CSS Specificity Graph Generator](https://jonassebastianohlsson.com/specificity-graph/) 一个可视化的工具，用来对css文件进行全局分析。
+ [Specificity Calculator](http://specificity.keegan.st/) 对某个特性元素样式进行视觉分析

#### 预处理
如果项目开发中有使用到预处理，那么还需要考虑如下事情：
+ 是否有变量已经声明而重复声明的？声明变量是否合理？是否必须引入新的变量？
+ 像`extends`，`mixins`，是否得到有效使用？
+ css代码是否符合特定的预处理规范？

**可用的工具**
+ [Sass Style Guide](https://css-tricks.com/sass-style-guide/)
+ [Sass Guidelines](http://sass-guidelin.es/)

#### 性能
+ 最终的css代码有多大？是否需要压缩最小化和gzip？
+ 我们如何引入加载的css文件？通过`<link>`或者`@import`？能否减少css的请求数量，是否异步加载？
+ css文件是否需要缓存？

**可用的工具**
+ [CSS Stats](http://cssstats.com/) 通过这个平台可以得到css文件的一些统计数据，包括字体和颜色
+ [CSS Dig](http://cssdig.com/) 功能和上面类似，但它是一个Chrome插件
+ [unCSS](https://github.com/giakki/uncss) 可以通过对比html和css文件，删除未使用的css代码
+ [loadCSS](https://github.com/filamentgroup/loadCSS) 异步加载css

#### 总结
上面讨论的很多工具并不是在项目中都要使用到，我们可以根据自己的具体需求选择一些实用的工具来提升我们的代码质量和开发效率。 
