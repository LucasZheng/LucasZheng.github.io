title: Vue.js基础教程
description: Vue.js是一个数据双向绑定的MVVM的library，文章将讲述Vue的如何使用。
date: 2015-09-07 20:27:46

categories:
- mvc

tags: [javascript, vue]

---
Vue.js和其他一些流行的框架像Rivert.js，Ractive.js，Angular.js和Knockout.js功能类似，这些框架提供了一个开发交互式app的基本架子。<!-- more -->
原生的HTML作为一种模版语言，在这些框架中得以被继承，它们都提供了一个基于数据绑定的用户模型。而Vue力图尽可能的让这些框架所提供的功能简单化，下面我们可以看看它是如果工作的，它给我们提供了什么样的开发接口。

#### 创建一个Vue实例
在我们让Vue工作前，我们需要先创建一个实例，或者说是一个ViewModel，它会指定到我们对应的某个dom节点上。如下面我们已经有一个id为`myvueinstance`的div节点，我们就可以在Vue实例中用css选择器的方式将它们绑定在一起。

```html
<div id="myvueinstance" class="container">
    
    <!-- build out all kinds of things here -->
    
</div>
```
js中：
```javascript
var viewmodel = new Vue({
    el: '#myvueinstance'
});
```
到此，你就成功创建了一个实例。也就意味着`#myvueinstance`的div节点，以及它所包含的内部节点都具有Vue实例上的特性。
#### 双向绑定
现在我们有一个绑定了Vue实例的div，我们可以可以在这个父节点内部再添加其他的节点。为了说明双向绑定的特性，我们将实现一个带`v-model`指令的input，我们可以简单的将这些指令想象成是一个普通的html属性。实际上我们是通过这些指令扩展了html的功能。
html
```html
<div id="myvueinstance" class="container">
    <div class="row">UI element</div>
    <input type="text" v-model="textinput" class="form-control">
 
    <p> </p>
 
    <div class="row">Vue Instance data object</div>
    {{ $data | json }}
</div>
```
js
```javascript
var viewmodel = new Vue({
    el: '#myvueinstance',
 
    data: {
        textinput: 'Too hot, hot damn.'
    }
});
```
运行这个例子，可以看出改变data中textinput的值会反应到ui上的变化，同时在ui上输入其他值，也会更新textinput。
#### 通过Vue展示列表
在Vue中我们如何展示一个数组类型的条目呢？php有foreach，javascript有for(in)，而Vue使用了v-repeat指令。
js
```javascript
var viewmodel = new Vue({
    el: '#myvueinstance',
 
    data: {
        libraries: ['angular.js', 'd3', 'node', 'jquery']
    }
});
```
html
```html
<ul class="nav nav-pills">
    <li v-repeat="library: libraries" class="active"><a href="#">{{ library }}</a></li>
</ul>
```
我们也可以增加一些交互来改变或删除当前的数组内容。
```javascript
var viewmodel = new Vue({
    el: '#myvueinstance',
 
    data: {
        libraries: ['angular.js', 'd3', 'node', 'jquery']
    },
 
    methods: {
        addLibrary: function () {
            this.libraries.push(this.newlibrary);
 
            this.newlibrary = '';
        },
 
        deleteLibraries: function () {
            this.libraries = [];
        }
    }
});
```
html
```html
<div id="myvueinstance" class="container">
    <div class="row">UI List Element</div>
 
    <ul class="nav nav-pills">
        <li v-repeat="library: libraries" class="active"><a href="#">{{ library }}</a></li>
    </ul>
 
    <p> </p>
 
    <input class="form-control" type="text" placeholder="Type the library name here, then click the button below." v-model="newlibrary">
    <button class="btn btn-info" v-on="click: addLibrary">Click to add library</button>
    <button class="btn btn-danger" v-on="click: deleteLibraries">Click to delete all libraries</button>
 
    <p> </p>
 
    <div class="row">Vue Instance data object</div>
    {{ $data | json }}
</div>
```
#### Vue的事件处理
在早些时候，我们经常使用如：`<div id=”element” onclick=”alert(‘you just clicked this’);”></div>` 这种原生的javascript写法来绑定事件。在用jQuery时，我们就使用如：`$(“#element”).click(function() { alert(“you just clicked this”); });`。而Vue使用了事件监听的方式，在元素上通过使用`v-on`来绑定事件的类型。格式如：`v-on=”event: function”`，这里的事件可以是任何标准的javascript事件，如onfocus, onblur, onchange, onselect, onmouseover, onclick, onload, onsubmit等等，但是要去掉前面的‘on’。而function就是你所希望接收到这个事件的处理函数，它通常是写在Vue实例的methods对象中。
native js
```javascript
<div id="element" onclick="alert(‘you just clicked this’);"></div>
```
jquery
```javascript
$("#element").click(function () {
    alert("you just clicked this");
});
```
通过Vue可以这样：
html
```html
<div id="element" v-on="click: myfunction"></div>
```
Vue js
```javascript
methods: {
  myfunction: function () {
    alert('you just clicked this');
  }
}
```
#### Vue中的Filtering
Vue中的filtering就是一个function，输入一个值经过处理输出想要的东西，在模版中就是通过一个管道符来实现。
在Vue中有许多现成的filters，如：capitalize，uppercase，lowercase，currency，pluralize，json，key，filterBy，和orderBy。下面这个例子将会使用到filterBy和orderBy。我们将会创建一个由对象组成的数组，每个对象中有`id`和`framework`属性。
```html
<div id="myvueinstance" class="container">
    <div class="row">UI List Element</div>
 
    <input type="text" class="form-control" v-model="filterkey">
 
    <table class="table table-hover">
        <thead>
        <tr>
            <th><a href="#" v-on="click: sortvia('id')">id</a></th>
            <th><a href="#" v-on="click: sortvia('framework')">Framework</a></th>
        </tr>
        </thead>
 
        <tbody>
        <tr v-repeat="frameworks | filterBy filterkey | orderBy sortparam reverse">
            <td>{{ id }}</td>
            <td>{{ framework }}</td>
        </tr>
        </tbody>
    </table>
</div>
```
js
```javascript
var viewmodel = new Vue({
    el: '#myvueinstance',
 
    data: {
        sortparam: '',
 
        reverse: false,
 
        fitlerkey: '',
 
        frameworks: [
            {id: '001', framework: 'angular'},
            {id: '002', framework: 'd3'},
            {id: '003', framework: 'node'},
            {id: '004', framework: 'jquery'},
            {id: '005', framework: 'reveal.js'},
            {id: '006', framework: 'impress.js'},
            {id: '007', framework: 'backbone.js'},
            {id: '008', framework: 'meteor.js'},
            {id: '009', framework: 'express'},
            {id: '010', framework: 'moment'},
            {id: '011', framework: 'underscore'},
            {id: '012', framework: 'gulp'},
            {id: '013', framework: 'react'},
            {id: '014', framework: 'ghost'},
            {id: '015', framework: 'sweetalert'},
            {id: '016', framework: 'select2'},
        ]
    },
 
    methods: {
        sortvia: function (sortparam) {
            this.reverse = (this.sortparam == sortparam) ? !this.reverse : false;
            this.sortparam = sortparam;
        }
    }
});
```
#### 自定义filters
Vue中现有的filters可以满足大部分所需，此外你也可以在Vue实例上创建自己的filters或者创建全局的filters。
在Vue实例上创建
```html
<div id="myvueinstance" class="container">
 
    <h4>{{ string | upper }} </h4>
 
    <h4>{{ string | lower }}</h4>
 
</div>
```
javascript
```javascript
var viewmodel = new Vue({
    el: '#myvueinstance',
 
    data:{
        string: 'Custom Filters'
    },
 
    filters: {
        upper: function(value) {
            return value.toUpperCase();
        },
 
        lower: function(value) {
            return value.toLowerCase();
        }
    }
});
```
Vue全局filters
```html
<div id="anotherdiv" class="container">
 
    <h4>{{ string | upper }} </h4>
 
    <h4>{{ string | lower }} </h4>
 
</div>
```
js
```javascript
Vue.filter('upper', function (value) {
    return value.toUpperCase();
});
 
Vue.filter('lower', function (value) {
    return value.toLowerCase();
});

var anothermodel = new Vue({
    el: '#anotherdiv',
 
    data:{
        string: 'Spooky action from a distance.'
    }
});
```
#### Vue组件
在Vue中可以创建自定义的组件来复用，也就是说你可以自定义一个标签，并在Vue中得以实现，比如我们创建一个<alert>标签：
```javascript
Vue.component('alert', {
    template: '<div class="alert alert-success" role="alert"><b>Smashing!</b> Nice work.</div>'
});
 
var viewmodel = new Vue({
    el: '#myvueinstance'
});
```
html
```html
<div id="myvueinstance" class="container">
    
    <alert></alert>
    
</div>
```
#### Vue组件中的props
通过props，可以让我们的组件变得更加灵活可控：
```javascript
Vue.component('alert', {
    
    props: ['type', 'bold', 'msg'],
    
    template: '<div class="alert alert-{{ type }}" role="alert"><b>{{ bold }}</b> {{ msg }}</div>'
});
 
var viewmodel = new Vue({
    el: '#myvueinstance'
});
```
html
```html
<div id="myvueinstance" class="container">
 
    <alert type="info" bold="Greetings." msg="This is some information."></alert>
    <alert type="warning" bold="Slow down." msg="You might crash."></alert>
    <alert type="danger" bold="Oh no!" msg="The program just crashed!"></alert>
    <alert type="success" bold="Rock Out" msg="with your Props out!"></alert>
 
</div>
```
props数组中的值就是你绑定在自定义标签上的属性，你可以在标签上提供任何属性。
#### Vue中的自定义指令
我们知道Vue中指令在模版中都是以`v-`标识的，此外除了一些定义好的指令外，我们也可以创建自定义的指令。比如我们可以创建一个`v-twitter`指令，点击它可以打开一个窗口来分享我们的文章到twitter上。
```javascript
Vue.directive('twitter', function (message) {
    
    this.el.addEventListener('click', function () {
        
        var width = 818,
            height = 400,
            left = (document.documentElement.clientWidth - width) / 2,
            top = (document.documentElement.clientHeight - height) / 2,
            url = 'https://twitter.com/intent/tweet?text=' + message + '&url=http://vegibit.com',
            opts = 'status=1' +
                ',width=' + width +
                ',height=' + height +
                ',top=' + top +
                ',left=' + left;
 
        window.open(url, 'twitter', opts);
        return false;
    });
});
 
var viewmodel = new Vue({
    el: '#myvueinstance'
});
```
html
```html
<div id="myvueinstance" class="container">
 
    <button v-twitter="'You gotta see how cool Vue.js is!'" class="btn btn-info">Share this on Twitter</button>
 </div>
```
#### 总结
我们看到通过Vue这个库可以实现很多功能，创建Vue实例，数据双向绑定，遍历数据，事件，过滤，组件，指令以及更多的特性。如果你对Vue感兴趣的话，可以参考它的官网：[Vue.js](http://vuejs.org/)

