title: ESLint的使用 
description: ESLint的使用方法及规则
date: 2016-03-01 22:52:13
categories:
- build

tags: [ESLint]
---
在产品开发前，开放团队往往会制定一系列编码规范，在编码时遵循这些规范来避免出现低级Bug，而实用Lint工具和代码风格检测工具，则可以辅助编码规范的执行，有效的控制代码质量。<!-- more -->

之前比较流行的工具有JSHint和JSCS的结合使用。最近React比较流行，而JSHint不支持JSX语法，所以有了ESLint这个工具的出现。

ESLint主要特点有：

+ 默认规则包含所有 JSLint、JSHint 中存在的规则，易迁移；
+ 规则可配置性高：可设置「警告」、「错误」两个 error 等级，或者直接禁用；
+ 包含代码风格检测的规则（可以丢掉 JSCS 了）；
+ 支持插件扩展、自定义规则。

#### 使用ESLint

+ 通过npm安装：`npm install -g eslint`
+ 通过`eslint --init`命令在当前目录下生成`.eslintrc.js`文件。如果没有 package.json 文件的话，需要先创建这个文件，eslint会根据你的安装向导写入对应的插件到 devDependencies 里面。

设置规则：
```json
"rules": {
  "semi": [2, "always"],
  "quotes": [2, "double"]
}
```

规则有3种配置：

+ 0：禁止使用该规则
+ 1：将该规则定义为警告等级（不影响exit code）
+ 2：将该规则定义为错误等级（exit code为1）

