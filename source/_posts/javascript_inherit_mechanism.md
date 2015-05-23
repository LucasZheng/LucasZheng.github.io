title: JavaScript的继承机制
description: javascript中几种常见的继承方式
date: 2014-12-23 16:27:46

categories: 
- javascript

tags:
- javascript

---
在JavaScript中，主要有以下几种手法实现继承：对象冒充、原型链、apply和call方法。<!-- more -->

#### 父类:
{% codeblock lang:javascript %}
function ClassA(sColor) {
    this.color = sColor;
    this.sayColor = function () {
        alert(this.color);
    };
} 
{% endcodeblock %}  

##### 第一种方式:对象冒充
{% codeblock lang:javascript %}
function ClassB(sColor, sName) {
    this.newMethod = ClassA;  //注意这里
    this.newMethod(sColor);
    delete this.newMethod;
    this.name = sName;
    this.sayName = function () {
    	alert(this.name);
    };
}
{% endcodeblock %}  

##### 第二种方式:call()
{% codeblock lang:javascript %}
function ClassB(sColor, sName) {
    ClassA.call(this, sColor);
    this.name = sName;
    this.sayName = function () {
    	alert(this.name);
    };
}
{% endcodeblock %}  

##### 第三种方式:apply()
{% codeblock lang:javascript %}
function ClassB(sColor, sName) {
    ClassA.apply(this, arguments);
    this.name = sName;
    this.sayName = function () {
    	alert(this.name);
    };
}
{% endcodeblock %}  

##### 第四种方式:原型链(prototype)
{% codeblock lang:javascript %}
function ClassA() {}
ClassA.prototype.color = "red";
ClassA.prototype.sayColor = function () {
    alert(this.color);
};

function ClassB() {}
ClassB.prototype = new ClassA();
ClassB.prototype.name = "";
ClassB.prototype.sayName = function () {
    alert(this.name);
};
//测试
var objA = new ClassA();
var objB = new ClassB();
objA.color = "red";
objB.color = "blue";
objB.name = "Nicholas";
objA.sayColor();
objB.sayColor();
objB.sayName();
{% endcodeblock %}
