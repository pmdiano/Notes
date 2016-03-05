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

更简单的原型语法：
```javascript
function Person(){
}

Person.prototype = {
  name : "Nicholas",
  age : 29,
  job : "Software Engineer",
  sayName : function() {
    alert(this.name);
  }
};

// 重设构造函数，只适用于ECMAScript 5兼容的browser
Object.defineProperty(Person.prototype, "constructor", {
  enumerable: false,
  value: Person
});
```
原生的应用类型，都是采用这种模式创建的。`Object`, `Array`, `String`，等等，都在其构造函数的原型上定义了方法，且可以定义新方法（并不推荐这么做）。
```javascript
alert(typeof Array.prorotype.sort);       // "function"
alert(typeof String.prorotype.substring); // "function"

String.prototype.startWith = function (text) {
  return this.indexOf(text) == 0;
};

var msg = "Hello world!";
console.log(msg.startWith("Hello"));
```

原型对象也有问题。原型中所有属性是被很多实例共享的，这对于函数非常合适，对于包含基本值的属性倒也说得过去，但对于包含引用类型值的属性来说，问题就比较突出了。

### 6.2.4 组合使用构造函数模式和原型模式
创建自定义类型的最常见方式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。且支持像构造函数传递参数。
```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Shelby", "Court"];
}

Person.prototype = {
    constructor : Person,
    sayName : function() {
        console.log(this.name);
    }
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.friends.push("Van");
console.log(person1.friends);
console.log(person2.friends);
console.log(person1.friends === person2.friends);
console.log(person1.sayName === person2.sayName);
```

### 6.2.5 动态原型类型
```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;

    if (typeof this.sayName != "function") {
        Person.prototype.sayName = function() {
            console.log(this.name);
        };
    }
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
person1.sayName();
```
使用动态原型模式时，不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。

### 6.2.6 寄生构造函数模式
```javascript
function Person(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        console.log(this.name);
    };
    return o;
}

var p1 = new Person("Nicholas", 29, "Software Engineer");
var p2 = new Person("Greg", 27, "Doctor");
p1.sayName();
p2.sayName();
console.log(p1.sayName === p2.sayName);
```
除了使用`new`操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一样的。不能依赖`instanceof`操作符来确定对象类型。

### 6.2.7 稳妥构造函数模式（durable objects）
指没有公共属性，而且其方法也不引用`this`的对象。与寄生构造函数相似，有两点不同：一是新创建的实例方法不引用`this`，二是不使用`new`操作符调用构造函数。
```javascript
function Person(name, age, job) {
  var o = new Object();

  // 在这里定义私有变量和函数

  // 添加方法
  o.sayName = function() {
    alert(name);
  };

  // 返回对象
  return o;
}
```
在这种模式创建的对象中，除了使用`sayName()`方法以外，没有其他办法访问`name`的值。

## 6.3 继承
JavaScript中无法实现接口继承。只支持实现继承，而且其实现继承主要是依靠原型链来实现的。
### 6.3.1 原型链
利用原型让一个引用类型继承另一个引用类型的属性和方法。每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。
```javascript
function SuperType(){
    this.property = true;
}
SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

// 继承了SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function(){
    return this.subproperty;
};

var instance = new SubType();
console.log(instance.getSuperValue());  // true
console.log(instance.getSubValue());    // false
```
这里我们没有使用`SubType`的默认原型，而是给了它一个新原型；这个新原型就是`SuperType`的实例。此外，`instance.constructor`现在指向的是SuperType。一句话，`SubType`继承了`SuperType`，`SuperType`继承了`Object`。当调用`instance.toString()`时，实际上调用的是保存在`Object.prortotype`中的那个方法。

确定原型和实例关系的两种方法：

1. `instanceof`操作符
2. `isPrototype()`方法

```javascript
alert(instance instanceof Object);      // true
alert(instance instanceof SuperType);   // true
alert(instance instanceof Subtype);     // true

alert(Object.prototype.isPrototypeOf(instance));    // true
alert(SuperType.prototype.isPrototypeOf(instance)); // true
alert(SubType.prototype.isPrototypeOf(instance));   // true
```
若子类型需要重写超类型中的某个方法，给原型添加方法的代码一定要放在替换原型的语句之后。

原型链的问题：原型是另一个类型的实例，于是，所有新类型的实例都共享了创建原型链的那个另一个类型的实例的属性。实践中很少会单独使用原型链。

### 6.3.2 借用构造函数 (constructor stealing)
有时也叫做伪造对象或经典继承。在子类型构造函数的内部调用超类型构造函数。
```javascript
function SuperType(){
  this.colors = ["red", "blue", "green"];
};

function SubType(){
  // 继承了SuperType
  SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);  // "red,blue,green,black"

var instance2 = new SubType();
alert(instance2.colors);  // "red,blue,green"
```
`call`那一句实际上实在（未来将要）新创建的`SubType`实例的环境下调用了`SuperType`的构造函数。相对原型链而言，此方法一个很大的优势是可以在子类型构造函数中向超类型构造函数传递参数。但也无法避免构造函数模式存在的问题 - 无函数复用。而且在超类型的原型中定义的方法，对子类型而言也是不可见的。这种方法也是很少单独使用的。

### 6.3.3 组合继承（combination inheritance）
也叫伪经典继承，指的是将原型链和借用构造函数的技术组合到一块。使用原型链实现对原型属性和方法的继承，借用构造函数来实现对实例属性的继承。
```javascript
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
    console.log(this.name);
};

function SubType(name, age){
    // 继承属性
    SuperType.call(this, name);

    this.age = age;
}

// 继承方法
SubType.prototype = new SuperType();

SubType.prototype.sayAge = function(){
    console.log(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
console.log(instance1.colors);
instance1.sayName();
instance1.sayAge();

var instance2 = new SubType("Greg", 27);
console.log(instance2.colors);
instance2.sayName();
instance2.sayAge();

var instance3 = new SuperType("Jack");
console.log(instance3.colors);
instance3.sayName();
```
这是JavaScript中最常用的继承模式。

### 6.3.4 原型式继承
ECMAScript 5新增的方法：`Object.create()`。在传入一个参数的情况下，与如下`create()`方法行为相同：
```javascript
function object(o){
  function F(){}
  F.prototype = o;
  return new F();
}
```
`Object.create()`方法的第二个参数与`Object.defineProperties()`方法的第二个参数格式相同，每个属性都是通过自己的描述符定义的。
```javascript
var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = Object.create(person, {
  name: {
    value: "Greg"
  }
});
```
包含引用类型值的属性始终都会共享相应的值，就像使用原型模式一样。

### 6.3.5 寄生式继承
```javascript
function createAnother(original){
  var clone = object(original);
  clone.sayHi = function(){
    console.log("hi");
  };
  return clone;
}

var person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = createAnother(person);
anotherPerson.sayHi();
```
用此种方式添加函数对象，会由于不能做到函数复用而降低效率。

### 6.3.6 寄生组合式继承
组合继承是最常用的，它的不足是会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。寄生组合式继承没有这个问题，一般认为是最理想的。
```javascript
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype);    // 创建对象
    prototype.constructor = subType;    // 增强对象
    subType.prototype = prototype;      // 指定对象
}

function SuperType(name){
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

SuperType.prototype.sayName = function(){
    console.log(this.name);
}

function SubType(name, age){
    SuperType.call(this, name);

    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function(){
    console.log(this.age);
}
```
