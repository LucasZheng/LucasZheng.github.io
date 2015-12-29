title: vue初探
date: 2015-03-14 16:21:55
description: vue是一个MVVM的数据双向绑定的library，比其它MVC框架更轻量级，更容易学习
categories:
- MVC

tags: [vue]
---
MVC对许多前端开发者而言并不陌生，像当前比较流行的backbone、angular、ember等，这些框架提供了比较详细的api文档，能够让开发者在熟悉api的情况下迅速的搭建起一个应用。但这些框架的学习成本比较高，而且开发者要按照框架约定格式来写代码，不容易将第三方库结合进来。<!-- more -->之前看过一篇文章，说是未来的应用架构不再是一个大的framework，而是由众多的library构成。现在mvc领域里也出现许多优秀的library，今天就主要简单的记录下其中之一的vue.js。
#### 概述
vue是由google的一位中国程序员开发出来的，这个library只专注于mvvm的数据双向绑定，不提供controller和router等功能，它没有任何依赖，且压缩后只有大概18kb大小，因此如果你的应用中只需要数据双向绑定的功能话，vue就是你的首选。**重要提示：vue不支持ie8及其以下版本**
#### install
vue提供了多种安装方式：script引入、npm、bower等，可以参照[vue安装](http://vuejs.org/guide/installation.html)
#### 简单的demo使用
template(将delimiters更改为：'[['和']]')：
{% codeblock %}
<div id="demo">
    <h1>[[intro]]</h1>
    <ul>
        <li v-repeat="todos"
            v-on="click: done = !done"
            class="[[done ? 'done' : '']]">
            [[say]]
        </li>
    </ul>
</div>
{% endcodeblock %}
创建vm实例：
{% codeblock lang:javascript %}
var demo = new Vue({
    el: '#demo',
    data: {
        intro: 'todos',
        todos: [
            {
                done: true,
                say: 'Learn JavaScript'
            },
            {
                done: false,
                say: 'Learn Vue.js'
            }
        ]
    },
    filters: {
        //自定义的filters
    },
    components: {
        //自定义的component
    },
    methods: {
        //事件方法
    }
})
{% endcodeblock %}
实例中的写法跟backbone很类似，模版中的指令又跟angular比较雷同，因此如果熟悉backbone和angular的同学一般都比较容易上手。
#### 自定义filter
filter可以写在全局的Vue下，也可以写在实例中。
{% codeblock lang:javascript %}
Vue.filter('wrap', function (value, begin, end) {
    return begin + value + end;
})
{% endcodeblock %}
使用：
{% codeblock lang:html %}
<!-- 'hello' => 'before hello after' -->
<span v-text="message | wrap before after"></span>
{% endcodeblock %}
#### 自定义指令
指令中的bind和unbind方法根据自己的需要添加。同样可以在全局Vue或实例中定义。
{% codeblock lang:javascript %}
Vue.directive('my-directive', {
    bind: function () {
        // do preparation work
        // e.g. add event listeners or expensive stuff
        // that needs to be run only once
    },
    update: function (newValue, oldValue) {
        // do something based on the updated value
        // this will also be called for the initial value
    },
    unbind: function () {
        // do clean up work
        // e.g. remove event listeners added in bind()
    }
})
{% endcodeblock %}
使用：
{% codeblock lang:html %}
<div v-my-directive="someValue"></div>
{% endcodeblock %}
#### 自定义component
{% codeblock lang:javascript %}
var myComponent = Vue.component('my-component', {
    template: 'A custom component!',	//or require a template file
    data: function () {
        return {};
    },
    created: function() {},
    compiled: function() {},
    computed: {},
    methods: {}
});
return myComponent;
{% endcodeblock %}
使用：
{% codeblock lang:html %}
 <p v-component="my-component" v-with="componentData"></p>
{% endcodeblock %}
也可以使用如下方式：
{% codeblock lang:html %}
<my-component v-with="componentData"></my-component>
{% endcodeblock %}
component的生命周期主要有：__created__，__compiled__，__attached__，__detached__，__destroyed__
#### 事件传播
事件传播有以下几种写法：
{% codeblock lang:javascript %}
vm.$dispatch( event, [args…] )  //事件从当前实例向上逐层传播，直到其中一个返回false
vm.$broadcast( event, [args…] ) //事件从当前实例向下逐层传播，直到其中一个返回false
vm.$emit( event, [args…] )      //事件在当前实例传播
vm.$on( event, callback )       //监听某个事件
vm.$once( event, callback )     //只监听某个事件一次
vm.$off( [event, callback] )    //注销事件
{% endcodeblock %}
#### 支持transition
在template片段切换的过程中支持自定义的transition效果，如：
{% codeblock lang:html %}
<p class="animated" v-if="show" v-transition="bounce">Look at me!</p>
{% endcodeblock %}
css：
{% codeblock lang:css %}
.animated {
    display: inline-block;
}
.animated.bounce-enter {
    animation: bounce-in .5s;
}
.animated.bounce-leave {
    animation: bounce-out .5s;
}
@keyframes bounce-in {
    0% {
        transform: scale(0);
    }
    50% {
        transform: scale(1.5);
    }
    100% {
        transform: scale(1);
    }
}
@keyframes bounce-out {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(1.5);
    }
    100% {
        transform: scale(0);
    }
}
{% endcodeblock %}
transition一般包括enter和leave两种状态，因此可以针对这两种状态写不同的过度效果。