如果配置了`"extends": "eslint:recommended"`，则 [ESLint Rules](http://eslint.org/docs/rules/) 页面所有打勾的规则都会被生效。

如果使用了`eslint --fix ***`，则ESLint将会为我们修复这一报错，如将多个semiicolons变为一个semiicolon。

#### 配置ESLint
两种方式来配置ESLint:

+ 在文件中使用js comment方式来添加配置规则
+ 通过配置文件： `.eslingrc.xx` 或在 `package.json` 中添加 `eslintConfig`属性，或者在命令模式下指定配置文件：`eslint -c ~/my-eslint.json file.js`。

配置的内容包括：

+ 环境：代码运行的环境，每个环境预置了一些全局变量。
+ 全局变量：代码所运用到的全局变量
+ 规则：运用哪些规则来检测以及规则的等级。

#### 指定Parser的选项
ESLint默认只支持ES5的语法，但你可以通过指定parser来支持检测ES6和JSX语法。具体是在`.eslintrc.*`文件中使用`parserOptions`属性。可以设置内容如下：

+ `ecmaVersion`：可以设置3，5（默认）和6来代表ECMAScript的版本
+ `sourceType`：可以设置“script”（默认）或"module"如果你的代码采用模块化的方式  
+ `ecmaFeatures`：设置features：
    + `globalReturn`：在全局范围内允许使用`return`语句
    + `impliedStrict`：如果ecmaVersion为5及以上，使用全局的** strict mode **
    + `jsx`：使用JSX语法
    + `experimentalObjectRestSpread`

应用例子：  
```json
{
  "parserOptions": {
      "ecmaVersion": 6,
      "sourceType": "module",
      "ecmaFeatures": {
          "jsx": true
      },
  },
  "rules": {
      "semi": 2
  }
}
```

#### 指定Parser
ESLint默认使用** Espree **作为parser。如果你想指定其他的parser，也可以修改 ** parser **值来生效。比如：
```json
  {
      "parser": "esprima",
      "rules": {
          "semi": 2
      }
  }
```

目前只有 ** Esprima ** 和 ** Babel-ESLint ** 两个parser兼容ESLint。

#### 指定环境
不同的环境预定义了一些全局变量，环境有如下选项：

+ `browser`
+ `node`
+ `commonjs`
+ `shared-node-browser`：Node和Browser有相同的变量
+ `es6`
+ `worker`：web workers全局变量
+ `amd`：定义`require()`和`define()`作为全局变量
+ `mocha`
+ `jasmine`
+ `jest`
+ `phantomjs`
+ `protractor`
+ `qunit`
+ `jquery`
+ `prototypejs`
+ `shelljs`
+ `meteor`
+ `mongo`：MongoDB
+ `applescript`
+ `nashorn`
+ `serviceworker`
+ `atomtest`
+ `embertest`：Ember test helper变量
+ `webextensions`
+ `greasemonkey`

可以在文件内指定环境：`/*eslint-env node, mocha */`，也可以在命令模式下通过`--env`参数来指定。

配置文件设置如下：
```json
{
  "env": {
    "browser": true,
    "node": true
  }
}
```

或在** package.json ** 中：
```json
{
  "name": "mypackage",
  "version": "0.0.1",
  "eslintConfig": {
    "env": {
      "browser": true,
      "node": true
    }
  }
}
```

YAML设置：
```json
  env:
    browser: true
    node: true
```

如果某个插件使用了不同的环境，则可以如下设置：
```json
{
  "plugins": ["example"],
  "env": {
    "example/environment_name": true
  }
}
```

或在package.json中：
```json
{
  "name": "mypackage",
  "version": "0.0.1",
  "eslintConfig": {
    "plugins": ["example"],
    "env": {
      "example/environment_name": true
    }
  }
}
```

或在YAML中：
```json
  plugins:
    - example
  env:
    example/environment_name: true
```

#### 指定全局变量
如果我们在文件中引用了未定义的变量，ESLint就会触发`no-undef`警告信息。但往往我们会引入一些全局变量，此时我们就可以在文件中添加注释或在配置文件中指定全局变量来避免出现警告信息。  
```json
  /*global var1, var2*/
```

如果你认为这些全局变量是只读的，不可重新被覆盖的话，那么可以在每个变量上设置`false`标识。
```json
  /*global var1:false, var2:false*/
```

配置文件如下：
```json
{
  "globals": {
    "var1": true,
    "var2": false
  }
}
```

YAML配置：
```json
  globals:
      var1: true
      var2: false
```

#### 配置插件
ESLint支持第三方插件的使用，在使用插件前需要通过npm来安装。在配置文件中使用`plugins`来设置插件名称。带有`eslint-plugin-`前缀的插件名称可以省略其前缀。如：
```json
{
  "plugins": [
    "plugin1",
    "eslint-plugin-plugin2"
  ]
}
```

YAML：
```json
---
  plugins:
    - plugin1
    - eslint-plugin-plugin2
```

注意：如果是使用的全局安装的ESLint的话，则只能使用全局安装的ESlint插件。而使用本地安装的ESLint则可以使用全局和本地安装的ESLint插件。

#### 配置规则
ESLint有大量的规则，你可以在项目中通过添加注释的方式或者通过配置文件的方式来使用。

+ 0：停止使用该规则
+ 1：设置该规则为警告等级（不影响exit code）
+ 2：设置该规则为错误等级（此时exit code为1）

用注释的方式设置如下：
```javascript
  /*eslint eqeqeq:0, curly: 2*/
```

如果规则还有其他信息的话，则可以用如下方式设置：
```javascript
  /*eslint quotes: [2, "double"], curly: 2*/
```

配置文件方式：
```json
{
  "rules": {
    "eqeqeq": 0,
    "curly": 2,
    "quotes": [2, "double"]
  }
}
```

YAML：
```json
---
  rules:
    eqeqeq: 0
    curly: 2
    quotes:
      - 2
      - "double"
```

如果我们想为某个特定的插件配置规则的的话，想要如下设置：
```json
{
  "plugins": [
    "plugin1"
  ],
  "rules": {
    "eqeqeq": 0,
    "curly": 2,
    "quotes": [2, "double"],
    "plugin1/rule1": 2
  }
}
```

YAML：
```json
---
  plugins:
    - plugin1
  rules:
    eqeqeq: 0
    curly: 2
    quotes:
      - 2
      - "double"
    plugin1/rule1: 2
```
同样我们也可以用注释的方式设置：
```javascript
  /*eslint "plugin1/rule1": 2*/
```

注意：当指定特点插件规则时，需要去掉`eslint-plugin-`前缀。

临时需要disable某个规则时，可以如下：
```javascript
/*eslint-disable */

//Disable all rules between comments
alert('foo');

/*eslint-enable */
```

也可以设置disable的某些具体规则：
```javascript
/*eslint-disable no-alert, no-console */

alert('foo');
console.log('bar');

/*eslint-enable no-alert */
```

在某行上disable所有的规则：
```javascript
alert('foo'); // eslint-disable-line

// eslint-disable-next-line
alert('foo');
```

在某行上disable特定的规则：
```javascript
alert('foo'); // eslint-disable-line no-alert

// eslint-disable-next-line no-alert
alert('foo');
```

在某行上disable某些规则：
```javascript
alert('foo'); // eslint-disable-line no-alert, quotes, semi

// eslint-disable-next-line no-alert, quotes, semi
```

#### 设置共享
ESLint支持在配置文件中设置共享信息，你可以通过设置`settings`来让其他规则共享该信息，这对有些自定义的规则比较有用。
```json
{
  "settings": {
    "sharedData": "Hello"
  }
}
```

YAML：
```json
---
  settings:
    sharedData: "Hello"
```

#### 使用配置文件
通过命令行可以使用某个路径下的配置文件：`eslint -c myconfig.json myfiletotest.js`。

第二种方式是通过`.eslintrc.*`或`package.json`文件方式，ESlint会自动寻找当前目录下的这两个文件，如果没有就到它的父级目录寻找，直到文件系统的根目录。这种方式比较有用，特别是需要针对不同的部分应用不同的规则。最里面的配置规则要比它的父级权限大。

#### 配置文件的格式
ESLint支持如下几种格式的配置文件：

+ JavaScript：使用`.eslintrc.js`，通过export一个包含所有规则的对象
+ YAML：使用`.eslintrc.yaml` 或 `.eslintrc.yml` 来定义规则
+ JSON：使用`.eslintrc.json`来配置规则
+ Deprecated：使用`.eslintrc`，其可以是JSON或YAML格式
+ package.json：在`package.json`中定义`eslintConfig`来设置规则

如果目录下有多个配置格式的文件，则ESLint只会使用其中一个，优先顺序为：`.eslintrc.js` > `.eslintrc.yaml` > `.eslintrc.yml` > `.eslintrc.json` > `.eslintrc` > `package.json`

默认情况下，我们不需要应用跨项目的配置规则，即使项目在目录上有父子关系，此时我们可以通过在`package.json`的`eslintConfig`或`.eslintrc.*`中设置`"root": true`来禁止ESLint继续向上级查找配置文件。
```json
{
  "root": true
}
```

YAML：
```json
---
  root: true
```

比如下面：
```json
home
└── user
    ├── .eslintrc <- Always skipped if other configs present
    └── projectA
        ├── .eslintrc  <- Not used
        └── lib
            ├── .eslintrc  <- { "root": true }
            └── main.js
```

完整的配置链，从高到低如下：

內联配置：

+ /*eslint-disable*/ 和 /*eslint-enable*/
+ /*global*/
+ /*eslint*/
+ /*eslint-env*/

命令参数：

+ --global
+ --rule
+ --env
+ -c, --config

项目级别的配置：

+ 目录下的`.eslintrc.*` 或 `package.json`
+ 继续在父级目录中查找的`.eslintrc`和`package.json`文件，直到出现`"root": true`
+ 如果前两个都没有配置文件的话，就会用用户根目录下的`~/.eslintrc`

#### 继承配置文件
如果你想继承某个特定的配置文件，可以使用`extends`属性，并且指定一个路径，可以是相对或绝对路径。被继承的配置可以是YAML、JSON、JS或可共享的配置包，继承的配置文件可以覆盖某些规则：
```json
{
  "extends": "./node_modules/coding-standard/.eslintrc",

  "rules": {
    // Override any settings from the "parent" configuration
    "eqeqeq": 1
  }
}
```

可以继承多个文件，写在下面的配置规则覆盖上面配置中对应的规则。
```json
{
  "extends": [
    "./node_modules/coding-standard/eslintDefaults.js",
    // Override eslintDefaults.js
    "./node_modules/coding-standard/.eslintrc-es6",
    // Override .eslintrc-es6
    "./node_modules/coding-standard/.eslintrc-jsx",
  ],

  "rules": {
    // Override any settings from the "parent" configuration
    "eqeqeq": 1
  }
}
```

被继承的文件里面同样可以使用`extends`来继承其他的配置文件，你也可以继承通过npm安装的配置包，比如：
```json
{
  "extends": "eslint-config-myrules",

  "rules": {
    // Override any settings from the "parent" configuration
    "eqeqeq": 1
  }
}
```

同样这里也可以去掉`eslint-config-`前缀，ESLint会自动为你添加。

ESLint同样支持继承某个插件中的配置：
```json
{
  "extends": "plugin:eslint-plugin-myplugin/myConfig",

  "rules": {
    // Override any settings from the "parent" configuration
    "eqeqeq": 1
  }
}
```

这个例子中，`eslint-plugin-myplugin`插件中包含一个名为`default`的配置。这里也可以去掉`eslint-plugin-`前缀。

注意：在配置文件`extends`中指定的路径都是相对于项目的路径，而不是相对于该配置文件的路径。

#### 配置文件中的注释
JSON和YAML配置文件都支持JavaScript格式的注释，`package.json`不支持注释。
```json
{
  "env": {
    "browser": true
  },
  "rules": {
    // Override our default settings just for this directory
    "eqeqeq": 1,
    "strict": 0
  }
}
```

#### 指定哪些格式的文件被检测
目前唯一的方法是通过命令行的`--ext`来指定。

#### 忽略文件或目录
可以通过在项目的根目录下创建`.eslintignore`文件来告诉ESLint忽略哪些文件，每一行代表一个路径或文件。ESLint还始终忽略`/node_modules/**` 和 `/bower_components/**`文件。例子如下：
```json
  # /node_modules and /bower_components ignored by default

  # Ignore files compiled from TypeScript and CoffeeScript
  **/*.{ts,coffee}.js

  # Ignore built files except build/index.js
  build/
  !build/index.js
```

#### 使用其他对应的忽略文件
你也可以通过设置`--ignore-path`使用其他的忽略文件，如：`eslint --ignore-path .jshintignore file.js`

或：`eslint --ignore-path .gitignore file.js`

如果指定了上面的规则，则当前的`.eslintignore`将不会被再使用。

#### 关于忽略警告
如果你在忽略配置中指定了一个文件夹，则该文件夹下的文件及子文件夹都会被默默的忽略掉，如果你指定了一个具体的文件，则ESlint就会给出一个警告信息，如：
```json
  foo.js
```

运行：`eslint foo.js`

得到警告信息：
```json
  foo.js
    0:0  warning  File ignored because of your .eslintignore file. Use --no-ignore to override.

  ✖ 1 problem (0 errors, 1 warning)
```

之所以有这个信息，是ESLint不确定是否需要检测这个文件，你可以设置`--no-ignore`来覆盖之前的配置。

#### 集成ESLint
##### pre-commit hook
```json
#!/bin/zsh

function lintit () {
  OUTPUT=$(git diff --name-only | grep -E '(.js)$')
  a=("${(f)OUTPUT}")
  e=$(eslint -c eslint.json $a)
  echo $e
  if [[ "$e" != *"0 problems"* ]]; then
    echo "ERROR: Check eslint hints."
    exit 1 # reject
  fi
}
lintit
```

##### [gulp-eslint](https://www.npmjs.com/package/gulp-eslint)
安装：`npm install gulp-eslint`

```json
var gulp = require('gulp'),
    eslint = require('gulp-eslint');
 
gulp.task('lint', function () {
  // ESLint ignores files with "node_modules" paths. 
  // So, it's best to have gulp ignore the directory as well. 
  // Also, Be sure to return the stream from the task; 
  // Otherwise, the task may end before the stream has finished. 
  return gulp.src(['**/*.js','!node_modules/**'])
    // eslint() attaches the lint output to the "eslint" property 
    // of the file object so it can be used by other modules. 
    .pipe(eslint())
    // eslint.format() outputs the lint results to the console. 
    // Alternatively use eslint.formatEach() (see Docs). 
    .pipe(eslint.format())
    // To have the process exit with an error code (1) on 
    // lint error, return the stream and pipe to failAfterError last. 
    .pipe(eslint.failAfterError());
});

gulp.task('default', ['lint'], function () {
  // This will only run if the lint task is successful... 
});
```

或者使用插件api：
```json
gulp.src(['**/*.js','!node_modules/**'])
  .pipe(eslint({
    extends: 'eslint:recommended',
    ecmaFeatures: {
        'modules': true
    },
    rules: {
      'my-custom-rule': 1,
      'strict': 2
    },
    globals: {
      'jQuery':false,
      '$':true
    },
    envs: [
      'browser'
    ]
  }))
  .pipe(eslint.formatEach('compact', process.stderr));
```

##### webpack([eslint-loader](https://www.npmjs.com/package/eslint-loader))
```json
  npm install eslint-loader
```

使用：
```json
module.exports = {
  // ... 
  module: {
    loaders: [
      {test: /\.js$/, loader: "eslint-loader", exclude: /node_modules/}
    ]
  }
  // ... 
}
```
如果你使用了`babel-loader`的话，要注意写的顺序：
```json
module.exports = {
  // ... 
  module: {
    loaders: [
      {test: /\.js$/, loader: "babel-loader", exclude: /node_modules/},
      {test: /\.js$/, loader: "eslint-loader", exclude: /node_modules/}
    ]
  }
  // ... 
}
```

或者安全起见，可以使用`preLoaders`：
```json
module.exports = {
  // ... 
  module: {
    preLoaders: [
      {test: /\.js$/, loader: "eslint-loader", exclude: /node_modules/}
    ]
  }
  // ... 
}
```

##### Ember([ember-cli-eslint](https://www.npmjs.com/package/ember-cli-eslint))
```json
  ember install ember-cli-eslint
```

运行后会在你的项目目录下生成一个`.eslintrc`文件。 如果代码中应用到了ECMA最新的一些特性，需要在该文件中加入parser：
```json
  "parser": "babel-eslint",
```

例子：
```json
// ember-cli-build.js (or Brocfile.js on older versions of ember-cli) 

var path = require('path');
var EmberApp = require('ember-cli/lib/broccoli/ember-app');
// `npm install --save-dev js-string-escape` 
var jsStringEscape = require('js-string-escape');

var app = new EmberApp({
  eslint: {
    testGenerator: eslintTestGenerator
  }
});

function render(errors) {
  if (!errors) { return ''; }
  return errors.map(function(error) {
    return error.line + ':' + error.column + ' ' +
        ' - ' + error.message + ' (' + error.ruleId +')';
  }).join('\n');
}

// Qunit test generator 
function eslintTestGenerator(relativePath, errors) {
  var pass = !errors || errors.length === 0;
  return "import { module, test } from 'qunit';\n" +
    "module('ESLint - " + path.dirname(relativePath) + "');\n" +
    "test('" + relativePath + " should pass ESLint', function(assert) {\n" +
    "  assert.ok(" + pass + ", '" + relativePath + " should pass ESLint." +
    jsStringEscape("\n" + render(errors)) + "');\n" +
  "});\n";
}

// Mocha test generator 
function eslintTestGenerator(relativePath, errors) {
  var pass = !errors || errors.length === 0;
  return "import { describe, it } from 'mocha';\n" +
    "import { assert } from 'chai';\n" +
    "describe('ESLint - " + path.dirname(relativePath) + "', function() {\n" +
    "  it('" + relativePath + " should pass ESLint', function() {\n" +
    "    assert.ok(" + pass + ", '" + relativePath + " should pass ESLint." +
    jsStringEscape("\n" + render(errors)) + "');\n" +
  "  });\n});\n";
}
```

运行：
```json
npm install -g phantomjs;
ember test
```

