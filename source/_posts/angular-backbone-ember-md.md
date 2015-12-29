title: Angular VS Backbone VS Ember
date: 2015-03-17 16:26:56
description: 对当前流行的三个mvc框架进行了简单的比较分析。
categories:
- MVC

tags: [Angular, Backbone, Ember]
---
在这篇文章中我们将对三种比较流行的MV*框架：AngularJS、Backbone和Ember进行分析比较，在你的项目中根据你的时间和能力选择合适的框架。<!-- more --> 一般我们都会选择比较健壮、稳定的框架，但其实不要被你的这些观点过束缚。在web领域，新的技术更新频率比较快，很快某种比较合适的技术方案就会被淘汰。基于这点，我们将会对这三种框架进行深入的分析比较。
#### 了解框架
我们今天所见到的框架都有一个共同点：它们都是开源、基于MIT license的，并且都是用来解决用MV*模式开发SPA应用的。它们都有views、events、data model和routing。我们先快速的看下这些框架的历史背景，然后深入的进行比较。  
AngularJS作为大型商业产品的一部分出现在2009年，它的主要特点就是双向绑定、注入依赖、测试简单和通过指令来操作html。
Backbone.js是一个轻量级的MVC框架。它出现在2010年，之后很短的时间内就变得很流行。  
Ember最早出现是在2007年，最初是由Sproutlt，后来是Apple来开发的。在2011年，Yehuda Katz，jquery和ruby项目的核心成员之一frok了Ember代码。Ember的用户包括Yahoo!、Groupon和ZenDesk。
#### 社区
社区是选择框架时考虑的一个重要因数。一个大的社区意味着由更多的用户问题提出，更多的第三方模块支持，更多的学习资料。在这三种框架中，Angular在github上的star是最多的，超过其它两种框架的star之和。且Agular在StackOverflow上的提问也是最多的。从最近几年的使用上来看，Angular框架的用户数在急剧的上升，而Ember和Backbone的使用人数则相对平缓。
#### 框架大小
页面的加载时间是你的应用的一个重要指标。用户对较长的加载时间往往是没有耐心的，所以你要想尽一切办法来提高页面的加载速度。对框架而言，由两个考虑的因数：框架大小和框架初始化的时间  
Javascript的静态文件往往通过minifiy和gzip压缩传输。所以我们比较下它们minified-gzipped版本。但仅从自身框架大小来看也是有问题的，比如Backbone.js本身只有6.5kb，但往往还需要加载Underscore(5kb)、jQuery(32kb)或Zepto(9.1kb)。Ember本身大小是90kb，但往往还需要加载jQuery和Handlbars(共计136.2kb)。AngularJS一般不需要加载第三方的库，本身大小仅仅为39.5kb。
#### 模版
Angular和Ember引入了模版引擎。Backbone则让用户自己来决定是否使用模版引擎。下面我们就以实际的例子来分析下三种框架的模版引擎使用：  
##### AngularJS
Angular的模版引擎就是在html中用双大括弧来包含表达式（这里用'[['和' ]]'替代）。  
{% codeblock lang:javascript%}
<ul> 
    <li ng-repeat="framework in frameworks" title="[[framework.description]]">
        [[framework.name]] 
    </li> 
</ul>
{% endcodeblock %}

##### Backbone
Backbone可以和很多的第三方模版引擎结合，默认是使用Underscore。因为Backbone要依赖Underscore，且已经加载到你的页面中，所以你可以在你的应用中直接使用它。
{% codeblock lang:javascript%}
<ul> 
    <% _.each(frameworks, function(framework) { %> 
        <li title="<%- framework.description %>"> 
            <%- framework.name %> 
        </li> 
    <% }); %> 
</ul>
{% endcodeblock %}

