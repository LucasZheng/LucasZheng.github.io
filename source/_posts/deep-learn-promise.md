title: 深入了解promise
description: 开发过程中常见的promise问题
date: 2015-05-25 20:27:46

categories: 
- javascript

tags:
- [javascript, promise]

---
Promise对很多有一定js开发经验的developer来说并不陌生，它解决了js中callback hell的问题，让我们的js代码更优雅、更容易阅读和维护。<!-- more -->虽然现实中developer在一定程度上会使用promise，但他们并没有真正的了解它的工作原理。如下面的代码，你能说出它们之间的差别吗？

{% codeblock lang:javascript %}
doSomething().then(function () {
  return doSomethingElse();
});

doSomething().then(function () {
  doSomethingElse();
});

doSomething().then(doSomethingElse());

doSomething().then(doSomethingElse);
{% endcodeblock %}

如果你知道答案，那么恭喜你，你不用继续阅读下面的内容了。  
#### 三角形的代码模式  
{% codeblock lang:javascript %}
remotedb.allDocs({
  include_docs: true,
  attachments: true
}).then(function (result) {
  var docs = result.rows;
  docs.forEach(function(element) {
    localdb.put(element.doc).then(function(response) {
      alert("Pulled doc with id " + element.doc._id + " and added to local db.");
    }).catch(function (err) {
      if (err.status == 409) {
        localdb.get(element.doc._id).then(function (resp) {
          localdb.remove(resp._id, resp._rev).then(function (resp) {
// et cetera...
{% endcodeblock %}

更好的一种实现方式：  
{% codeblock lang:javascript %}
remotedb.allDocs(...).then(function (resultOfAllDocs) {
  return localdb.put(...);
}).then(function (resultOfPut) {
  return localdb.get(...);
}).then(function (resultOfGet) {
  return localdb.put(...);
}).catch(function (err) {
  console.log(err);
});
{% endcodeblock %}

这是一种比较好的实现方式，每个function只会在前一个promise `resolve`时触发，并且可以获取前一个promise的输出作为参数。  
#### 如何在forEach中使用promise  
这种情况也比较常见，当developer使用他们常见的forEach、for或while时，他们不清楚如何将promise融合进来使用，于是他们经常这样写：  
{% codeblock lang:javascript %}
// I want to remove() all docs
db.allDocs({include_docs: true}).then(function (result) {
  result.rows.forEach(function (row) {
    db.remove(row.doc);  
  });
}).then(function () {
  // I naively believe all docs have been removed() now!
});
{% endcodeblock %}

这种写法的问题在于forEach中的第一个function返回是`undefined`，第二个function并不会等待`db.remove`，实际上它并不会等待，它可以在任何时候执行。这时你可以使用`Promise.all`来解决这个问题：  
{% codeblock lang:javascript %}
db.allDocs({include_docs: true}).then(function (result) {
  return Promise.all(result.rows.map(function (row) {
    return db.remove(row.doc);
  }));
}).then(function (arrayOfResults) {
  // All docs have really been removed() now!
});
{% endcodeblock %}

`Promise.all`使用`array`格式的promise作为输入，并且只有在包含的所有promise都`resolve`时才返回另外一个promise。  
`Promise.all`同样传递一个`array`格式的结果给下一个function。如果其中一个promise被`reject`，那么`all()`同样也会被`reject`。  
#### 忘记使用`.catch()`  
这是另外一种常见的错误。developer通常认为他们的promise从来不会抛出错误，所以许多人在代码中忘记使用`.catch()`，这意味着抛出的错误将会被吞噬掉，你在console中也不会看到任何报错信息，这非常不利于debug代码。  
为了避免这种情况，我们应该养成习惯在promise链中添加如下代码：  
{% codeblock lang:javascript %}
somePromise().then(function () {
  return anotherPromise();
}).then(function () {
  return yetAnotherPromise();
}).catch(console.log.bind(console)); // <-- this is badass
{% endcodeblock %}

#### 使用`deferred`  
Promise的发展经历了一段比较长的时间，在早些时候，`jQuery`和`Angular`曾经使用`deferred`模式，现在已经被ES6的规范所取代，或者被一些库所实现，如：`Q`，`When`，`RSVP`, `Bluebird`，`Lie`等。  
许多情况下，你可以使用`import`来引入第三方promise库，比如Angular中的`$q`允许你使用`$q.when()`来包裹非`$q`promise的代码：  
{% codeblock lang:javascript %}
$q.when(db.put(doc)).then(/* ... */); // <-- this is all the code you need
{% endcodeblock %}

同样也可以使用如下的模式：  
{% codeblock lang:javascript %}
new Promise(function (resolve, reject) {
  fs.readFile('myfile.txt', function (err, file) {
    if (err) {
      return reject(err);
    }
    resolve(file);
  });
}).then(/* ... */)
{% endcodeblock %}

#### 忘记使用`return`  
看看下面的代码：  
{% codeblock lang:javascript %}
somePromise().then(function () {
  someOtherPromise();
}).then(function () {
  // Gee, I hope someOtherPromise() has resolved!
  // Spoiler alert: it hasn't.
});
{% endcodeblock %}

promise的魅力在于它可以返回我们前一个的`return`和`throw`。每一个promise给你提供了`then()`方法（或者`catch()`，等同于`then(null, ...)`）。  
{% codeblock lang:javascript %}
somePromise().then(function () {
  // I'm inside a then() function!
});
{% endcodeblock %}

在上面的代码中我们可以做如下三件事情： 
1、`return`另外一个promise  
2、`return`一个值或者`undefined`  
3、`throw`一个错误  
##### 返回另外一个promise
这在promise结构中是一种比较常见的模式：
{% codeblock lang:javascript %}
getUserByName('nolan').then(function (user) {
  return getUserAccountById(user.id);
}).then(function (userAccount) {
  // I got a user account!
});
{% endcodeblock %}
这里`return`非常关键，如果没有使用return，则下一个function将会获取到`undefined`而不是`userAccount`。  
##### 返回一个值或`undefined`  
{% codeblock lang:javascript %}
getUserByName('nolan').then(function (user) {
  if (inMemoryCache[user.id]) {
    return inMemoryCache[user.id];    // returning a synchronous value!
  }
  return getUserAccountById(user.id); // returning a promise!
}).then(function (userAccount) {
  // I got a user account!
});
{% endcodeblock %}

第二个function并不会关心`userAccount`是同步获取的还是异步获取的。在javascript中如果一个function没有return，则默认返回是`undefined`，但最好是在`then()`function中始终使用`return`或`throw`。  
##### 抛出一个错误  
比如当用户没有登录时，我们抛出一个错误：
{% codeblock lang:javascript %}
getUserByName('nolan').then(function (user) {
  if (user.isLoggedOut()) {
    throw new Error('user logged out!'); // throwing a synchronous error!
  }
  if (inMemoryCache[user.id]) {
    return inMemoryCache[user.id];       // returning a synchronous value!
  }
  return getUserAccountById(user.id);    // returning a promise!
}).then(function (userAccount) {
  // I got a user account!
}).catch(function (err) {
  // Boo, I got an error!
});
{% endcodeblock %}

如果其中一个promise被`reject`了，则`.catch`将会获取到错误信息。