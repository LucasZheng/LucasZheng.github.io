title: ES6中新的正则表达式规则 
description: ES6中对正则表达式新增加了几个有用的特性
date: 2015-08-05 20:27:46

categories: 
- javascript

tags: [javascript, ES6, RegExp]

---
这篇文章将详细了解下ES6中新增加的正则表达式规则，如果你对ES5中的正则表达式比较熟悉的话，那么有助于理解文章所将的内容。如果你对正则表达式还是不熟悉的话，可以参考下面的链接文章：<!-- more -->
+ [Regular Expressions](http://speakingjs.com/es5/ch19.html)
+ [Unicode and Javascript](http://speakingjs.com/es5/ch24.html)

#### 概述
下面是ES6中新增加的正则表达式特性：
+ 新的flag `/y`，基于前一个匹配规则结尾的锚点
+ 新的flay `/u`，用来处理unicode代码，如`\uD83D\uDE80`，并且允许你在正则表达式中使用诸如`\u{1F680}`之类的规则。
+ 新的属性`flags`，可以让你获取到正则表达式的flags，就如ES5中`source`属性可以获取到正则表达式的pattern一样。

```javascript
> /abc/ig.source // ES5
  'abc'
> /abc/ig.flags // ES6
  'gi'
```

你也可以使用`RegExp()`的构造函数获取一个复杂的正则表达式：
```javascript
> new RegExp(/abc/ig).flags
  'gi'
> new RegExp(/abc/ig, 'i').flags // change flags
  'i'
```

#### 新flag `/y` (sticky)
新flag `/y`在正则表达式`re`处理字符串匹配时要注意两点：
+ `re.lastIndex`锚点：匹配必须基于`re.lastIndex`开始（上次匹配的index）。这个行为和`^`比较相似，但是它始终是从0开始匹配。
+ 重复匹配：如果一个匹配成功，`re.lastIndex`将会被设置成匹配成功后的那个index。这和`/g` flag类似，`/g`通常也是用来进行多次匹配的。
下面的这个function实现了`RegExp.prototype.exec()`中的`/y` flag。
```javascript
function execSticky(regex, str) {
  // Anchor the regex to the beginning of the string
  let matchSource = regex.source;
  if (!matchSource.startsWith('^')) {
    matchSource = '^' + matchSource;
  }
  // Ensure that instance property `lastIndex` is updated
  let matchFlags = regex.flags; // ES6 feature!
  if (!regex.global) {
    matchFlags = matchFlags + 'g';
  }
  let matchRegex = new RegExp(matchSource, matchFlags);
  
  // Ensure we start matching `str` at `regex.lastIndex`
  const matchOffset = regex.lastIndex;
  const matchStr = str.slice(matchOffset);
  let match = matchRegex.exec(matchStr);
  
  // Translate indices from `matchStr` to `str`
  regex.lastIndex = matchRegex.lastIndex + matchOffset;
  match.index = match.index + matchOffset;
  return match;
}
```

下面这个表格会显示出各种正则表达式操作如何响应`/y` flag。（`re`是触发匹配的正则表达式）

|| Flags | Start matching | Anchored to | Result if match | No match | re.lastIndex |
|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| exec() | - | 0 | - | Match object | null | unchanged |
|| /g | re.lastIndex | - | Match objec | null | index after match |
|| /y | re.lastIndex | re.lastIndex | Match object | null | index after match |
|| /gy | re.lastIndex | re.lastIndex | Match object | null | index after match |
| test() | (Any) | (like exec()) | (like exec()) | true | false | (like exec()) |

字符串上面的一个匹配规则：（`str`是字符串，`re`是正则表达式）

|| Flags | Start matching | Anchored to | Result if match | No match | re.lastIndex |
|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| search() | -,/g | 0 | - | Index of match | -1 | unchanged |
|| /y, /gy | 0 | 0 | Index of match | -1 | unchanged |
| match() | - | 0 | - | Match object | null | unchanged |
|| /y | re.lastIndex | re.lastIndex | Match object | null | index after match |
|| /g | After prev.match(loop) | - | Array with matches | null | 0 |
|| /gy | After prev.match(loop) | After prev.match | Array with matches | null | 0 |
| split() | -, /g | After prev.match (loop) | - | Array with strings between matches | [str] | unchanged |
|| /y, /gy | After prev.match (loop) | After prev.match | Array with empty strings between matches | [str] | unchanged |
| replace() | - | 0 | - | First match replaced | No repl. | unchanged |
|| /y | 0 | 0 | First match replaced | No repl. | unchanged |
|| /g | After prev.match (loop) | - | All matches replaced | No repl. | unchanged |
|| /gy | After prev.match (loop) | After prev.match | All matches replaced | No repl. | unchanged |

##### RegExp.prototype.exec(str)
如果`/g`没有设置，匹配始终重最开始位置进行匹配，直到找到匹配结果位置。REGEX.lastIndex不会发生变化。
```javascript
const REGEX = /a/;
  
REGEX.lastIndex = 7; // ignored
const match = REGEX.exec('xaxa');
console.log(match.index); // 1
console.log(REGEX.lastIndex); // 7 (unchanged)
```
如果设置了`/g`，匹配就会从REGEX.lastIndex开始，直到找到匹配结果为止。REGEX.lastIndex将会被设置为找到匹配结果后的那个index，这也意味着你会得到所有的匹配结果直到exec()返回null。
```javascript
const REGEX = /a/g;
  
REGEX.lastIndex = 2;
const match = REGEX.exec('xaxa');
console.log(match.index); // 3
console.log(REGEX.lastIndex); // 4 (updated)
  
// No match at index 4 or later
console.log(REGEX.exec('xaxa')); // null
```
如果仅仅设置了`/y`，匹配将会从REGEX.lastIndex开始，并且在该点设置一个锚点（不会跳过开头直到找到匹配为止。）REGEX.lastIndex的改变和/g类似。
```javascript
const REGEX = /a/y;
   
// No match at index 2
REGEX.lastIndex = 2;
console.log(REGEX.exec('xaxa')); // null
   
// Match at index 3
REGEX.lastIndex = 3;
const match = REGEX.exec('xaxa');
console.log(match.index); // 3
console.log(REGEX.lastIndex); // 4
```
如果同时设置了/y和/g的话，其效果和/y一致。
##### RegExp.prototype.test(str)
test()和exec()的工作相似，但是它只返回true或false。
```javascript
const REGEX = /a/y;
   
REGEX.lastIndex = 2;
console.log(REGEX.test('xaxa')); // false
   
REGEX.lastIndex = 3;
console.log(REGEX.test('xaxa')); // true
console.log(REGEX.lastIndex); // 4
```
##### String.prototype.search(regex)
search()会忽略`/g`标识和lastIndex(一直不会发生变化)。从字符串开头进行匹配，匹配成功后就返回它的index（如果没有匹配成功就返回-1）:
```javascript
const REGEX = /a/;
   
REGEX.lastIndex = 2; // ignored
console.log('xaxa'.search(REGEX)); // 1
```
如果你设置了`/y`标识，lastIndex同样会被忽略，但是正则表达式匹配的锚点被设置从0开始。
```javascript
const REGEX = /a/y;
   
REGEX.lastIndex = 1; // ignored
console.log('xaxa'.search(REGEX)); // -1 (no match)
```
##### String.prototype.match(regex)
match()有两种模式：
+ 如果没有设置`/g`，它就工作原理就和exec()类似
+ 如果设置了`/g`，匹配成功后就会返回一个字符串数组，匹配不成功就返回null
如果没有设置`/g`标识，match()匹配就像exec()方法：
```javascript
{
  const REGEX = /a/;
    
  REGEX.lastIndex = 7; // ignored
  console.log('xaxa'.match(REGEX).index); // 1
  console.log(REGEX.lastIndex); // 7 (unchanged)
}
{
  const REGEX = /a/y;
    
  REGEX.lastIndex = 2;
  console.log('xaxa'.match(REGEX)); // null
    
  REGEX.lastIndex = 3;
  console.log('xaxa'.match(REGEX).index); // 3
  console.log(REGEX.lastIndex); // 4
}
```
如果match()仅仅设置了`/g`，它会返回所有匹配成功的字符串数组，匹配不成功就返回null。匹配始终是从位置0开始。
```javascript
const REGEX = /a|b/g;
REGEX.lastIndex = 7;
console.log('xaxb'.match(REGEX)); // ['a', 'b']
console.log(REGEX.lastIndex); // 0
```
如果你额外的增加了`/y`标识，匹配仍然是进行全局重复多次匹配，匹配的锚点将会从上次匹配成功后的那个index开始或者从0开始。
```javascript
const REGEX = /a|b/gy;
    
REGEX.lastIndex = 0; // ignored
console.log('xab'.match(REGEX)); // null
REGEX.lastIndex = 1; // ignored
console.log('xab'.match(REGEX)); // null
    
console.log('ab'.match(REGEX)); // ['a', 'b']
console.log('axb'.match(REGEX)); // ['a']
```
##### String.prototype.split(separator, limit)
关于split()的详细解释可以参考[speakingjs](http://speakingjs.com/es5/ch19.html#String.prototype.match)。
对于ES6的`/y`, 字符串必须以分隔符开头：
```javascript
'x##'.split(/#/y) // no match
[ 'x##' ]
'##x'.split(/#/y) // 2 matches
[ '', '', 'x' ]
'#x#'.split(/#/y) // 1 match
[ '', 'x#' ]
'##'.split(/#/y) // 2 matches
[ '', '', '' ]
```
同样，你也可以进行分组：
```javascript
'##'.split(/(#)/y)
[ '', '#', '', '#', '' ]
```
##### String.prototype.replace(search, replacement)
如果没有设置`/g`标识，replace()仅仅替换找到的第一个匹配结果：
```javascript
const REGEX = /a/;

// One match
console.log('xaxa'.replace(REGEX, '-')); // 'x-xa'
```
如果仅仅设置了`/y`，同样你也只会得到最多一个匹配结果，但是那个匹配结果始终是从字符串开头匹配的。lastIndex将会被忽略而且不会发生改变。
```javascript
const REGEX = /a/y;

// Anchored to beginning of string, no match
REGEX.lastIndex = 1; // ignored
console.log('xaxa'.replace(REGEX, '-')); // 'xaxa'
console.log(REGEX.lastIndex); // 1 (unchanged)

// One match
console.log('axa'.replace(REGEX, '-')); // '-xa'
```
如果设置了`/g`, 将会对字符串进行全局匹配替换：
```javascript
const REGEX = /a/g;

// Multiple matches
console.log('xaxa'.replace(REGEX, '-')); // 'x-x-'
```
如果设置了`/gy`, replace()将会替换所有匹配结果，但是每次匹配的锚点是基于上次匹配成功后的那个index：
```javascript
const REGEX = /a/gy;

// Multiple matches
console.log('aaxa'.replace(REGEX, '-')); // '--xa'
```
replacement参数同样可以是一个function, 请参考[speakingjs](http://speakingjs.com/es5/ch19.html#String.prototype.replace)
##### 例如：使用粘性匹配进行标记化
粘性匹配主要用来进行标记化，把文本转换为标记序列。关于标记化的一个重要特点是，标记是文本中的一部分，并且它们之间没有空隙。因此很适合用粘性匹配进行操作。
```javascript
function tokenize(TOKEN_REGEX, str) {
  let result = [];
  let match;
  while (match = TOKEN_REGEX.exec(str)) {
    result.push(match[1]);
  }
  return result;
}
    
const TOKEN_GY = /\s*(\+|[0-9]+)\s*/gy;
const TOKEN_G  = /\s*(\+|[0-9]+)\s*/g;
```
在一个合法的标记序列中，粘性匹配和非粘性匹配会得到同样的结果：
```javascript
tokenize(TOKEN_GY, '3 + 4')
[ '3', '+', '4' ]
tokenize(TOKEN_G, '3 + 4')
[ '3', '+', '4' ]
```
但是，如果字符串中有非标记文本，则粘性匹配会停止工作，而非粘性匹配会忽略这个非标记字符：
```javascript
tokenize(TOKEN_GY, '3x + 4')
[ '3' ]
tokenize(TOKEN_G, '3x + 4')
[ '3', '+', '4' ]
```
##### 例如：手动实现粘性匹配
如果你想自己实现粘性匹配，你可以按如下操作：execStricky()和在粘性模式下工作的RexExp.prototype.exec()工作类似：
```javascript
function execSticky(regex, str) {
  // Anchor the regex to the beginning of the string
  let matchSource = regex.source;
  if (!matchSource.startsWith('^')) {
    matchSource = '^' + matchSource;
  }
  // Ensure that instance property `lastIndex` is updated
  let matchFlags = regex.flags; // ES6 feature!
  if (!regex.global) {
    matchFlags = matchFlags + 'g';
  }
  let matchRegex = new RegExp(matchSource, matchFlags);
    
  // Ensure we start matching `str` at `regex.lastIndex`
  const matchOffset = regex.lastIndex;
  const matchStr = str.slice(matchOffset);
  let match = matchRegex.exec(matchStr);
    
  // Translate indices from `matchStr` to `str`
  regex.lastIndex = matchRegex.lastIndex + matchOffset;
  match.index = match.index + matchOffset;
  return match;
}
```
#### 新标识`/u`(unicode)
新标识`/u`允许在正则表达式中使用Unicode模式。主要有两个特点：
+ 你可以使用Unicode来指定某个字符，如\u{1F42A}。正常的Unicode转义如\u03B1仅仅只有4位16进制数字。
+ 正则表达式和字符串中的字符都是基于代码点（非UTF-16代码单元）。代码单元将会被转换成代码点。
后者在转义序列中将包含更多的信息。下面我将会用两个UTF-16代码单元(如：\uD83D\uDE80)而非代码点（如：\u{1F680}）来更清楚的表明，Unicode模式中的分组以及它同时在Unicode模式和非Unicode模式中生效。
```javascript
'\u{1F680}' === '\uD83D\uDE80' // code point vs. surrogate pairs
true
```
##### 总结：正则表达式中的独立的surrogates仅仅匹配独立的surrogates
在非Unicode模式下，正则表达式中独立的surrogate可以匹配代码点中的surrogate:
```javascript
/\uD83D/.test('\uD83D\uDC2A')
true
``` 
而在Unicode模式下，surrogate pairs将会变成原子单位，独立的surrogates不会匹配代码点中连接的surrogate:
```javascript
/\uD83D/u.test('\uD83D\uDC2A')
false
```
实际上独立的surrogate仍然是可以匹配的：
```javascript
/\uD83D/u.test('\uD83D \uD83D\uDC2A')
true
/\uD83D/u.test('\uD83D\uDC2A \uD83D')
true
```
##### 总结：你可以将代码点放在字符类中
在Unicode模式下，你可以将代码点放进字符类中，它们不会被当作两个字符来对待。
```javascript
/^[\uD83D\uDC2A]$/u.test('\uD83D\uDC2A')
true
/^[\uD83D\uDC2A]$/.test('\uD83D\uDC2A')
false
/^[\uD83D\uDC2A]$/u.test('\uD83D')
false
/^[\uD83D\uDC2A]$/.test('\uD83D')
true
```
##### 总结：点操作符号（.）只对代码点有效，而对代码单元无效
在Unicode模式下，点操作符对代码点生效（一到两个代码单元）。在非Unicode模式下，它仅仅匹配单个的代码单元。比如：
```javascript
'\uD83D\uDE80'.match(/./gu).length
1
'\uD83D\uDE80'.match(/./g).length
2
```
##### 总结：数字仅对代码点而非代码单元有效
在Unicode模式下，数字仅对代码点（一到两个代码单元）生效。在非Unicode模式下，它们仅对单个代码单元生效。比如：
```javascript
/\uD83D\uDE80{2}/u.test('\uD83D\uDE80\uD83D\uDE80')
true
/\uD83D\uDE80{2}/.test('\uD83D\uDE80\uD83D\uDE80')
false
/\uD83D\uDE80{2}/.test('\uD83D\uDE80\uDE80')
true
```
#### 新的属性标识
在ES6中，正则表达式有如下一些属性：
+ `source`
+ `flags`
+ `global`，`ignoreCase`，`multiline`，`sticky`，`unicode`
+ `lastIndex`
而`lastIndex`是现在唯一的一个实例属性，其他的属性标识可以通过内部实例属性的一些getter方法获取到，如[get RegExp.prototype.global](http://www.ecma-international.org/ecma-262/6.0/#sec-get-regexp.prototype.global)。
`source`属性已经在ES5中得到支持，如：
```javascript
/abc/ig.source
'abc'
```
`flags`是一个新的属性，它返回一个字符串的flags：
```javascript
/abc/ig.flags
'gi'
```
你不能改变现有正则表达式中的flags，但是可以通过flags复制一个新的改变的flags：
```javascript
function copyWithIgnoreCase(regex) {
  return new RegExp(regex.source, regex.flags+'i');
}
```
#### 使用RegExp()生成一个新的正则表达式
在ES6中，RegExp()有两种方式：
+ new RegExp(pattern : string, flags = '')
+ new RegExp(regex : RegExp, flags = regex.flags)
```javascript
new RegExp(/abc/ig).flags
'gi'
new RegExp(/abc/ig, 'i').flags // change flags
'i'
```
因此，RegExp构造函数可以让我们以另外一个方式来改变flags：
```javascript
function copyWithIgnoreCase(regex) {
  return new RegExp(regex, regex.flags+'i');
}
```
#### javascript中的转义序列
在javascript中有三种转义类型：
+ 十六进制转义（特别是两位十六进制）：`\xHH`
```javascript
'\x7A' === 'z'
true
```
+ Unicode转义（特别是4位十六进制）：`\uHHHH`
```javascript
'\u007A' === 'z'
true
```
+ Unicode代码点转义（一到两个十六进制）：`\u{...}`
```javascript
'\u{7A}' === 'z'
true
```
Unicode代码点转义是ES6中新增加的。
转义序列可以在以下地方使用：

|| \uHHHH | \u{...} | \xHH |
| Identifiers | true | true ||
| String literals | true | true | true |
| Template literals | true | true | true |
| Regular expression literals | true | Only with flag /u | true |

修饰符：
+ 一个4位的Unicode转义\uHHHH成为一个独立的代码点
+ 一个Unicode代码点转义\u{...}变成一个独立的代码点
```javascript
let hello = 123;
hell\u{6F}
123
```
字符串：
+ 字符串在内部是以UTF-16代码单元存储的
+ 一个十六进制转义\xHH就成为一个UTF-16代码单元
+ 一个4位的Unicode转义\uHHHH成为一个UTF-16代码单元
+ 一个Unicode代码点转义\u{...}成为UTF-16代码点（一到两个UTF-16代码单元）
模版字符串：
+ 在模版字符串中，转义处理就和字符串处理类似
+ 在标签模版中，转义序列如何处理取决于标签函数。由两个因素决定：
  + Cooked: 转义序列处理和字符串类似
  + Raw: 转义序列处理和字符类似
```javascript
`hell\u{6F}` // cooked
'hello'
String.raw`hell\u{6F}` // raw
'hell\\u{6F}'
```
正则表达式：
+ Unicode代码点转义仅仅允许设置`/u`标识，因为\u{3}仅仅被解释成有3个u字符：
```javascript
/^\u{3}$/.test('uuu')
true
```
##### ES6中关于转义序列的相关文档
+ 将source code认为是Unicode代码点序列[Source Text](http://www.ecma-international.org/ecma-262/6.0/#sec-source-text)
+ 修饰符中的Unicode转义序列[Names and Keywords](http://www.ecma-international.org/ecma-262/6.0/#sec-names-and-keywords)
+ 字符串在内部是以UTF-16代码单元存储[String Literals](http://www.ecma-international.org/ecma-262/6.0/#sec-literals-string-literals)
+ 字符串中的各种转义序列是如何被转换成UTF-16代码单元的：[Static Semantics: SV](http://www.ecma-international.org/ecma-262/6.0/#sec-static-semantics-sv)
+ 模版中的各种转义是如何被转换成UTF-16代码单元的：[Static Semantics: TV and TRV](http://www.ecma-international.org/ecma-262/6.0/#sec-static-semantics-tv-and-trv)
正则表达式：
[Pattern Semantics](http://www.ecma-international.org/ecma-262/6.0/#sec-pattern-semantics) 文档解释了BMP模式（没有`\u`标识）和Unicode模式的差别：
+ 如果文档规则中R有一个[U]，那么就意味着它有两个版本：R和R_U
+ 部分规则中允许通过[?U]这样设置
+ 如果规则中有前缀[+U]，那么它仅会在[U]存在的情况下生效
+ 如果规则中有前缀[~U]，那么它仅会在[U]不存在的情况下生效
你可以通过[Patterns](http://www.ecma-international.org/ecma-262/6.0/#sec-patterns)来了解更多的信息。[U]在BMP和Unicode模式下有两种不同的语法：
+ IdentityEscape: 在BMP模式中，许多字符前面有一个反斜杠，被解释成这个字符本身（比如：如果\u后面没有4位十六进制，那么它会被识别成u）。在Unicode模式下，它仅对如下的符号生效：^ $ \ . * + ? ( ) [ ] { } |
+ RegExpUnicodeEscapeSequence: \u{"xxxx"} 只能在Unicode模式下存在。
参考[CharacterEscape](http://www.ecma-international.org/ecma-262/6.0/#sec-characterescape)解释各种转义序列是如何被转换成字符。
#### 字符串中正则表达式运用了正则表达式的方法
下面字符串中一些方法实际上是运用了正则表达式的一些方法：
+ String.prototype.match calls RegExp.prototype[Symbol.match]
+ String.prototype.replace calls RegExp.prototype[Symbol.replace]
+ String.prototype.search calls RegExp.prototype[Symbol.search]
+ String.prototype.split calls RegExp.prototype[Symbol.split]
#### 支持情况
可以参考下面的链接来获得新特性的支持情况：
+ [RegExp flags /y and /u](https://kangax.github.io/compat-table/es6/#RegExp_y_and_u_flags)
+ [New property flags](https://kangax.github.io/compat-table/es6/#RegExp.prototype_properties)
#### 更多详细信息
如果你想了解更多关于/u是如何工作的，可以参考这篇文章：[Unicode-aware regular expressions in ECMAScript 6](https://mathiasbynens.be/notes/es6-unicode-regex)。
