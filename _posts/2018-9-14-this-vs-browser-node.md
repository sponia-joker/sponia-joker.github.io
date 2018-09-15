---
layout: post
title:  "老生常谈之再谈this"
categories: javascript


---

### 前言

JavaScript中this指向一直都是刚接触JavaScript或者已经有一两年JavaScript编程经验人员容易弄混或者记不清的地方。this的复杂性主要是由this只有在调用时才能确定其值而不是在声明时和主流认为JavaScript语言设计缺陷导致。this在浏览器和node环境的细微差别，更加增加了理解难度。在JavaScript语言精粹中作者从方法调用，函数调用，构造器调用和类apply调用分别讲解了this的绑定。本文也会从这几方面介绍this，同时会介绍node，浏览器环境的轻微不同来帮助读者理解，也方便自己以后复习。

### 方法调用

当一个函数保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时，this就绑定到该对象上。

```javascript
var obj = {
    name:'joker',
    sayName:function(){
        console.log(this.name) // joker
    }
}
obj.sayName()
```

方法可以使用this访问自己所属的对象。this到对象的绑定发生在方法被调用时，而不是在我们定义obj对象时候。

### 函数调用

函数调用的模式可能是this变得复杂的最终原因。本章节我们将从浏览器和node环境来讲解this的绑定

#### 函数调用之浏览器环境

注意以下实例都需要在浏览器环境中运营，不然结果可能不一致。首先我们看看简单函数一般的调用方法

```JavaScript
var name = 'joker'
function sayName(){
    console.log(this.name)
}
sayName(joker) // joker
```

很显然this对象被绑定到了全局window对象上，相信大家没有什么疑惑的。但是编程实际中，一般不会存在这么简单的函数，JavaScript中很多函数的执行都会返回一个新的函数，然后执行新的函数（是不是感觉很熟悉，其实我们可以把新的函数理解成闭包，闭包就不展开了，有兴趣可以自行google）

```javascript
var number = 1
var obj = {
    number:999,
    f1:function(){
        return function(){
            console.log(this.number)
        }
    }
}
obj.f1()() // 1
```

此时this还是被绑定了全局对象，大多数人认为这是语言上的设计错误。倘若正确设计内部函数被调用时，this应该绑定到外部函数this。一般为了解决这种this绑定问题，可以在外部函数中执行 var that = this.将this赋予给变量that(变量名不一定要取that)然后在内部函数中引用that。之所以that可以访问到我们期望的值，是因为我们在外部函数定义了变量that，当我们在内部函数访问that时JavaScript引擎会通过作用域链来寻找变量that，最终在父作用域中找到。另外我们也可通过箭头函数来保证访问到正确的值，就不列出代码了。

```javascript
var number = 1
var obj = {
    number:999,
    f1:function(){
        var that = this
        return function(){
            console.log(that.number)
        }
    }
}
obj.f1()() //999
```

#### 函数调用之node环境

在node环境中，this的指向有轻微的一些差别。首先在node环境中，我们需要认识到以下几点

1. 全局对象不是window而是global
2. Node在内部采用的是commonjs规范，所有代码都会运行在模块作用域中，而不是全局作用域（用户代码都会被node包裹在一个函数中，从而不会污染全局作用域）

由于node中对代码的特殊处理，使得全局对象，模块中this指向都会和浏览器存在一些不同

1. 模块中顶层this===module.exports
2. 全局变量的声明必须通过global显示声明

```javascript
console.log(this===module.exports) // true
var number = 1
global.number = 2
var obj = {
    number:999,
    f1:function(){
        return function(){
            // node中此处的this依然会指向全局对象global，而不是当前模块作用域的this(module.exports)
            console.log(this.number,this===global) // 2 true
        }
    }
}
obj.f1()()
```

可以看到除了全局对象，node模块中顶层this===module.exports之外，其余和浏览器环境中没有什么不同。

### 构造函数调用

一个函数如果创建的目的就是希望结合`new`使用，那么就被称为构造函数，按照约定一般函数名首字母要大写从而和普通函数进行区别。如果new调用构造函数，this会绑定到那个新对象上

```javascript
function People(name){
	this.name = name
}
var people = new People('joker')
console.log(people.name) // joker
```

### 类apply调用方式

因为JavaScript是一门函数式的面向对象编程语言，函数可以拥有方法，apply，call，bind方法都可以让我们手动指定this的值。

```javascript
var obj = {
    name:'joker'
}

function sayName(){
    console.log(this.name) // joker
}

sayName.apply(obj)

```

如果采用一般函数调用方式，this会指向全局对象。但是我们可以通过apply方式指定this值来调用函数。

### 参考资料

1. JavaScript语言精粹
2. https://www.sitepoint.com/javascript-this-event-handlers/
3. https://stackoverflow.com/questions/28955047/why-does-a-module-level-return-statement-work-in-node-js/28955050#
4. https://stackoverflow.com/questions/12370851/understanding-javascript-scope-with-var-that-this/12371105#12371105
5. https://stackoverflow.com/questions/22770299/meaning-of-this-in-node-js-modules-and-functions

