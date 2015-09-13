title: Promise在javascript单元测试中的应用
description: Promise在javascript单元测试中的应用。
date: 2015-09-13 20:27:46

categories:
- javascript
- test

tags: [javascript, unit test, promise]

---
Promise已经成为javascript标准的一部分，原生的`Promise`已经在所有的主流浏览器中得到实现，包括Chrome，Firefox和Safari。<!-- more -->
尽管在代码中实现异步比较容易，但在单元测试中使用promise却比较麻烦。你需要将断言写在promise的回调函数中，这给测试增加了额外的代码量。
这篇文章将告诉你如何用一种简单有用的方式来实现单元测试中的promise应用。
这里我们将使用`Mocha`作为测试框架，`Chai`作为断言库。
首先我们需要安装这两个工具：
```javascript
npm install mocha chai
```
当你首次在单元测试中使用promise时，可能会像下面这样写：
```javascript
var expect = require('chai').expect;

it('should do something with promises', function(done) {
  //define some data to compare against
  var blah = 'foo';

  //call the function we're testing
  var result = systemUnderTest();

  //assertions
  result.then(function(data) {
    expect(data).to.equal(blah);
    done();
  }, function(error) {
    assert.fail(error);
    done();
  });
});
```
我们需要有一些测试数据，然后调用我们的测试函数，接着是promise。这种代码实现方式显得有点复杂。
对于promise而言，我们有两个处理函数，第一个是成功时处理的函数，我们在里面对返回值进行比较，第二个时处理失败时的函数。我们还需要`done()`函数，因为promise是异步的，我们必须告诉Mocha这是一个异步测试，告诉它什么时候结束。
但为什么我们需要`assert.fail`？这个测试的目的是比较成功时返回的值是否符合预期，如果promise返回失败，那这个测试也应该失败，那就是为什么我们需要一个处理失败的函数。
假设我们去掉处理失败的函数，如下：
```javascript
result.then(function(data) {
  expect(data).to.equal(blah);
  done();
});
```
这时，如果promise返回失败，那么就不会有错误报出，因为这里没有错误处理函数，同样这里的测试也应该失败才对，这也是为什么promise在测试中比较棘手的原因。
#### Mocha和Promise
Mocha已经内置了promise，这也就意味着失败的promise同样也会让当前测试失败。比如：
```javascript
it('should fail the test', function() {
  var p = Promise.reject('this promise will always be rejected');
  
  return p;
});
```
上面的测试返回了一个失败的promise，意味着每次跑这个测试都会失败，我们可以对之前的测试代码进行如下改进：
```javascript
var expect = require('chai').expect;

it('should do something with promises', function() {
  var blah = 'foo';

  var result = systemUnderTest();

  return result.then(function(data) {
    expect(data).to.equal(blah);
  });
});
```
现在我们不需要处理失败的函数，也不需要`done()`回调函数，因为Mocha会自动处理promise，如果promise失败，Mocha也会让当前的测试失败。
#### 使用Chai-as-promised优化测试
如果我们能在promise上直接写断言岂不更好？使用[chai-as-promised](https://github.com/domenic/chai-as-promised)，我们可以做到这点。
首先安装：
```javascript
npm install chai-as-promised
```
实现：
```javascript
var chai = require('chai');
var expect = chai.expect;

var chaiAsPromised = require('chai-as-promised');
chai.use(chaiAsPromised);

it('should do something with promises', function() {
  var blah = 'foo';

  var result = systemUnderTest();

  return expect(result).to.eventually.equal(blah);
});
```
这里我们使用了`eventually`。当我们比较普通的值时，我们可以使用：
```javascript
expect(value).to.equal(something);
```
但是如果是promise，我们就使用`eventually`:
```javascript
return expect(value).to.eventually.equal(something)
```
需要注意的是不要忘记return当前的promise，否则Mocha不知道是否需要处理它。
我们可以让`eventually`结合其他的断言一起使用：
```javascript
//assert promise resolves with a number between 1 and 10
return expect(somePromise).to.eventually.be.within(1, 10);

//assert promise resolves to an array with length 2
return expect(somePromise).to.eventually.have.length(2);
```
#### Promise在测试中其他有用的模式
如果你的promise返回的是一个object，你可以使用`deep.equal`来进行比较：
```javascript
return expect(value).to.eventually.deep.equal(obj)
```
常规的`eaqual`对object来说只会比较引用，如果你返回的object有相同的属性和值，但却和你预期的object不是同一个对象时，测试就会失败。
chai-as-promised有一个有用的helper来比较object：
```javascript
return expect(value).to.eventually.become(obj)
```
##### object某个属性断言
有时你需要对object上某个属性进行比较：
```javascript
var value = systemUnderTest();

return value.then(function(obj) {
  expect(obj.someProp).to.equal('something');
});
```
但是使用chai-as-promised 有另外一种实现方式：
```javascript
var value = systemUnderTest().then(function(obj) {
  return obj.someProp;
});
```
##### 多个promise
如果测试中有多个promise，你可以使用`Promise.all`：
```javascript
return Promise.all([
  expect(value1).to.become('foo'),
  expect(value2).to.become('bar')
]);
```
##### 对多个promise进行比较
如果你需要对多个promise返回值进行比较：
```javascript
return Promise.all([p1, p2]).then(function(values) {
  expect(values[0]).to.equal(values[1]);
});
```
##### 失败断言
有时你需要返回一个失败的promise，这种情况下你可以使用chai-as-promised的`rejected`断言：
```javascript
return expect(value).to.be.rejected;
```
如果你需要promise返回一个特定的错误类型或错误信息，可以使用`rejectedWith`:
```javascript
//require this promise to be rejected with a TypeError
return expect(value).to.be.rejectedWith(TypeError);

//require this promise to be rejected with message 'holy smokes,
Batman!'
return expect(value).to.be.rejectedWith('holy smokes, Batman!');
```
##### 测试勾子
你可以将promise放在测试hooks里面，如`before`，`after`，`beforeEach`，`afterEach`：
```javascript
describe('something', function() {
  before(function() {
    return somethingThatReturnsAPromise();
  });

  beforeEach(function() {
    return somethingElseWithPromises();
  });
});
```

