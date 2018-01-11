---
title: "JavaScript原型模式"
excerpt: "对象的创建不是通过实例化类的方法实现的，而是采用原型模式生成的。即当要获取一个新对象的时候，是通过从另一个对象 clone 而来，这样每个对象都有一个对应的原型对象"
modified: 2015-05-12
categories: 
  - JavaScript
tags:
  - JavaScript
---

{% include toc title="内容列表" icon="file-text" %}

目前 JavaScript 没有类，对象的创建不是通过实例化类的方法实现的，而是采用原型模式生成的。即当要获取一个新对象的时候，是通过从另一个对象 clone 而来，这样每个对象都有一个对应的原型对象。JavaScript 对象可使用对象字面量直接创建或采用构造函数的方式创建。

## 使用构造函数创建对象

采用构造函数的方式即 new 关键字 + 函数。整个过程如下：
 1 系统创建一个对象
 2 将构造函数的 this 指向这个对象，调用构造函数完成对对象的初始化，
 3 返回对象。

```javascript
function F(name) {
  this.name = name;
}
var obj = new F('func');
obj.name === 'func'; //true
```

由于通过构造函数的方式创建的每一个对象都会拥有各自独立的属性，会造成同一功能的方法在每一个对象中均有一份拷贝，函数没有被复用，造成资源浪费。解决此问题需要用到原型。

## 使用原型

无论什么时候， 只要创建了一个新函数，系统就会根据一组特定的规则为函数创建一个 prototype 属性， 这个属性指向函数的原型对象。也就说每声明一个函数，系统会自动为该函数配套创建一个相对应的原型对象，函数有一个 prototype 属性指向此原型对象。

``` javascript
obj.constructor === F; // true
F.prototype.constructor === F; //true
obj.__proto__ === F.prototype; //true
```

JavaScript 对象属性在使用时有一个特性，即当在对象中查询不到此属性时，会继续向其原型对象中查找。这样将对象的共用函数和属性定义在其原型对象上，多个对象共同引用同一个原型对象，就实现了属性和方法的共享。

## 动态原型模式

在构造函数中初始化原型
``` javascript
function Person(name) {
  this.name = name;
  if(typeof this.sayName !== 'function') {
    Person.prototype.sayName = function () {
      console.log('Hello, ' + this.name);
    }
  }
}
```

## 继承

继承是指子类型可以使用父类型中的属性和方法。

### 借用构造函数

通过 call、apply 方法改变 this 指向，使其指向子类型，利用父类型的构造函数的对子类型的属性进行初始化。

``` javascript
function Person(name) {
  this.name = name;
  this.sayHello = function() {
     console.log('Hello there, I am ' + this.name);
  }
}
function Friend(name) {
  Person.call(this, name);
}
var friend = new Friend('Liu');
friend.sayHello();
```

问题：
* 每个 friend 对象都有 sayHello 方法属性，方法没有复用
* Friend 无法使用 Person 的原型链上的属性

### 组合继承

组合使用构造函数和原型链，方法和原型属性放置于原型链上，子类对象要拥有的属性放置于构造函数中

```javascript
function Person(name) {
  this.name = name;
}
Person.prototype.sayHello = function() {
  console.log('Hello there, I am ' + this.name);
}
function Friend(name) {
  Person.call(this, name);
}
Friend.prototype = new Person();
var friend = new Friend('Liu');
friend.sayHello();
```

### 原型式继承

将继承的过程封装，以传入对象为原型，返回一个新的对象，新对象可被附加参数增强。即 Object.create 方法

```javascript
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
``` 

### 寄生式继承

接收一个对象作为新对象的原型，并将新对象加强返回。

```javascript
function Sub(originObj) {
  var clone = object(originObj);
  clone.sayHello = function() {
    console.log('Hello');
  };
}
```

### 寄生组合式继承

改进组合模式两次调用父类型构造函数的情况，通过原型继承的方式使子类型的原型指向父类型的原型对象，从而获得父类型原型上的定义的方法属性。

```javascript
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
} 
function inheritPrototype(subType, superType) {
  //创建对象
  var prototype = object(superType.prototype);
  // 增强对象，原型对象的构造函数指向子类型，同 Function.prototype.constructor = Function
  prototype.constructor = subType;
  //指定对象，子类型原型指向通过寄生于父类型原型的对象
  subType.prototype = prototype;
} 
function Person(name) {
  this.name = name;
}
Person.prototype.sayHello = function() {
  console.log('Hello there, I am ' + this.name);
}
function Friend(name) {
  Person.call(this, name);
}
inheritPrototype(Friend, Person);
```