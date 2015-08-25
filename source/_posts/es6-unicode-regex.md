title: ES6中的Unicode正则表达模式
description: ES6中正则表达式新添加的Unicode匹配模式
date: 2015-08-25 20:27:46

categories:
- javascript

tags: [javascript, ES6, RegExp]

---
ES6中正则表达式引入了两个新的feature：<!-- more -->
+ `y`开启[粘性匹配](https://docs.webplatform.org/wiki/javascript/RegExp/sticky)
+ `u`开启各种Unicode相关特性

这篇文章将说明使用`u` flag所带来的影响。
#### 语法上的改变
在正则表达式中使用`u` flag 将开启ES6中的Unicode转义模式`\u{...}`.

```javascript
// Note: `a` is U+0061 LATIN SMALL LETTER A, a BMP symbol.
console.log(/\u{61}/u.test('a'));
// → true

// Note: `𝌆` is U+1D306 TETRAGRAM FOR CENTRE, an astral symbol.
console.log(/\u{1D306}/u.test('𝌆'));
// → true
```

如果没有这个标识，比如像`\u{1234}`，这也是一个合法的正则表达式，但它不会被当作Unicode转义。`/\u{1234}/`就相当于`/u{1234}/`，会匹配有1234个连续的`u`字符。  
但是如果设置了`\u`，比如像`\a`（`a`是一个非转义字符）就不会再被当作`a`解释。即使`/\a/`会被当作`/a`，而`/\a/u`则会抛出一个错误，因为`\a`不是一个保留的转义序列。

#### 对`.`操作符的影响
如果没有设置`u`标识符，`.`将会匹配除折行外的任何BMP符号。当设置了ES6中的`u`，`.`同时也会匹配图形符号。
```javascript
// Note: `𝌆` is U+1D306 TETRAGRAM FOR CENTRE, an astral symbol.
var string = 'a𝌆b';

console.log(/a.b/.test(string));
// → false

console.log(/a.b/u.test(string));
// → true

var match = string.match(/a(.)b/u);
console.log(match[1]);
// → '𝌆'
```

#### 对匹配数字的影响
在javascript的正则表达式中数字匹配符号一般有：`*`，`+`，`{2}`，`{2,}`，`{2,4}`之类的写法。如果没有`u`标识，紧跟着图形符号后面的数字就只会匹配图形转义的低位符号。
```javascript
// Note: `a` is a BMP symbol.
console.log(/a{2}/.test('aa'));
// → true

// Note: `𝌆` is an astral symbol.
console.log(/𝌆{2}/.test('𝌆𝌆'));
// → false

// Explanation: the previous example is equivalent to the following.
console.log(/\uD834\uDF06{2}/.test('\uD834\uDF06\uD834\uDF06'));
// → false

console.log(/\uD834\uDF06{2}/.test('\uD834\uDF06\uDF06\uD834'));
// → true
```
设置ES6中的`u`标识后，数字将会匹配整个符号，包括图形符号。
```javascript
// Note: `a` is a BMP symbol.
console.log(/a{2}/u.test('aa'));
// → true

// Note: `𝌆` is an astral symbol.
console.log(/𝌆{2}/u.test('𝌆𝌆'));
// → true
```
#### 对字符集的影响
如果没有设置`u`标识，任何给定的字符集仅仅匹配BMP字符，如：
```javascript
var regex = /^[bcd]$/;
console.log(
  regex.test('a'), // false
  regex.test('b'), // true
  regex.test('c'), // true
  regex.test('d'), // true
  regex.test('e')  // false
);
```
但是，当字符集中有图形符号时，javascript就会把它当作两个字符来对待。
```javascript
// Note: `𝌆` is an astral symbol.
var regex = /^[bc𝌆]$/;
console.log(
  regex.test('a'), // false
  regex.test('b'), // true
  regex.test('c'), // true
  regex.test('𝌆')  // false
);

// Explanation: the regular expression is equivalent to the following.
// var regex = /^[bc\uD834\uDF06]$/;
```
ES6中的`u`标识可以让字符集中正常匹配图形符号。
```javascript
// Note: `𝌆` is an astral symbol.
var regex = /^[bc𝌆]$/u; // Or, `/^[bc\u{1D306}]$/u`.
console.log(
  regex.test('a'), // false
  regex.test('b'), // true
  regex.test('c'), // true
  regex.test('𝌆')  // true
);
```
重要的是，整个图形字符集可以使用区间来匹配，只要设置了`u`标识。
```javascript
// Match any symbol from U+1F4A9 PILE OF POO to U+1F4AB DIZZY SYMBOL.
var regex = /[💩-💫]/u; // Or, `/[\u{1F4A9}-\u{1F4AB}]/u`.
console.log(
  regex.test('💨'), // false
  regex.test('💩'), // true
  regex.test('💪'), // true
  regex.test('💫'), // true
  regex.test('💬')  // false
);
```
`u`标识同样对非某些字符匹配有影响。比如`/[^a]`就相当于`/[\0-\x60\x62-\uFFFF]`，匹配那些除了`a`之外的任何BMP字符。但是如果设置了`u`标识，`/[^a]/u`就还会匹配除了`a`之外的所有Unicode字符。
```javascript
var regex = /^[^a]$/u;
console.log(
  regex.test('a'), // false
  regex.test('b'), // true
  regex.test('☃'), // true
  regex.test('𝌆'), // true
  regex.test('💩')  // true
);
```
#### 对转义字符的影响
`u`标识同样对现有的特殊转义字符有不同的解释，比如`\D`，`\S`和`\W`。如果没有设置`u`，`\D`，`\S`和`\W`分别匹配那些非`\d`，`\s`和`\w`的字符。
```javascript
var regex = /^\S$/;
console.log(
  regex.test(' '), // false
  regex.test('a'), // true
  // Note: `𝌆` is an astral symbol.
  regex.test('𝌆')  // false
);
```
如果你设置了`u`，`\D`，`\S`和`\W`同样会匹配图形符号。
```javascript
var regex = /^\S$/u;
console.log(
  regex.test(' '), // false
  regex.test('a'), // true
  // Note: `𝌆` is an astral symbol.
  regex.test('𝌆')  // true
);
```
而它们相反的匹配`\d`，`\s`和`\w`则不会受`u`影响。
#### 对`i`标识的影响
```javascript
var es5regex = /[a-z]/i;
var es6regex = /[a-z]/iu;
console.log(
  es5regex.test('s'),      es6regex.test('s'),      // true true
  es5regex.test('S'),      es6regex.test('S'),      // true true
  // Note: U+017F canonicalizes to `S`.
  es5regex.test('\u017F'), es6regex.test('\u017F'), // false true
  // Note: U+212A canonicalizes to `K`.
  es5regex.test('\u212A'), es6regex.test('\u212A')  // false true
);
```
同样表达式也可以如下使用：
```javascript
console.log(
  /\u212A/iu.test('K'), // true
  /\u212A/iu.test('k'), // true
  /\u017F/iu.test('S'), // true
  /\u017F/iu.test('s')  // true
);
```
这中行为在大多数情况下非常有用，但是也有一些例外让人比较迷惑。默认情况下，`\W`将会匹配任何非`\w`匹配的字符，但同时设置了`i`和`u`时，情况就会变的有点复杂：
```javascript
var es5regex = /\W/i;
var es6regex = /\W/iu;
console.log(
  es5regex.test('☃'),     es6regex.test('☃'),      // true true
  es5regex.test('S'),      es6regex.test('S'),      // false true
  es5regex.test('\u017F'), es6regex.test('\u017F'), // true true
  es5regex.test('K'),      es6regex.test('K'),      // false true
  es5regex.test('\u212A'), es6regex.test('\u212A')  // true true
);
```
当`i`和`u`设置后，`\W`就不再是`\w`相反的含义。
+ `/\w/`和`/\w/i`等价于`/[0-9A-Z_a-z]/`
+ `/\w/iu`同样匹配`U+017F`，因为`S`属于U+017F中。
+ `/\W/iu`奇怪的是同样匹配U+017F，`S`，因为`\W`包括U+017F中的符号。
#### 结束
当前，ES6中的`u`特性还没有在主流浏览器的稳定版本中得到实现。一些浏览器的实验版本可以提供支持[IE Tech Preview](http://devchannel.modern.ie/)，在V8中也可以设置`--harmony-unicode-regexps`来支持。
