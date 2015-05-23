title: 为什么webcomponent很重要
date: 2015-03-16 21:29:18
description: 为什么webcomponent很重要，如何写一个webcomponent
categories:
- webcomponent

tags: [webcomponent]
---
webcomponent的标准即将产生，那么如何编写一个webcomponent？我们为什么需要特别关注这个标准？<!-- more -->
#### 简介
这篇文章对webcomponent的标准进行了大概分析，了解当前哪些浏览器支持webcomponent，且分析了webcomponet所能解决的问题，为什么web要采用这个即将产生的标准。你同样也可以了解到如何通过javascript写一个简单的webcomponent。
#### 什么是webcomponent
长期以来开发者常常以插件或模块的方式来共享代码片段，开发时可以重用这些功能片段。这种方式持续了很长时间，在必要的地方通过引入javascipt、css和html片段。同时你又希望这些引入的文件不会与你的网站或app产生冲突。webcomponent就能很好的解决这个问题，通过对代码进行封装，每个component维护自己的hmtl、style和javascript，不会对外部的环境产生冲突。
#### shadow DOM
你可能对shadow DOM有一定的了解，或者至少听说过这个术语，但是它到底是什么？shadow DOM让开发者在文档中可以创建sub-DOM树，且它与周边的其他元素完全的隔离开来。这种独立的封装组件不会对页面中其他DOM元素有影响。shadow DOM和标准的DOM元素一样工作，你可以对它应用style，可以通过javascript操作它。
#### html 模版
如果你曾经用过比较流行的javascript框架，比如AngularJS，你就会对html模版比较熟悉。模版可以提取出来并且重用。新的HTML5标准中可以实现且可以不依赖javascript框架。
#### 导入html模版
通过对html片段和sub-DOM树抽取成独立的文件，然后使用`<link>`标签来引入这些文件。
#### 自定义元素
最后我们需要通过语义话的元素来引入我们的组件。通过在javascript中创建一个自定义的元素并把它通过如`<my-custom-element></my-custom-element>`link到我们的模版和shadow Dom中，这种方式和AngularJS的指令使用比较相似。
#### 第一个component
现在你已经对webcompent有了一定的了解，下面这个直观的例子将会使你对一些概念加深理解。下面的代码实现了一个非常简单的webcomponet，通过`<template>`标签对HTML和style进行了包裹，通过javascript创建`<favorite-colour>`元素来使得这一组件可以在其他地方复用。
{% codeblock lang:javascript %}
<template>
    <style>
        .coloured {
            color: red;
        }
    </style>
    <p>My favorite colour is: <strong class="coloured">Red</strong></p>
</template>
<script>
    (function() {
        // Creates an object based in the HTML Element prototype
        var element = Object.create(HTMLElement.prototype);
        // Gets content from <template>
        var template = document.currentScript.ownerDocument.querySelector('template').content;
        // Fires when an instance of the element is created
        element.createdCallback = function() {
            // Creates the shadow root
            var shadowRoot = this.createShadowRoot();
            // Adds a template clone into shadow root
            var clone = document.importNode(template, true);
            shadowRoot.appendChild(clone);
        };
        document.registerElement('favorite-colour', {
            prototype: element
        });
    }());
</script>
{% endcodeblock %}
下面通过`<link />`标签在html文件中引入组件，在需要的地方插入`<favorite-colour>`来使用组件。
{% codeblock lang:html %}
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>My First WebComponent</title>
    <link rel="import" href="components/favorite-colour.html" />
</head>
<body>
    <favorite-colour></favorite-colour>
</body>
</html>
{% endcodeblock %}
#### 浏览器对webcomponent的支持情况
当前只有chrome、opera和chrome for android对webcomponent支持比较完善，其他浏览器不存在或部分支持webcomponent，你也可以通过[caniuse](http://caniuse.com/)来随时查询支持情况。  
虽然大部分浏览器对webcomponent不支持，但我们可以使用[webcomponentsjs](http://webcomponents.org/polyfills/)来弥补这一个缺陷，通过引入这个文件，你可以在其他浏览器中编写如上面例子中的webcomponent。
#### webcomponent为什么很重要
webcomponent如果改变web世界？对component进行封装有什么好处？
##### 组件无干扰
上面讲过在文档中引入shadow DOM时不会受其他CSS和Javascript文件所影响，这种好处是显而易见的，当在你的应用中引入第三方组件时，你不用担心它会对应用的其他部分产生问题。比如在组件中存在一个叫colour的css class，其定义color为红色，在组件的外部同样定义一个colour的class，且使用`!important`方式定义color为绿色，此时webcomponent仍然显示红色，不会受到外部css的干扰。
##### 一次编写多次复用
一旦webcomponent得到适当的支持，你就应该在你的应用中编写组件，而不用考虑所用的其他技术，通过组件的一些编写，实现多次复用。
##### 可维护性和可测试性
由于webcomponent的良好封装性，对其维护也比较方便，还可以对组件编写特定的测试用例来进行测试。
##### 抽象性
由于在组件中可以实现比较复杂的逻辑，因此你不需要一开始就构建一个比较复杂的应用，你只需要考虑抽象出许多component，通过共享数据来实现component组件的通信，再把这些组件拼接在一起构成一个完整的应用。
#### 总结
webcomponent的功能比较强大，但是它的完全实现也还需要一定的时间。如果你想对webcomponent有更多的了解，[WebComponents.org](http://webcomponents.org/)是一个不错的选择。
