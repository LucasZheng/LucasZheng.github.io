title: Ember2.0是否还应该使用controller 
description: Ember2.0是否还应该使用controller
date: 2016-02-17 22:52:13
categories:
- mvc

tags: [Ember]
---
在Ember 2.x以后Controllers将会被components和services取代。尽管如此，在此前的版本中controller仍然是Ember的一个核心概念。<!-- more -->
#### controller是什么
controller是一个充满业务逻辑的用来装饰`model`的一个对象，如：
```javascript
export default Ember.Controller.extend({
  emphasizeName() {
    return `HEY ${this.get('model.firstName')} !!!`
  },
  isVisible: Ember.computed.and('model.firstName', 'model.lastName')  
})
```

你不用将`emphasizeName`放在`model`中，因为它是需要一定的逻辑处理。
```html
{{#if isVisible}}
  <span>{{ emphasizeName }} Kill the controller!</span>
{{/if}}
```

我们应用中的每个template都对应有一个controller，模版中用到的property也都存在于它的controller中。
值得注意的是：如果controller是用来处理业务逻辑的，那么也许你会认为处理events和DOM交互的逻辑也是在controller中来处理，这是不正确的。在此之前，`View`是用来干这个事情的，但从Ember 2.0后，`View`也被`conponent`取代了。`ArrayController`和`ObjectController`曾经被我们用来作为model的代理，如`controller.firstName`会对应到`model.firstName`，但在2.0后就不会了，我们必须明确的指定`model.firstName`。
让我们看看之前版本的框架图：
![](/images/ember-before.png)

`route`加载model并且同时默认调用`setupController`方法，默认处理如下：
```javascript
export default Ember.Route.extend({

  model() {
    // returns a model
  },

  setupController(controller, model) {
    controller.set('model', model);
  }

})
```

controllers不仅仅用来维护模版的状态，同时他们也是单例模式，这在多个请求会话中同步状态很有用。
已经过时的`needs`允许我们加载其他的controller并且获取它们的属性，比如：`this.get('controllers.position.progress')`
现在我们使用单例的`Services`来处理长连接会话，通过`Ember.inject.service()`这种方式来处理。
#### 什么是component
实际上controller很像component，在某种程度上，设计人员是按照component来组织页面的。因此，在Ember中component就是一个独立功能的template。
![](/images/ember-component-service.png)

#### 我们还需要使用controller吗
在Ember的最新官方文档中指出，controller将会完全被component所取代。
因此，为了以后Ember的升级，我们应该尽量减少controller的使用，现在是基于如下两点因素还在使用：
+ 支持query参数
+ 作为route和template/component的中间连接件

在Ember 2.x中，component将会实现route的功能，新的框架图如下：
 ![](/images/ember-after.png)

#### 如何转变
对大多数的迁移，我们仅仅将`export default Ember.Controller.extend(...)`改成`export default Ember.Component.extend(...)`即可，下面给出一个例子：
之前：
```javascript
App.AlbumController = Ember.ObjectController.extend({

  needs: ['audio'],

  equalizer: function() {
    var genre = this.get('genre');
    return this.get('controllers.audio').equalizerFor(genre);
  }.observes('knob'),

  // ...

});
```

之后：
```html
{{!-- app/template/album.hbs --}}

{{ album-knobs model=model }}
```

和js文件：
```javascript
// app/components/album-knobs.js

export default Ember.Component.extend({

  audio: Ember.inject.service(),

  equalizer() {
    const genre = this.get('model.genre');
    return this.get('audio').equalizerFor(genre);
  }.observes('knob')

});

// app/services/audio.js

export default Ember.Service.extend({
  equalizerFor(genre) {
    // ...
  }
});
```
