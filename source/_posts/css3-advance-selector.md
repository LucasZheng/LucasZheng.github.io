title: css3中的子选择器
description: css3中几种常见的子选择器，以及如何使用它们
date: 2014-12-22 21:54:52

categories:
- css

tags: [css3, css]
---
css3中有一些不常见但非常有用的子选择器，掌握好这些选择器的用法能够帮我们节约不少的代码开发工作量。<!-- more -->
#### type类型
`:first-of-type` 选择器匹配属于其父元素的特定类型的首个子元素的每个元素
`:last-of-type` 选择器匹配属于其父元素的特定类型的最后一个子元素的每个元素
`:only-of-type` 选择器匹配属于其父元素的特定类型的唯一子元素的每个元素
所有主流浏览器均支持 :first-of-type 选择器，除了 IE8 及更早的版本。
##### p:first-of-type {color: #36de4e;}
{% codeblock lang:html %}
<div id="target1">
    <h2>Where the Buggalo Roam</h2>
    <p>我是父元素target下的第一个p，所以color为#36de4e</p>
    <div>
        <p>我是父元素div下的第一个p，所以color也为#36de4e</p>
    </div> 
    <h2>The Mutants Are Revolting</h2>
    <p>我是target的第二个p元素，颜色不变</p>
    <h2>Space Pilot 3000</h2>
    <p>我是target的第三个p元素，颜色不变</p>
</div>
{% endcodeblock %}

##### p:last-of-type {color: #36de4e;}
{% codeblock lang:html %}
<div id="target2">
    <h2>Where the Buggalo Roam</h2>
    <p>我是父元素target2下的第一个p，颜色不变</p>
    <div>
        <p>我是父元素div下的最后一个p，所以color为#36de4e</p>
    </div>
    <h2>The Mutants Are Revolting</h2>
    <p>我是父元素target2的第二个p元素，颜色不变</p>
    <h2>Space Pilot 3000</h2>
    <p>我是父元素target2的最后一个p元素，所以color为#36de4e</p>
</div>
{% endcodeblock %}

##### p:only-of-type {color: #36de4e;}
{% codeblock lang:html %}
<div id="target3">
    <h2>Where the Buggalo Roam</h2>
    <p>我是父元素target3下的第一个p，颜色不变</p>
    <div>
        <p>我是父元素div下的唯一一个p，所以color为#36de4e</p>
        <span>hahha</span>
    </div>
    <h2>The Mutants Are Revolting</h2>
    <p>我是父元素target3的第二个p元素，颜色不变</p>
    <h2>Space Pilot 3000</h2>
    <p>我是父元素target3的最后一个p元素，颜色不变</p>
</div>
{% endcodeblock %}

##### :nth-of-type()
`:nth-of-type(n)` 选择器匹配属于父元素的特定类型的第 N 个子元素的每个元素。n 可以是数字、关键词或公式。
常用方法：Odd 和 even 是可用于匹配下标是奇数或偶数的子元素的关键词（第一个子元素的下标是 1）。nth-child(odd)、nth-child(even)
使用公式 (an + b)。描述：表示周期的长度，n 是计数器（从 0 开始），b 是偏移值。如：nth-child(3n+0)
所有主流浏览器均支持 :nth-of-type() 选择器，除了 IE8 及更早的版本。
###### p:nth-of-type(2) {color: #36de4e;}
{% codeblock lang:html %}
<div id="target">
    <h1>这是标题</h1>
    <p>第一个段落。</p>
    <p>第二个段落，颜色为#36de4e。</p>
    <p>第三个段落。</p>
    <p>第四个段落。</p>
    <p>第五个段落。</p>
</div>
{% endcodeblock %}

 #### child类型
 `:first-child` 选择器用于选取属于其父元素的首个子元素的指定选择器
 `:last-child` 选择器匹配属于其父元素的最后一个子元素的每个元素。
 `:only-child` 选择器匹配属于其父元素的唯一子元素的每个元素
 所有主流浏览器都支持 :first-child、:last-child和:only-child选择器。除了 IE8 及更早的版本。对于 IE8 及更早版本的浏览器中的 :first-child，必须声明 <!DOCTYPE>
##### p:first-child {color: #36de4e;}
{% codeblock lang:html %}
<div id="target">
    <p>这个段落是其父元素（target）的首个子元素，颜色为#36de4e</p>
    <h1>欢迎访问我的主页</h1>
    <p>这个段落不是其父元素的首个子元素，颜色不变</p>
    <div>
        <p>这个段落是其父元素（div）的首个子元素，颜色为#36de4e</p>
        <p>这个段落不是其父元素的首个子元素，颜色不变</p>
    </div>
</div>
{% endcodeblock %}

##### p:last-child {color: #36de4e;}
{% codeblock lang:html %}
<div id="target">
    <h1>这是标题</h1>
    <p>第一个段落。</p>
    <p>第二个段落。</p>
    <div>
        <p>这是div中最后一个段落，颜色为#36de4e。</p>
    </div>
    <p>第三个段落。</p>
    <p>这是target中最后一个段落，颜色为#36de4e。</p>
</div>
{% endcodeblock %}

##### p:only-child {color: #36de4e;}
{% codeblock lang:html %}
<div id="target">
    <div>
        <p>这是div中唯一一个子元素，颜色为#36de4e。</p>
    </div>
    <div>
        <span>这是一个 span。</span>
        <p>这是一个段落。但父元素div中子元素不唯一，所以颜色不变。</p>
    </div>
</div>
{% endcodeblock %}

##### :nth-child(n)
`:nth-child(n)` 选择器匹配属于其父元素的第 N 个子元素，不论元素的类型。n 可以是数字、关键词或公式
常用方法：Odd 和 even 是可用于匹配下标是奇数或偶数的子元素的关键词（第一个子元素的下标是 1）。nth-child(odd)、nth-child(even)
使用公式 (an + b)。描述：表示周期的长度，n 是计数器（从 0 开始），b 是偏移值。如：nth-child(3n+0)
所有主流浏览器均支持 :nth-child() 选择器，除了 IE8 及更早的版本
###### p:nth-child(1) {color: #36de4e;}
{% codeblock lang:html %}
<div id="target">
    <h1>这是标题</h1>
    <p>这是target下第二个子元素，颜色不变。</p>
    <div>
        <p>这是div下第一个子元素，颜色为#36de4e。</p>
    </div>
    <p>这是target下第二个p子元素，颜色不变。</p>
    <p>这是target下第三个p子元素，颜色不变。</p>
</div>
{% endcodeblock %}
