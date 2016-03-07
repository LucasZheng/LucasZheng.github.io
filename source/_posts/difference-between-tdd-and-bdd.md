title: TDD与BDD的差别 
description: TDD与BDD的差别
date: 2016-03-07 22:52:13
categories:
- test

tags: [unit test]
---
如果你了解最新的软件开发实践，那么你就会经常听到`unit test`、`TDD`和`BDD`这些词汇。很多Javascript开发者对这些术语都不是很清楚，这篇文章将解释它们分别是什么，以及它们之间的区别在哪里。<!-- more -->
#### Unit testing（单元测试）
单元测试往往只关注于一个代码片段，通常是一个模块或函数，测试代码应该比较简单，可以快速实现和运行。这也意味着你会写很多的单元测试用例来保证代码的功能正常。单元测试应该没有什么依赖，比如通过网络请求获取数据库数据。现在有很多工具都可以模拟Api的响应数据来运行测试文件。单元测试的基本原则就是：多个测试用例，每个测试不同的功能并且相互之间没有依赖。也有一部分人认为任何的自动化测试都是单元测试，这是不正确的，这里有不同类型的自动化测试，每种类型的功能都不一样。
+ Unit tests：单一的代码片段测试（通常是一个函数），测试用例之间相互独立
+ Integration tests：同时关联多个测试用例，比如通过测试数据库测试数据的获取
+ Acceptance tests（also called Functional tests）：测试整个应用

#### TDD（测试驱动开发）
`TDD`就是用测试来驱动软件开发，它有如下五个步骤：
+ 开发者首先写一些测试
+ 开发者运行这些测试，但这些测试明显都会失败，因为你并没有实现代码细节
+ 开发者实现代码细节
+ 如果开发者顺利实现代码的话，运行所有测试就会通过
+ 开发者可以重构代码，如果新代码功能不正确的话，对应的测试文件也会失败

开发者可以基于这种流程实现更多的需求，如下面的流程图：
![](/images/tdd-flowchart.png)

**示例**

假如开发者写了一个函数来计算阶乘，那么常规`TDD`模式就是使用这个函数，输入一个特定值判断输出是否正确。这个例子中使用[Mocha](http://mochajs.org/)作为测试框架：
```javascript
var assert = require('assert'),
    factorial = require('../index');

suite('Test', function (){
  setup(function (){
    // Create any objects that we might need
  });

  suite('#factorial()', function (){
    test('equals 1 for sets of zero length', function (){
      assert.equal(1, factorial(0));
    });

    test('equals 1 for sets of length one', function (){
      assert.equal(1, factorial(1));
    });

    test('equals 2 for sets of length two', function (){
      assert.equal(2, factorial(2));
    });

    test('equals 6 for sets of length three', function (){
      assert.equal(6, factorial(3));
    });
  });
});
```

在实现代码前，这个测试肯定会失败的，我们现在实现这个函数：
```javascript
module.exports = function (n) {
  if (n < 0) return NaN;
  if (n === 0) return 1;

  return n * factorial(n - 1);
};
```

现在运行这个测试文件就会全部通过，这就是`TDD`开发模式，现在我们来看下`BDD`有什么不同的地方。

#### BDD（行为驱动开发）
很多人都认为`BDD`跟`TDD`很接近，跟`TDD`相比，`BDD`就是基于我们写的需求行为和文档来驱动开发，这些文档描述跟我们的测试代码很相像。

**示例**

```javascript
var assert = require('assert'),
    factorial = require('../index');

describe('Test', function (){
  before(function(){
    // Stuff to do before the tests, like imports, what not
  });

  describe('#factorial()', function (){
    it('should return 1 when given 0', function (){
      factorial(0).should.equal(1);
    });

    it('should return 1 when given 1', function (){
      factorial(1).should.equal(1);
    });

    it('should return 2 when given 2', function (){
      factorial(2).should.equal(2);
    });

    it('should return 6 when given 3', function (){
      factorial(3).should.equal(6);
    });
  });

  after(function () {
    // Anything after the tests have finished
  });
});
```

主要区别就是在于测试的描述上，`BDD`使用一种更通俗易懂的文字来描述测试用例。尽管上面的例子很简单，但从中我们可以看出`BDD`更关注需求的功能，而不是实际结果，`BDD`相对于`TDD`而言更有利于帮助我们设计软件开发。

#### TDD 与 BDD

那到底是使用`TDD`或者`BDD`好呢？它取决于你所使用语言是否有一种合适的测试框架，你的同事们更倾向于使用哪种方式。

