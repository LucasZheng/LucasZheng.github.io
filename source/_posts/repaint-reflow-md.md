title: web性能优化之－－reflow和repaint
date: 2015-03-29 15:57:47
description: repaint和reflow是如何产生的，如何减少它对页面的视觉体验
categories:
- performance
tags: [performance, reflow, repaint]
---
也许每个前端开发人员在去面试时都会遇到这么一个问题：‘从浏览器地址栏输入url后到页面完全展现内容，这之间都发生了什么事情？’，这个问题在网上有很详细的答案，<!-- more -->你可以回答的比较简单，也可以回答得特别详细，看你自己的掌握程度。今天在这里概述下其中的一个环节：reflow和repaint。因为在我们提到web性能优化的时候，我们一般想到的诸如：合并压缩静态文件、css置顶，js置低等方法，一般我们很少会想到reflow和repaint其实对web页面的性能也有一定的影响。  
#### 浏览器render流程
浏览器在获取到后端传过来的内容后，就开始执行render流程。不同内核的浏览器render流程略有不同，但下面的图给了一个大致的工作流程：  
![render流程](/img/render.png)
+ 浏览器解析HTML代码并且构建DOM树。此时HTML代码中元素的相对位置和DOM树是一致的。
+ 解析css代码。包括UA自身的stylesheet、用户自身的样式文件、外链的样式文件、inline 样式和代码中设置的style样式。
+ 开始构建渲染树(render tree)。渲染树和DOM树比较相似，但渲染树能够根据style进行调整，比如一个`div`设置了`display:none`，那么这个元素就不会在渲染树中存在。同样对于`head`元素也不会存在。渲染树中的元素可以理解为一个盒子，有`width`,`height`, `border`,`margin`等属性。
+ 渲染树完成后，浏览器开始绘制（draw）。

** 举个例子 ** 
html 代码：
```html
<html>
<head>
    <title>Beautiful page</title>
</head>
<body>
    <p>Once upon a time there was a looong paragraph...</p>
    <div style="display: none">Secret message</div>
    <div><img src="..." /></div>
    ...   
</body>
</html>
```
构建出的DOM树如下：
```html
documentElement (html)
    head
        title
    body
        p
            [text node]    
        div 
            [text node] 
        div
            img   
        ...
```
渲染树如下：
```html
root (RenderView)
    body
        p
            line 1
    div
        img
    ...
```
#### reflow和repaint
在页面初始化过程中至少会有一次repaint的layout（除非你是一个空白页面）。页面打开后，在`input`中输入信息时会有下面的一系列变化产生：
+ 部分或整个渲染树需要重新计算布局，这称之为reflow。所以页面初始化时必然有一次reflow。
+ 屏幕上的部分元素发生改变，由于改变了某个节点的属性，比如背景色，屏幕需要重新更新。这称之为repaint。

其实reflow和repaint可以如下定义：

+ 当元素的视觉样式发生改变，但并不会重新引起layout时，就会触发浏览器repaint。比如改变outline、字体颜色、背景颜色等。
+ 当改变某个元素的行为会导致部分或所有页面元素发生layout时，就会触发浏览器reflow。比如隐藏/显示元素、改变元素的尺寸大小等。

#### 聪明的浏览器
reflow和repaint都会占用cpu和内存资源，任何一个元素的属性发生改变都会触发reflow或repaint，或者两个都同时触发。幸运的是，当前许多浏览器都对repaint和reflow进行了优化，浏览器就会尽量减少它们所带来的负面影响。其中的一个策略就是不要reflow或repaint，或者至少不立即执行。浏览器会建立一个队列，将每次的改变放到这个队列中，等到一定程度时一次性flush这个队列，这就只会产生一次reflow计算。不幸的是，一些浏览器特别是IE还没有做到这点。
#### reflow是如何引起的
```html
<body>
<div class=”error”>
    <h4>My Module</h4>
    <p><strong>Error:</strong>Description of the error…</p>
    <h5>Corrective action required:</h5>
    <ol>
        <li>Step one</li>
        <li>Step two</li>
    </ol>
</div>
</body>
```
上面的html代码片段中，如果`p`元素发生了reflow，那么它的子元素`strong`也会跟着reflow，因为它是`p`的子元素，同时`p`的父元素（包括父元素以上的元素）和兄弟元素也都会跟着reflow。大部分的reflow都会引起浏览器的重新re-render。   
ok，我们来看下有哪些因素能引起reflow：
+ 调整window大小或滚动页面
+ 改变font
+ 添加或删除stylesheet
+ 内容改变。比如用户在input里面输入内容
+ 激活css的伪类。比如：`:hover`
+ 操作class属性
+ 通过script操作DOM（添加、删除、更新）
+ 计算offsetWidth和offsetHeight
+ 设置style的某个属性

如何避免或减少reflow带来的影响？  
可以从css和javascript两个主要方面来进行优化。
** css **
+ 降低class的作用域  
  reflow的元素可以自顶而下和自下而顶的引起其它元素的reflow。reflow是不可避免的，但我们可以尽量减少它的影响。通过降低class的作用域，我们可以将reflow的影响面缩小。比如你应该避免在容器的最外层改变class，因为它会影响容器内的所有子元素显示。面向对象的css代码原则上是将class绑定到它所实际需要作用的元素上。  
+ 避免设置大量的inline style  
  我们知道操作DOM一般都比较慢。我们可以把所有的change都用DOM片段组装好，然后一次性应用到DOM上去。同样，通过style属性来设置样式也会引起reflow，并且是每个样式都会引起一次reflow。所以，将所有的style都用class来实现，然后将这个class应用到元素上，这样就只会有一次reflow。  
+ 用`fixed`或`absolute`来设置动画  
  通过这种方式来设置元素布局，当自身reflow时不会引起全局的reflow。  
+ 尽量使动画更流畅  
  Opera建议应该使动画过渡更流畅，意思就是我们应该每次只移动1px，但是如果动画及其子元素的reflow占用了浏览器100%的cpu时，浏览器就会重新更新整个动画流程。如果我们一次移动3px，那么在性能比较差的机器上或移动设备上就显得不那么流畅。  
+ 避免使用table布局（或设置table-layout:fixed）  
  table中的任何一个元素发生layout改变时，都会引起整个table的reflow。在需要用table的地方，我们尽量设置`table-layout`属性（任何非auto值），这可以让整个table一行一行的进行渲染。  
+ 避免css中的表达式  
  css中的表达式都会有一定的开销。当页面中的元素reflow时，这些表达式每次都会重新计算，可能每秒中都会计算成千上万次。这个要特别注意！

** javascript **
+ ‘采用offline模式操作DOM’  
   你可以将所有的DOM操作在js中完成，然后一次性替换DOM树上的节点。  
+ 避免频繁的计算style  
  如果你需要计算style，你可以将第一次计算的值缓存起来供后面的计算使用。  

#### 测试工具
  推荐一个测试工具：`DynaTrace`,通过它你可以清楚看到页面是在何时reflow、何时repaint，分别占用了多长时间，也还可以看到用户的事件，如click，mouse相关事件，keyboard事件等。当然这个工具提供的功能远不止于此，你可以深入的研究下，结合其它的性能测试工具来对你的网站做一个全面的性能分析。

