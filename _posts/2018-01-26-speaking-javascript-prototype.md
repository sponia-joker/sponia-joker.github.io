---
layout: post
title:  "深入理解JavaScript原型"
date:   2018-01-26 12:02:15 +0800
categories: javascript
---
### 前言
在软件工程中，代码重用的模式极为重要，因为他们可以显著地减少软件开发的成本。在那些主流的基于类的语言（比如Java,C++）中都是通过继承(extend)来实现代码复用，同时类继承引入了一套类型规范。而JavaScript是一门弱类型的语言，从来不需要类型装换，在JavaScript中变量可以指向任何类型的value(ES6规范中的类也只是语法糖，基于类的继承本质上也是通过原型实现)。而基于原型的继承模式可以说提供了更加丰富的代码重用模式（后面再详细讲解JavaScript中的常用继承模式，本文只专注于JavaScript中的原型），一个对象可以直接继承另外一个对象，从而获得新的方法和属性。<!-- more -->

### 适合人群
* 对JavaScript原型有一定了解，希望深入了解原型。
* 具有JavaScript相关开发经验
* 不适合刚接触JavaScript人员

### 对象
要理解JavaScript中的原型关系，首先必须弄清楚对象的基本概念。[ECMAScript 5.1](http://www.ecma-international.org/ecma-262/5.1/#sec-4.3)规范中描述的对象

> An object is a collection of properties and has a single prototype object. The prototype may be the null value.

直译就是：对象是属性的集合并且拥有一个原型对象。原型可能是null(除非故意设置一个对象的原型为null，否则只有Object.prototype的原型为null)。我们可以简单把对象想象成hash表。有一种说法是JavaScript中一切都是对象，这种说法并不准确。[How is almost everything in Javascript an object?](https://stackoverflow.com/questions/9108925/how-is-almost-everything-in-javascript-an-object)
### 原型
> JavaScript的原型存在着诸多矛盾。它的某些复杂的语法看起来就像那些基于类的语言，这些语法的问题掩盖了它的原型机制。它不直接让对象从其他对象继承，反而插入一个多余的间接层：通过构建器函数产生对象。——JavaScript语言精粹第5章节（继承）

虽然可以直接设置一个对象的原型为另外一个对象，从而获得新的方法和属性。如下所示：

```javascript
// Generic prototype for all letters.
let letter = {
  getNumber() {
    return this.number
  }
}

// 在ES6规范中，已经正式把__proto__属性添加到规范中
// 也可以通过Object.setPrototypeOf(obj, prototype) Object.getPrototypeOf(obj)
// 设置和获取对象的原型对象

let a = { number: 1, __proto__: letter }
let b = { number: 2, __proto__: letter }
// ...
let z = { number: 26, __proto__: letter }

console.log(
  a.getNumber(), // 1
  b.getNumber(), // 2
  z.getNumber() // 26
)
```
对象之间的关系可以用下图来表示

![原型关系图](https://user-gold-cdn.xitu.io/2018/1/28/1613bfe82f758bbe?w=1205&h=1035&f=png&s=85731)

但规范主要介绍了如何利用构造函数去构建原型关系。所以JavaScript语言精粹的作者[Douglas Crockford](https://en.wikipedia.org/wiki/Douglas_Crockford)才会认为：不让对象直接继承另外一个对象，而通过中间层（构造函数）去实现显得有些复杂而且存在一些弊端。调用构造器函数忘记new关键字，this将不会绑定到一个新对象上。悲剧的是，this将会绑定到全局对象上。详情可以阅读JavaScript语言精粹继承章节。

下面利用构造函数来实现上述同样功能

```javascript
function Letter(number) {
  this.number = number
}

Letter.prototype.getNumber = function() {
  return this.number
}

let a = new Letter(1)
let b = new Letter(2)
let z = new Letter(26)

console.log(
  a.getNumber(), // 1
  b.getNumber(), // 2
  z.getNumber() // 26
)
```
其中原型关系可以下图表示

![原型关系](https://user-gold-cdn.xitu.io/2018/1/28/1613bfe83c26cac1?w=1779&h=1035&f=png&s=129668)

#### `prototype`和`__proto__`属性

我们看下规范中有关原型介绍的核心，更多详情请阅读[ECMAScript 5.1 4.2.1章节 ](http://www.ecma-international.org/ecma-262/5.1/#sec-4.2.1)
> ...Each constructor is a function that has a property named “prototype” that is used to implement prototype-based inheritance and shared properties...

> Every object created by a constructor has an implicit reference (called the object’s prototype) to the value of its constructor’s “prototype” property. Furthermore, a prototype may have a non-null implicit reference to its prototype, and so on; this is called the prototype chain...

通过上面的描述我们可以得出以下结论
1. 构造函数就是一个函数，函数中包含prototype属性用来实现基于原型的继承和共享属性。通过Function.prototype.bind方法构造出来的函数是个例外，它没有prototype属性。
2. 通过被构造函数创建的对象都有一个隐式的引用指向构造函数的prototype属性。
3. 构造函数的prototype属性值同样也是普通一个对象，它也有一个隐式的引用(non-null)指向它的原型对象。这样才形成了原型链，所以通过原型链去查找属性值时候，并不会访问prototype属性，而是obj.__proto__.__proto__...这样一层一层去寻找。

构造函数说到底本质上也是一个普通函数，只是该函数专门通过`new`关键字来生成对象。所以JavaScript语言无法确定哪个函数是打算用来做构造函数的。所以每个函数都会得到一个prototype属性，该属性值是一个包含constructor属性且constructor属性值为该函数的对象，如下所示。
![构造函数](https://user-gold-cdn.xitu.io/2018/1/29/1614141d0140afb9?w=539&h=337&f=png&s=12117)

只有函数才拥有prototype属性用来实现原型的继承，其他对象并没有。对象拥有__proto__指向其原型对象，JavaScript引擎可通过内部属性`[[prptotype]]`获取对象的原型对象。

关于这两个属性联系可以用一句话概括：**`__proto__` is the actual object that is used in the prototype chain to resolve field,methods, etc. prototype is the object that is used to build `__proto__` when you create an object with new.**

### 为什么要设计构造函数
如果你已经了解JavaScript原型，那我们可以来讲讲JavaScript语法为什么要设计构造函数。

首先来加深一遍概念：JavaScript是一门基于原型继承的语言，这意味着对象可以直接从其他对象继承属性，该语言是无类型的。
然而这种设计是偏离主流方向的，当时主流语言JavaScript，C++都是通过 new Class 的语法来创建对象。JavaScript显然对它的原型本质缺乏信心，所以它提供了一套和`class`语法类似的对象构建语法——也就是构造函数。通过`instanceof`操作符来判断对象是否属于某一类型。
#### instanceof 操作符
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)介绍了其内部原理

> The **instanceof** operator tests whether the prototype property of a constructor appears anywhere in the prototype chain of an object.

instanceof操作符的语法

> object instanceof constructor

简单来说：instanceof 操作符就是判断**构造函数的prototype属性值是否能在object对象的原型链中被找到**，对就是这么简单。这样通过构造函数语法JavaScript引入了类的概念（伪类）。

### 最后的彩蛋

知乎用户[wang z](https://www.zhihu.com/people/zheng-wang-24/activities)在其专栏中发布一张有关JavaScript原型链图，可以说看懂了图片也就清楚了JavaScript中的原型关系，感兴趣的用户可以直接浏览[详情](https://zhuanlan.zhihu.com/p/22189387)。如下图所示
![原型链图](https://user-gold-cdn.xitu.io/2018/1/28/1613bfe784db6292?w=1635&h=957&f=jpeg&s=168465)

笔者就可能读者遇到的问题备注如下：
1. Object.__proto__=== Function.prototype。Object本质上是一个built-in的全局构造函数，也是Function构造函数的实例。所以Object.__proto__ === Function.prototype.
2. Number，Date，Array等built-in构造函数都和Object构造函数一样。
3. Function.prototype本质也是对象，所以其__proto__指向Object.prototype

### 最后
如果你最后还是没有弄清楚JavaScript中的原型关系，可以在评论中进行描述我将尽我所能帮你答疑解惑。
或许你也可以看看参考文献中的引用链接。

### 参考文献
1. [JavaScript. The Core: 2nd Edition](http://dmitrysoshnikov.com/ecmascript/javascript-the-core-2nd-edition/)
2. [ecma-262](http://www.ecma-international.org/ecma-262/5.1/#sec-4.2.1)
3. [JavaScript Prototype in Plain Language](http://javascriptissexy.com/javascript-prototype-in-plain-detailed-language/)
4. [__proto__ VS. prototype in JavaScript](https://stackoverflow.com/questions/9959727/proto-vs-prototype-in-javascript)
5. Javascript语言精粹第五章节

