title: 对Ember Data的一点理解 
description: Ember Data的工作流程和原理
date: 2016-02-23 12:52:13
categories:
- mvc

tags: [Ember]
---
由于项目的后端采用的是Ruby来开发，因此前端也就采用了Ember这一mvc框架，相比较于其他mvc框架而言，Ember内置了强大的Ember Data，它是介于你的应用和持久层之间的一个数据模型。<!-- more -->在没有后端提供API数据时，它能够模拟后端的响应数据从而可以让前后端并行开发，同时它也能帮我们优化处理底层的HTTP请求、缓存响应数据来提升我们的产品性能。

在没有使用Ember Data来获取数据时，我们通常按照下面的方式来请求数据：
`app/components/list-of-drafts.js`
```javascript
export default Ember.Component.extend({
  willRender() {
    $.getJSON('/drafts').then(data => {
      this.set('drafts', data);
    });
  }
});
```

对应组件的模版：
`app/templates/components/list-of-drafts.hbs`
```html
<ul>
  {{#each drafts key="id" as |draft|}}
    <li>{{draft.title}}</li>
  {{/each}}
</ul>
```

这是没有什么问题的，但当你的应用中其他地方也需要获取这个请求的数据时，如：
`app/components/drafts-button.js`
```javascript
export default Ember.Component.extend({
  willRender() {
    $.getJSON('/drafts').then(data => {
      this.set('drafts', data);
    });
  }
});
```
对应的模版：
`app/templates/components/drafts-button.hbs`
```html
{{#link-to 'drafts' tagName="button"}}
Drafts ({{drafts.length}})
{{/link-to}}
```

现在我们的应用对于获取同样的数据需要向后端发送两个不同的请求，这既浪费了我们的带宽资源，也影响了应用的用户体验。而Ember Data提供了一个所有models集合的store对象，组件只展示更新model的数据，至于如何获取model则交给store来处理。这就意味着，对于两个不同组件对同样的一个请求，它只会向后端发送一个请求。当store第一次获取到数据时，它会对其进行缓存。你的所有组件和routes都可以访问这个公有的store对象。当你需要展示或修改一个model时，也都是通过这个store对象来操作。

#### Ember Data的几个核心概念
首先我们来看下Ember Data由哪些部分组成：

`store`：store就是一个数据容器，它存储从持久层获得的响应数据，同时它也扮演着数据请求的一个中间件角色，在Chrome浏览器上，可以通过安装`Ember Inspector`插件来查看store里面的数据。store也是一个Service，默认在Routes和Controllers中可以直接调用。

`adapter`：adapter定义了如何向持久层发送请求，它有一些内置的方法，开发人员可以自定义重置这些方法。比如你需要获取id为123的`Person`数据，Ember怎么知道这个请求的url？是`/person/123` 还是`/resources/people/123`?  一般来说，每个应用有一个`ApplicationAdapter`，其他自定义的adapter都可以继承于它。你也可以针对每个对应的model写一个adapter，如果你想这么做的话，那么就要遵循它的命名规范，比如有一个`BigWidget` model，那个它对于的adapter就应该为`BigWidgetAdapter`。

`serializer`：serializer其实就是Ember Data和持久层中间的一个数据转换器。

下面以一个例子来介绍下Ember Data的工作流程：

比如我们想查找一条数据记录，Ember Data提供了`findRecord`方法，这个方法希望你提供一个model名称和数据记录的id号。

store将会检查这条数据是否已经存在于store中，如果数据存在则继续检查`shouldReloadRecord`属性（默认是false），如果这个值为false，则直接返回缓存的数据，否则即使数据在缓存中，也会向持久层发送一个请求。

当store向持久层发送请求时，首先会找到对应的adapter(如果有的话)，其内置的`buildURL`将提供针对不同的情况如何构建这个请求的url，最后才通过AJAX请求将这个构建好的url发送出去。

当请求获取到响应数据时，serializer将会调用`mormalize`方法来转换原始数据，并将格式转换好的数据存储到Ember Data中，同时也存储在store里面。

![](/images/ember-data-flow.png)

![](/images/finding-unloaded-record-step1-diagram.png)

![](/images/finding-unloaded-record-step2-diagram.png)

![](/images/finding-loaded-record-diagram.png)

#### Model
Models就是应用展现给用户的数据对象，它的数据有时也需要保存到持久层。当从store获取到数据并加载到model对象里面时，components就会自动将数据更新到UI界面上。在Ember Data中，每个自定义的model对象都继承于`Model`，里面包含了所有的属性、model之间的关联关系以及展现给用户的一些数据行为处理。如：
```javascript
export default DS.Model.extend({
  firstName: DS.attr('string'),
  birthday:  DS.attr('date'),
  lineItems: DS.hasMany('line-item'),
  order: DS.belongsTo('order')
});
```

#### Records
一个record就是一个包含数据的model实例，在应用中也可以创建新的record并保存到服务端。一个record通常是根据它的model类型和ID来查找，比如有一个`Person` model，查找id为1的person数据：
```javascript
this.store.findRecord('person', 1); // => { id: 1, name: 'steve-buscemi'
}
```