##### Ember
Ember当前采用Handlebars作为模版引擎（这里用'[['和' ]]'替代）。
{% codeblock lang:javascript%}
<ul> 
    [[#each frameworks]]
        <li [[bind-attr title=description]]> 
            [[name]] 
        </li> 
    [[/each]] 
</ul>
{% endcodeblock %}

#### AngularJS

** 好的方面 **
Angular给开发者引入了很多新的概念，双向绑定能减少很多的代码量，像下面的jQuery代码片段：
{% codeblock lang:javascript%}
$('#greet-form input.user-name').on('value', function() { 
    $('#greet-form div.user-name').text('Hello ' + this.val() + '!'); 
});
{% endcodeblock %}
用Angular可以改写成如下（这里用'[['和' ]]'替代）：
{% codeblock lang:javascript%}
<input ng-model="user.name" type="text" />
Hello [[user.name]]!
{% endcodeblock %}
Angular可以很好的帮助你构建应用：Controllers, Directives, Factories, Filters, Services 和 Views这些都可以用模块化的方式开发。每种模块代表一种功能角色。Views用来处理UI，Controllers处理UI背后的逻辑，Services用来维护后端的通信及相关的通用性业务逻辑，Directioves则可以用来通过HTML方式创建复用的组件。  
自动的脏检测意味着你可以不用getter和setter方式来控制数据，你只需要修改环境中的某个变量，Angular会自动检测变量的变化并做对应的处理。   

** 不好的方面 ** 
Angular的Directives API比较复杂，而且有些概念对开发者而言比较模糊，directives的配置也比较复杂，需要花一定的时间来熟悉。  
Angular采用原型继承的模式，对那些习惯面向对象语言，如Java和C#的开发者比较难理解。  
Angular可以在模版里面使用表达式，表达式功能很强大，但有时也会出问题，它可以让开发者在模版里面使用很复杂的逻辑判断和表达式，这对于独立的测试是有问题的。像下面的代码就比较糟糕：
{% codeblock lang:html%}
<button ng-click="(oldPassword && checkComplexity(newPassword) && oldPassword != newPassword) ? (changePassword(oldPassword, newPassword) && (oldPassword=(newPassword=''))) : (errorMessage='Please input a new password matching the following requirements: ' + passwordRequirements)">Click me</button>
{% endcodeblock %}

在许多情况下，错误比较难跟踪检测。比如拼写错误或调用一个undefined的function，Angular会对这些问题进行忽略而不加以任何警告提示，所以一般很难查出问题的原因所在。  
最后，Angular的脏检测对开发者也是一个亮点。在非Angular的环境中很容易忘记调用$digest()。另一方面，你必须小心以免触发无限循环的脏检测。一般来说，页面上有大量的交互元素时，Angular会变得比较慢。所以最好在一个页面上不要有超过2000个绑定。
#### Backbone

** 好的方面 **
轻量级的框架，学习曲线单一，只有一些简单的概念需要掌握(Models/Collections, Views, Routes)。它的文档也比较全面，甚至你可以在一小时内看完它整个源代码。正因为它比较小，所以可以快速的构建你的应用。也出现了很多基于Backbone的第三方框架，比如：Aura, Backbone UI, Chaplin, Geppetto, Marionette, LayoutManager, Thorax, Vertebrae。所以和Angular、Backbone比较，你的选择性比较灵活。

** 不好的方面 **
Backbone并不提供架构，它只提供一些基础的工具让你来创建自己的架构。这时你就必须对比如内存管理格外小心。由于它的view缺乏生命周期管理，所以在操作view时还得考虑内存泄露的问题。  
由于Backbone的简单性，它的很多功能都可以由第三方库或模块来实现，所以在你的应用中，面对如此多的选择，你需要花一定的时间来选择哪个库或模块更适合你的需要。  
Backbone缺乏数据双向绑定，意味着你要写很多的代码来操作view。  
由于Backone是直接操作view里面的DOM，所以单元测试也比较困难，而且模版还不好复用。
#### Ember

** 好的方面 **
Ember也不需要写大量的boilerplate代码，它会自动的帮你配置，比如自动决定route和controller的名称。即使你忘记写controller，Ember也会自动帮你创建一个。  
Ember有很好的router和可选的data layer，叫Ember data。不像其它两个框架，它们都有一个data layer（Backbone的Collection/Model，Angular的$resource），Ember通过和后端的 Ruby-on-Rails或者RESTful JSON API结合创建了一个特殊的data module。它可以通过配置来模拟开发所需要的数据。  
性能是Ember设计时考虑的一个重要目标。当某个数据改变时，页面只会对某个节点更新。它还可以在开发时通过HandleBars预编译模版，使你的应用跑得更快。

** 不好的方面 **
Ember API 的文档在稳定版本出来前改变了很多。因此，很多内容或例子都过时了。  
Handlebars在DOM中插入了很多`<script>`标签，随着时间的推移，你的模版中充满了`<script>`，你很难分辨出哪些是你自己的代码。而且这些标签对某些第三方库的功能也会造成影响，比如jQuery UI的Sortalbe。
#### 总结
我们已经了解了各个框架的优缺点，Ember对那些熟悉Ruby, Python, Java, C#或者其它面向对象语言的开发者比较容易上手，同时它的性能也稍占上风。  
Backbone比较小，容易学习，提供了你的应用之基本所需。  
Angular有一个很大的社区来维护，有google作为后台，而且还在不断成长中，它也适合用来构建各种规模的应用。
