title: Ember 2.0中的五个重要思想 
description: Ember2.0的五个重要思想
date: 2016-02-18 22:52:13
categories:
- mvc

tags: [Ember]
---
从Ember 2.x开始，它的模型在概念上比以前清晰明了的多，你不用再纠结于`template`，`route`，`view`，`controller`，`model`和`component`等概念。<!-- more -->
#### 新版本的框架
在Ember 2.x的版本中我们需要注意其中的五个概念：`object model`，`routing`，`models`，`services`和`components`。
![](/images/ember-new-framework.png)

Ember的`routing`层会根据URL来指定一个`model`来处理对应的`component`用于展示和交互，其中也许会用到`service`来获取`models`。
如下是一个详细的示例图：
![](/images/ember-simplified.png)

处理流程如下：
+ `Router`解析URL`/items/2`，并且委派给对应的route：`ItemRoute`来处理，同时携带参数：`item_id=2`
+ `ItemRoute`调用它的`model()`方法，这个方法会通过service来获取数据并返回一个model对象
+ renderComponent()方法会渲染对应的一个component：`ItemDisplayComponent`
+ `ItemDisplayComponent`主要用来处理渲染页面，用户交互等逻辑处理

这就是Ember新版本的一个基本处理流程。下面我们探讨下它的五个基本概念。

#### Object对象
Javascript的原生object对象缺乏observe的功能，Ember引入了`Ember.Object`，它在原生object对象的基础上增加了observers，计算属性，继承，mixins和其他有用的功能。
这也是为什么我们需要使用`object.set('id', "value")`而不是`object.id = "value"`。
```javascript
fullName: Ember.computed('firstName', 'lastName', function() {
  return this.get('firstName') + " " + this.get('lastName');
});

fullNameDidChange: Ember.observer('fullName', function() {
  this.send('fullNameChanged');
});
```

自动监控对象的改变并更新对应的展现给我们的应用带来了极大的便利。Ember中的大多数对象，包括routes，Ember data models，services和components都继承了`Ember.Object`。

#### Routing
Ember是基于URL驱动的框架，如下：
```javascript
// app/router.js
...
this.route('item', { path: '/items/:item_id' });
...
```

需要注意的是，在Ember中，规范比配置更重要。当我们无论何时指定一个`item` route时，Ember就会默认指向`ItemRoute`这个类，且它的路径为`app/routes/item.js`，如果没有找到这个类，它就会自动给我们创建一个。
`ItemRoute`会调用`model()`方法：
```javascript
// app/routes/item.js

export default Ember.Route.extend({
  model(params) {
    return {
      id: params.item_id,
      text: "This is item " + params.item_id
    }
  }
});
```

一旦`model()`有返回后，我们的`renderComponent()`就会用这个model数据来调用`ItemDisplayComponent`。
routes不仅仅是把数据传下去，同时它也接受actions，actions来自于components或其他的routes，如：
```javascript
export default Ember.Route.extend({
  actions: {
    remove(item) {
      item.remove().then(() => {
        this.transitionTo('index');
      });
    }
  }
  ...
});
```

我们更新下流程图来反应`remove` action：
![](/images/ember-simplified2.png)

这个图也反应了Ember的`data down, actions up`设计思想。

#### Models
上面model中只返回了原始的javascrip对象数据，实际上它还可以用来处理更复杂的情形。

model可以通过`$.getJSON()`来获取数据。
```javascript
export default Ember.Route.extend({
  model() {
    return $.getJSON('/items.json');
  }
});
```

这是一个异步的请求，并且返回`Promise`对象。
```javascript
export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('item', params.item_id);
  }
});
```

上面的请求会返回一个`DS.Model`实例，所有的Ember Data models都继承于它。

#### Services
上面我们调用了`this.store`，但route是如何获取`store`的呢？实际上Ember是通过它的Injection API将它的store service注入到应用中的所有routes中。
我们也可以创建自己的service，然后通过注入的方式来调用：
```javascript
export default Ember.Route.extend({
  crypto: Ember.inject.service()
  ...
})
```

Ember会自动的查找`app/services/crypto.js`文件，并且可以通过`this.get('crypto')`来使用这个service。

#### Components
Components分为两部分：模版和逻辑处理文件。当前是通过如`{{my-component}}`方式来加载component，后面我们会使用`<my-component>`方式来加载。
```javascript
// app/components/item-display.js

export default Ember.Component.extend({
  isModelTwo: Ember.computed('model.id', function() {
    return this.get('model.id') == 2;
  })
});
```

模版：
```html
{{!-- app/templates/components/item-display.hbs  --}}

<h1>{{ model.text }}</h1>

{{#if isModelTwo}}
  <p><strong>OMG MODEL TWO!</strong></p>
{{/if}}
```

Components中可以嵌套包含其他的components：
```html
<h1>{{i18n-display text=model.text}}</h1>

{{#if isModelTwo}}
  <p><strong>OMG MODEL TWO!</strong></p>
{{/if}}

{{ad-items language="en" seed=model}}
```
