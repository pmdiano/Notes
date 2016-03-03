# 第五章 引用类型
## 5.6 基本包装类型
使用`new`调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的：
```javascript
var value = "25";
var number = Number(value);   // 转型函数
alert(typeof number);         // "number"

var obj = new Number(value);  // 构造函数
alert(typeof obj);            // "object"
```
### 5.6.1 Boolean类型
```javascript
var falseObject = new Boolean(false);
var result = falseObject && true; // Boolean对象为true
alert(result);                // true

var falseValue = false;
result = falseValue && true;
alert(result);                // false

alert(typeof falseObject);    // object
alert(typeof falseValue);     // boolean
alert(falseObject instanceof Boolean);  // true
alert(falseValue instanceof Boolean);   // false
```
建议永远不要使用Boolean对象。只要使用基本类型的布尔值。
### 5.6.2 Number类型
`toFixed()`, `toExponential()`, `toPrecision()`。
仍然不建议直接实例化Number类型，原因同上，在使用`typeof`和`instanceof`操作符测试基本类型数值与引用类型数值时，得到的结果完全不同。
### 5.6.3 String类型
`charAt()`, `charCodeAt()`, `concat()`, `slice()`, `substr()`, `substring()`, `indexOf()`, `lastIndexof()`, `trim()`, `toLowerCase()`, `toLocaleLowerCase`, `toUpperCase`, `toLocaleUpperCase`, `match()`, `search()`, `replace()`, `split()`, `localeCompare()`, `fromCharCode()`

其中`replace()`接受两个参数，第一个参数可以是`RegExp`对象或字符串，第二个参数可以是字符串或者一个函数。如果第一个参数是字符串，那么只会替换第一个字符串。要替换所有符合的字符串，唯一的办法是提供一个正则表达式，并且指定全局标志：
```javascript
var text = "cat, bat, sat, fat";
var result = text.replace("at", "ond");
alert(result);    // "cond, bat, sat, fat"

result = text.replace(/at/g, "ond");
alert(result);    // "cond, bond, sond, fond"

result = text.replace(/(.at)/g, "word ($1)");
alert(result);    // "word (cat), word (bat), word (sat), word (fat)"
```
## 5.7 单体内置对象
### 5.7.1 Global对象
不属于任何其他对象的属性和方法，最终都是它的属性和方法：`isNan()`, `isFinite()`, `parseInt()`, `parseFloat()`, `encodeURI()`, `encodeURIComponent()`, `decodeURI()`, `decodeURIComponent()`, `eval()`

另一种取得Global对象的方法是：
```javascript
var global = function(){
  return this;
}();
```
### 5.7.2 Math对象
```javascript
var values = [1, 2, 3, 4, 5, 6, 7, 8];
var max = Math.max.apply(Math, values);
```
# 第六章 面向对象的程序设计
## 6.1 理解对象
两种属性：数据属性和访问器属性。

数据属性：
+ `[[configurable]]`
+ `[[Enumerable]]`
+ `[[Writable]]`
+ `[[Value]]`

要修改属性默认的特性，须使用`Object.defineProperty()`方法。把`configurable`改为`false`后就不能改回来了。

访问器属性：
+ `[[Configurable]]`
+ `[[Enumerable]]`
+ `[[Get]]`
+ `[[Set]]`
访问器属性须使用`Object.defineProeprty()`来定义。

定义多个属性：`Object.defineProperties()`。

读取属性的特性：`Object.getOwnPropertyDescriptor()`。
## 6.2 创建对象
### 6.2.1 工厂模式
由于JavaScript中无法创建类，于是用函数来封装以特定接口创建对象的细节：
```javascript
function createPerson(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function(){
    alert(this.name);
  };
  return o;
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
```
工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。
### 6.2.2 构造函数模式
像`Object`和`Array`这样的原生构造函数，在运行时会自动出现在执行环境中。也可以创建自定义的构造函数：
```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.sayName = function(){
    alert(this.name);
  };
}

var person1 = new Person("Nicholas", 29, "Software Engineer");

alert(person1.constructor == Person);   // true
alert(person1 instanceof Object);       // true
alert(person1 instanceof Person);       // true
```
注意`Person()`构造函数按照惯例是大写开头。它内部没有显示地创建对象，直接将属性和方法赋给了`this`对象，且没有`return`语句。要创建`Person`的新实例，必须使用`new`操作符，以这种方式调用构造函数实际上会经历一下4个步骤：

1. 创建一个新对象
2. 将构造函数的作用域赋给新对象（因此`this`就指向了这个新对象）
3. 执行构造函数中的代码
4. 返回新对象

以这种方式定义的构造函数是定义在`Global`对象中。构造函数和其他函数唯一的区别，就在于调用它们的方式不同；只要通过`new`操作符来调用就是构造函数，否则就是普通函数：

```javascript
// 作为普通函数调用
Person("Greg", 27 "Doctor");  // 添加到window
window.sayName();             // "Greg"

// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName();                  // "Kristen"
```
构造函数模式的主要问题，就是每个方法都要在每个实例上重新创建一遍。
```javascript
alert(person1.sayName == person2.sayName);    // false
```
### 6.2.3 原型模式
`isPrototypeOf()`, `Object.getPrototypeOf()`
```javascript
function Person(){
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
  alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

person1.name = "Greg";
alert(person1.name);    // "Greg"
alert(person2.name);    // "Nicholas"

alert(person1.hasOwnProperty("name"));  // true
alert(person2.hasOwnProperty("name"));  // false

delete person1.name;
alert(person1.name);    // "Nicholas"
```
为对象实例添加属性只会阻止我们访问原型中的那个属性，但不会修改原型中的那个属性。`hasOwnProperty()`可以检测一个属性是否存在与实例中，还是存在于原型中。EMCAScript 5的`Object.getOwnPropertyDescriptor()`方法只能用于实例属性，要取得原型属性的描述符，必须直接在原型对象上调用`Object.getOwnPropertyDescriptor()`方法。

单独使用`in`操作符时，无论属性存在于实例中还是原型中，都会返回`true`。同时使用`hasOwnProperty()`方法和`in`操作符，就可以确定该属性到底是存在于对象中，还是原型中：
```javascript
function hasProrotypeProperty(object, name){
  return !object.hasOwnProperty(name) && (name in object);
}
```
在使用`for-in`循环时，返回的是所有能够通过对象访问的，可枚举的属性，包括实例中的属性和原型中的属性。屏蔽了原型中不可枚举属性的实例属性也会在`for-in`循环中返回，因为根据规定，所有开发人员定义的属性都是可枚举的。

取得对象上所有可枚举的实例属性：`Object.keys()`（ECMAScript 5)

取得所有实例属性，无论它是否可枚举：`Object.getOwnPropertyNames()`