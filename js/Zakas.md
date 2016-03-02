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
