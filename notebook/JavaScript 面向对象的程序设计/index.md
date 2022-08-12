<!-- TOC -->

- [JavaScript 面向对象的程序设计](#javascript-面向对象的程序设计)
  - [理解对象](#理解对象)
    - [属性类型](#属性类型)
      - [1 、数据属性](#1-数据属性)
      - [2 、访问器属性](#2-访问器属性)
    - [定义多个属性 `Object.defineProperties`](#定义多个属性-objectdefineproperties)
    - [读取属性的特性 `Object.getOwnPropertyDescriptor()`](#读取属性的特性-objectgetownpropertydescriptor)
  - [创建对象](#创建对象)
    - [工厂模式](#工厂模式)
    - [构造函数模式](#构造函数模式)
    - [原型模式](#原型模式)
    - [组合使用构造函数模式和原型模式](#组合使用构造函数模式和原型模式)
    - [动态原型模式](#动态原型模式)
    - [寄生构造函数模式](#寄生构造函数模式)
    - [稳妥构造函数模式](#稳妥构造函数模式)
  - [继承](#继承)
    - [原型链](#原型链)
    - [借用构造函数](#借用构造函数)
    - [组合继承](#组合继承)
    - [原型继承](#原型继承)
    - [寄生式继承](#寄生式继承)
    - [寄生组合式继承](#寄生组合式继承)
  - [JavaScript 构造函数、继承、原型](#javascript-构造函数继承原型)
    - [万物皆对象](#万物皆对象)
    - [关键字](#关键字)
    - [`prototype`](#prototype)
    - [`proto`](#proto)
    - [`new` 原理](#new-原理)
    - [`Object.prototype`、`Function.prototype`](#objectprototypefunctionprototype)
    - [构造函数、原型和实例的关系](#构造函数原型和实例的关系)
    - [`instanceof` 原理](#instanceof-原理)
    - [`isPrototypeOf()`、`getPrototypeOf()`、`setPrototypeOf()`](#isprototypeofgetprototypeofsetprototypeof)
    - [`Object.create()` 原理](#objectcreate-原理)
  - [构造函数的返回值](#构造函数的返回值)
  - [Class](#class)

<!-- /TOC -->

# JavaScript 面向对象的程序设计
 
## 理解对象

![面向对象中的常见概念](./images/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E4%B8%AD%E7%9A%84%E5%B8%B8%E8%A7%81%E6%A6%82%E5%BF%B5.png)

对象的定义：
- 无序属性的集合，其属性可以包含基本值、对象或者函数。
- 名值对（键值对）

创建自定义对象的两个方法:
1. 创建一个Object的实例。
2. 对象字面量。

```js
// 创建Object的实例
var person = new Object();
person.name = "Nicholas";
person.age = 29;
person.job = "Software Engineer";
person.sayName = function(){
  alert(this.name);
}
```

```js
// 对象字面量
var person = {
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  sayName: function(){
    alert(this.name);
  }   
};
```

### 属性类型

- 定义只有内部才用的特性（attribute）时，描述了属性（property）的各种特征。
- 如何表示属性的内部值?使用两对方括号，例如： `[[Enumerable]]`。
- 有两种属性类型：**数据属性**、 **访问器属性**。

#### 1、数据属性

数据属性有四个描述其行为的特性：
1. `[[Configurable]]`。表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。
2. `[[Enumerable]]`。表示能否枚举这个属性。
3. `[[Writable]]`。表示能否修改属性的值。
4. `[[Value]]`。读取/写入属性数据值。


**使用`Object.defineProperty()`修改属性的特性。**

- 一旦把属性定义为不可配置的（`configurable: false`），就不能再把它变回可配置了。可以多次调用 `Object.defineProperty()` 方法修改同一个属性，但在把 `configurable`
特性设置为 `false` 之后就会有限制了。
- 一旦使用 `Object.defineProperty` 给对象添加属性，如果不设置属性的特性，那么 `configurable`、`enumerable`、`writable` 这些值都为默认的 `false`

语法：

```js
Object.defineProperty(obj, prop, descriptor)

参数说明：
// obj：必需。目标对象 
// prop：必需。需定义或修改的属性的名字
// descriptor：必需。目标属性所拥有的特性

返回值：
// 传入函数的对象。即第一个参数obj

（descriptor）设置特性的总结：
// value: 设置属性的值
// writable: 值是否可以重写。true | false
// enumerable: 目标属性是否可以被枚举。true | false
// configurable: 目标属性是否可以被删除或是否可以再次修改特性 true | false
```

**参考资料**

- [【sg】理解Object.defineProperty的作用](https://segmentfault.com/a/1190000007434923)
- [【CSDN】扩展js原生对象的正确姿势](https://blog.csdn.net/wp270280522/article/details/45723993)


#### 2、访问器属性

1. `get` 与 `set` 是方法，因为是方法，所以可以进行判断。
2. `get` 是得到一般是要返回的，`set` 是设置不用返回。
3. 如果调用对象内部的属性约定的命名方式是 `_age`。
4. `get` 或 `set` 不是必须成对出现，任写其一就可以。也不是必须的，默认值是 `undefined`。
5. 如果不设置方法，则 `get` 和 `set` 的默认值为 `undefined`。
6. 访问器属性不包含数据值 `[[Value]]`。因为是访问器所以肯定是可以读写的，所以也没有 `[[Writable]]`。

```js
var obj = {};
Object.defineProperty(obj,"newKey",{
    get: function (){}, // undefined
    set: function (value){}, // undefined
    configurable: true, // false
    enumerable: true // false
});

// 注意：当使用了getter或setter方法，不允许使用writable和value这两个属性
```


```js
var p = {
  name: "chen",
  work: function () {
    console.log("wording...");
  },
  _age: 18,
  get age() {
    return this._age;
  },
  set age(val) {
    if (val < 0 || val > 100) { // 如果年龄大于100就抛出错误
      throw new Error("invalid value")
    } else {
      this._age = val;
    }
  }
};
console.log(p.name);
console.log(p.age);
console.log(p.age = 205);
```

- 巧用 `get` 和 `set`，能够直接操作对象属性实现读写，可以极大的提高编程效率

![defineProperty测试题1](./images/defineProperty%E6%B5%8B%E8%AF%95%E9%A2%981.jpg)

```js
var a = (function() {
  var person = {
    name: 'Vincent',
    age: 24,
  };

  return {
    run: function(k) {
      return person[k]
    }
  }
}())

// 答案
Object.defineProperty(Object.prototype, 'self', {
    get: function () {return this;},
    set: function (value) {return this},
    configurable: true
    // 该属性的存在是确保该属性可被delete方法删除
});

var person = o.run('self');
delete Object.prototype.self;
//由于不推荐使用prototype来扩展自己定义的属性（扩展标准规定的除外），这里把扩展再去掉。
```

### 定义多个属性 `Object.defineProperties`

```javascript
var book = {};
Object.defineProperties(book, {
  _year: {
    value: 2004
  },
  edition: {
    value: 1
  },
  year: {
    get: function () {
      return this._year;
    },
    set: function (newValue) {
      if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
      }
    }
  }
});
```

### 读取属性的特性 `Object.getOwnPropertyDescriptor()`

- 取得给定属性的描述符。
- 这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称。
- 返回值是一个对象

```javascript
var book = {};
Object.defineProperties(book, {
  _year: {
    value: 2004
  },
  edition: {
    value: 1
  },
  year: {
    get: function () {
      return this._year;
    },
    set: function (newValue) {
      if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
      }
    }
  }
});

var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
alert(descriptor.value); //2004
alert(descriptor.configurable); //false
alert(typeof descriptor.get); //"undefined"

var descriptor = Object.getOwnPropertyDescriptor(book, "year");
alert(descriptor.value); //undefined
alert(descriptor.enumerable); //false
alert(typeof descriptor.get); //"function"
```

**兼容性**

- ie9+
- vue2不兼容ie8就是因为用了新特性

## 创建对象

### 工厂模式

工厂模式，使用简单的函数创建对象，为对象添加属性和方法，然后返回对象。这个模式后来
被构造函数模式所取代。

```js
function createPerson(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function () {
    alert(this.name);
  };
  return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```


### 构造函数模式

```js
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function () {
    alert(this.name);
  };
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

跟上面的“工厂模式”相比有以下不同点：
- 没有显式地创建对象；
- 直接将属性和方法赋给了 this 对象；
- 没有 `return` 语句。

### 原型模式

```js
function Person() {}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function () {
  alert(this.name);
};

var person1 = new Person();
person1.sayName(); // "Nicholas"

var person2 = new Person();
person2.name = "qbrao";
person2.sayName(); // "qbrao"
alert(person1.sayName === person2.sayName); // true
```

**原型对象的问题**

- 没有传递初始化参数这个环节。
- 对于包含引用类型值的属性来说，所有实例都会共享变化。

### 组合使用构造函数模式和原型模式

- 这种构造函数与原型混成的模式，是目前在 ECMAScript中使用最广泛、认同度最高的一种创建自
定义类型的方法。可以说，这是用来定义引用类型的一种默认模式。
- 使用构造函数定义实例属性，而使用原型定义共享的属性和方法。

```js
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["Shelby", "Court"];
}
Person.prototype = {
  constructor: Person,
  sayName: function () {
    alert(this.name);
  }
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
person1.friends.push("Van");
alert(person1.friends); //"Shelby,Count,Van"
alert(person2.friends); //"Shelby,Count"
alert(person1.friends === person2.friends); //false
alert(person1.sayName === person2.sayName); //true
```

### 动态原型模式

```js
function Person(name, age, job) {
  //属性
  this.name = name;
  this.age = age;
  this.job = job;
  // 方法
  if (typeof this.sayName != "function") {
    Person.prototype.sayName = function () {
      alert(this.name);
    };
  }
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();
```

> 使用动态原型模式时，不能使用对象字面量重写原型。前面已经解释过了，如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。

### 寄生构造函数模式

### 稳妥构造函数模式

## 继承

### 原型链

在通过原型链实现继承时，不能使用对象字面量创建原型方法。

**默认的原型**

所有函数的默认原型都是 Object 的实例，因此默认原
型都会包含一个内部指针，指向 `Object.prototype` 。这也正是所有自定义类型都会继承 `toString()` 、
`valueOf()` 等默认方法的根本原因。

**确定原型和实例的关系**

- `instanceof`
- [`isPrototypeOf() `](https://developer.mozilla.org/zh-CN/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Object/GetPrototypeOf)

**原型链的问题**

- 1、最大的问题是，通过原型链继承的属性如果是引用类型值，会被所有实例共享。
    - 如何解决：在构造函数中定义属性，而不是在原型对象中定义属性。
- 2、在创建子类型的实例时，不能向超类型的构造函数中传递参数。
    - 所以很少单独使用原型链，一般跟构造函数组合使用。

### 借用构造函数

通过使用 `apply()` 和 `call()` 方法也可以在（将来）新创建的对象上执行构造函数。

```js
function SuperType() {
  this.colors = ["red", "blue", "green"];
}
function SubType() {
  // 继承了 SuperType
  SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors); // "red,blue,green,black"

var instance2 = new SubType();
alert(instance2.colors); // "red,blue,green"
```

**借用构造函数的问题**

如果仅仅是借用构造函数，那么也将无法避免构造函数模式存在的问题——方法都在构造函数中定
义，因此函数复用就无从谈起了。借用构造函数也很少单独使用。

### 组合继承

- JavaScript 中最常用的继承模式。
- 将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。
- 其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。

```js
function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function () {
  alert(this.name);
};
function SubType(name, age) {
  //继承属性
  SuperType.call(this, name);
  this.age = age;
}
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function () {
  alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors); //"red,blue,green,black"
instance1.sayName(); //"Nicholas";
instance1.sayAge(); //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors); //"red,blue,green"
instance2.sayName(); //"Greg";
instance2.sayAge(); //27
```

### 原型继承

### 寄生式继承

### 寄生组合式继承

**参考资料：**

- [【未读】一篇文章理解 JS 继承](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651554700&idx=1&sn=fc906e8518d3109de124723adb8a8d08&chksm=8025544db752dd5baf0b44d8561ce06d76f74746640a71e19239ef236e87c5028088f1a4ad34&mpshare=1&scene=24&srcid=08061tvetodVxl2D8MxLi9CC#rd)
- [【未读】JavaScript 的对象继承方式，有几种写法？](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651555303&idx=2&sn=3730b33f019ea6be8bb2ee65de2cc90c&chksm=80255226b752db30aed465535ea5d283959c3a27ff3d78fc2f80a8bd37357fa2a56fc72b2fa2&mpshare=1&scene=24&srcid=1112LmRq1vjOWETegiOp0p9F&key=798968cdb5a0aac41c999fcb80170987f1e276d5a60999c52d6f2aa0a3aae2f51008c4e79fc4861de9aff61f41a17bd3938ff3314e80d17f4871d8636c04d7ccadf043c543f61d347259a23c1d7ab1fe&ascene=14&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=ZoqXnMgi0AAEna540iG5qPugeLgO5W7T7qkyPIntCT9Pu1xlW6JZWbtLnqd%2FcXea)
- [【未读】认识原型对象和原型链](https://www.cnblogs.com/onepixel/p/5024903.html)
- [【未读】前端基础进阶（九）：详解面向对象、构造函数、原型与原型链](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651552061&idx=2&sn=c7aa5328a7700680b0a43e9cf57d44ca&chksm=8025aefcb75227ea338794252fb732c9e606e8d665457c214f5e2d086eccb7e45135c8983837&scene=0&key=00a8e91eefd868fc3fd6092db170a4229fe844f49cdf5fd26919332d3c52b7a8f9631c7f124b7830bfbd265c9181a0f0ae643719205abba35a6d887b4a9897bf6ff1eb09882f325244f15a3a34f149a7&ascene=14&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=ZoqXnMgi0AAEna540iG5qPugeLgO5W7T7qkyPIntCT9Pu1xlW6JZWbtLnqd%2FcXea)
- [【未读】面试官问：JS 的继承](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651555940&idx=1&sn=f65e7d7a1f73953f27c574776dd388f1&chksm=80255fa5b752d6b307bc417bac43ef6d78032d1d0a65085e9d402e7035ab91aed3585711cb73&scene=0&xtrack=1&key=00a8e91eefd868fc8dc0ee4ceda31da91d1a59bc6ea6bfdb8930bef8fc62f03d73edf6389f8622384ca387047d95abf8e5bb4d68c58de32f10d3ce0d4f6a8f48160b2e199d74d73781d018734f9b2e15&ascene=14&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=ZoqXnMgi0AAEna540iG5qPugeLgO5W7T7qkyPIntCT9Pu1xlW6JZWbtLnqd%2FcXea)

## JavaScript 构造函数、继承、原型

**参考资料:**

- [JavaScript 世界万物诞生记](https://zhuanlan.zhihu.com/p/22989691)
- [javascript王国的一次旅行，一个没有类的世界怎么玩转面向对象？](https://juejin.im/entry/6844903488476938254)
- [深度解析原型中的各个难点](https://github.com/KieSun/Dream/issues/2)

### 万物皆对象

- 万物皆对象？
- 对象都有模版；
- 使用机器按照模版批量生成对象；
- 每个对象不仅有模版的特性，还可以拥有自己的特性。

### 关键字

- `prototype`
- `__proto__`
- 继承
- 原型链
- 构造函数
- 原型对象
- 实例对象
- `constructor` 属性
- `Object.prototype`
- `Function.prototype`
- `instanceof`

**构造函数**

- 英文为 constructor

> 构造器其实就是一个普通的函数。当使用 new 操作符来作用这个函数时，它就可以被称为构造方法（构造函数）。
> <br>—— MDN

**原型链**

原型链有点像嵌套的作用域链。

### `prototype`

- `prototype` 属性，意为“原型”，准确的说应该是“原型对象”。
- `xx.prototype` 指向的是原型对象，所以看到 `prototype` 就可以说是“xx的原型对象”。
- 只有函数才拥有 `prototype` 属性。基本上所有的函数都具有这个属性，除了下面这种方式创建的函数。
- `prototype` 就是上面所说的“模版”，构造函数就是“机器”。

```js
let fun = Function.prototype.bind()

fun.prototype // undefined
```

当我们申明一个函数的时候，`prototype` 属性就自动生成了。

![prototype图1](./images/prototype%E5%9B%BE1.png)

由上图我们可以看到有个 `constructor` 属性。这个属性的值是 `foo`。

```js
foo.prototype.constructor === foo // true
```

> `constructor` 属性没有特殊的用图，让实例对象知道是什么函数构造了它。

**这里由一个疑问，为什么普通函数的构造函数是他自己？**

再看下面的构造函数：

```js
function F() {}
var f = new F();

f.__proto__ === F.prototype // true;

// f 是实例，F 是构造函数，F.prototype 是原型对象
```

一旦我们修改函数的 `prototype` 属性的值， 就没有 `constructor`属性了。

![prototype图2](./images/prototype%E5%9B%BE2.png)

### `__proto__`

- `__proto__` 可以叫做“继承”。
- `__proto__` 是一个内部属性 `[[prototype]]`，外部无法访问，但是可以通过 `__proto__` 来访问。
- 每一个实例对象，都有一个 `__proto__` 属性指向它的构造函数的原型对象（prototype） 。

```js
var one = {x: 1};
var two = new Object();
one.__proto__ === Object.prototype // true
two.__proto__ === Object.prototype // true
one.toString === one.__proto__.toString // true
```

由上面的代码可以看出 `__proto__` 指向的是原型对象。

那么， 实例对象的 `__proto__` 是如何产生的？

`__proto__` 的实现大致是这样：

```js
Object.defineProperty(Object.prototype, " __proto__", {
  get: function () {
    return Object.getPrototypeOf(this);
  },
  set: function (o) {
    // ES6中的setPrototypeOf(..)              
    Object.setPrototypeOf(this, o);
    return o;
  }
});

// 上面的代码参考《你不知道的JavaScript（上卷）》
```


再看下创建函数的 3 种方式：

1. 申明式

```js
function fn() { }
```

2. 函数字面量

```js
let fn = function() { }
```

3. 使用 `new Function`

```js
let fn = new Function(arg1, arg2, …, argN, body)
let fn = new Function('x', 'y', 'return x+y')

// arg 为函数参数
// body 为函数体
```

第3种方式，通常不会用到。但是第3种方式才是真正创建函数的方式。

- `fn` 这个函数是 Function 的实例对象
- `function` 就是一个语法糖，内部调用了 `new Function()`

说到这我们来看看 `new` 的原理。

### `new` 原理

使用 `new` 关键字实例化一个对象时，有哪[几个步骤](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)？

1. 创建一个空对象`{}`；
2. 将该对象链接到（继承）构造函数的原型；
3. 将步骤1新创建的对象作为 this 的上下文；
4. 返回这个新对象。

`new` 其实也是语法糖。就是避免每次实例化对象时都要写上面四个步骤的代码，现在一个 `new` 就搞定了。

如果实在搞不清楚 new 做了什么可以看[JS 的 new 到底是干什么的？](https://zhuanlan.zhihu.com/p/23987456?utm_medium=social&utm_source=wechat_session)。

现在我们用代码实现 `new`：

第一种方式实现

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.info = function() {
  console.log(`${this.name}: ${this.age}岁`, "Person");
}

function newFun(fn, ...rest) {
  let instance = {};
  instance.__proto__ = fn.prototype;
  fn.apply(instance, rest)
  return instance;
}

const person1 = new Person("张三", 13);
person1.info(); // 张三: 13岁
const person2 = newFun(Person, "李四", 20);
person2.info(); // 李四: 20岁

// info() 方法是从原型对象上继承来的
```

第二种方式实现

```js
Function.prototype.construct = function(...args) {
  const o = Object.create(this.prototype);
  this.apply(o, args)

  return o;
}

function Parent(name, age, sex) {
  this.name = name;
  this.age = age;
  this.sex = sex;
}

Parent.prototype.getName = function() {
  return this.name;
}

let child = Parent.construct("张三", 22, "男");
```

还有其他实现方式可以去看下 MDN 上 [`apply` 的用法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)。

结论：`new` 是在创建对象，并且把生成的对象跟原型链接起来，也就是继承。

当然，上面的代码只是为了揭示 `new` 背后的原理，还有一些细节和边界条件没有处理。如果感兴趣可以看下面的参考资料。

- [javascript中，new操作符的工作原理是什么?](https://www.zhihu.com/question/36440948)
- [详解 JS 中 new 调用函数原理](https://juejin.im/post/6844903630605123598)

**想一想，基本类型是对象吗？**

当我们申明一个变量的时候通常会这样写，`let s = "s"` 或者 `let s = String("s")`。

答案：基本类型（string、number、boolean）[并不是对象](https://zhuanlan.zhihu.com/p/65059409)。

> 与其说 JavaScript 中万物皆对象，不如说 JavaScript 中万物皆可以是对象。

所以 v8 引擎会把基本类型包装一下，让它成为一个对象，然后我们可以对它进行操作。

> 实际上，每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而让我们能够调用一些方法来操作这些数据。<br>
> JavaScript 基本包装类型，它的作用是方便操作基本类型值。<br>
> ——《JavaScript高级程序设计第3版》

```js
var s = "s";
var s1 = s.substring(2);
```

当我们使用基本类型上的方法时，其实经历了下面几个步骤。

```js
var s = "s";
// (1) 创建String类型的一个实例；
var s2 = new String(s);
// (2) 在实例上调用指定的方法；
var s1 = s2.substring(2);
// (3) 销毁这个实例。
s2 = null; 
```

**再想一想，函数是对象吗？**

答案：函数是对象。

>  函数就是对象的一个子类型（从技术角度来说就是“可调用的对象”）。JavaScript中的函数是“一等公民”，因为它们本质上和普通的对象一样（只是可以调用），所以可以像操作其他对象一样操作函数（比如当作另一个函数的参数）。
> <br>——《你不知道的JavaScript（上卷）》


### `Object.prototype`、`Function.prototype`

![构造函数-原型对象-实例关系图1](./images/%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0-%E5%8E%9F%E5%9E%8B%E5%AF%B9%E8%B1%A1-%E5%AE%9E%E4%BE%8B%E5%85%B3%E7%B3%BB%E5%9B%BE1.png)

![构造函数-原型对象-实例关系图2](./images/%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0-%E5%8E%9F%E5%9E%8B%E5%AF%B9%E8%B1%A1-%E5%AE%9E%E4%BE%8B%E5%85%B3%E7%B3%BB%E5%9B%BE2.jpg)

由上面这张图我们可以得到：

对象的原型从 `null` 派生而来。
```js
Object.prototype.__proto__ === null // true
```

函数的原型从对象的原型而来。
```js
Function.prototype.__proto__ === Object.prototype // true
```

制造函数的函数（`new Function()`）从函数的原型而来。
```js
Function.__proto__ === Function.prototype
```

制造对象的函数被制造函数的函数（`new Function()`）创造而来。
```js
Object.__proto__ === Function.prototype // true
Array.__proto__ === Function.prototype // true
String.__proto__ === Function.prototype // true
Number.__proto__ === Function.prototype // true
Boolean.__proto__ === Function.prototype // true
```

```js
var fun = function(a) {return a}
// 等价与下面的方式
// var fun = new Function("a", "return a");
fun.__proto__ === Function.prototype // true
```

对象由构造函数创造而来。
```js
var obj = {a: 1};
obj.__proto__ === Object.prototype // true

var s1 = "1";
s1.__proto__ === String.prototype // true
```

- `Object.prototype`、`Function.prototype` 是系统自己生成的。
- 通过 `Function.prototype` 构造出来了 `Object`、`Function` 。
- 函数也是从对象而来，所以函数也是一个特别的对象。

### 构造函数、原型和实例的关系

**原型对象**

无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 prototype
属性，这个属性指向函数的原型对象。

![构造函数-原型对象-实例关系图3](./images/%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0-%E5%8E%9F%E5%9E%8B%E5%AF%B9%E8%B1%A1-%E5%AE%9E%E4%BE%8B%E5%85%B3%E7%B3%BB%E5%9B%BE3.png)

- `Person.prototype` 指向了原型对象，而 `Person.prototype.constructor` 又指回了 `Person` 。
- `Person` 的每个实例——
`person1` 和 `person2` 都包含一个内部属性 `__proto__`，该属性仅仅指向了 `Person.prototype` ；换句话说，它们与构造函数没有直接的关系。（实例中的指针仅指向原型，而不指向构造函数。）

**构造函数、原型和实例的关系**

每个构造函数都有一个原型对象 `prototype`，原型对象都包含一个指向构造函数的指针 `constructor`，而实例都包含一个指向原型对象的内部指针`__proto__`。

### `instanceof` 原理

语法：实例 `instanceOf` 构造函数

> `instanceof` 运算符用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的**原型链**上。
> <br> ——MDN

> `a instanceof Foo`
> 
> `instanceof` 回答的问题是：在 `a` 的整条 `[[Prototype]]` 链中是否有指向 `Foo.prototype` 的对象？

通过修改实例的 `__proto__` 或构造函数的 `prototype` ，可以改变 `instanceof` 的值，所以它的值不是固定的。

我们来写一个 `instance_of` 函数

```js
function instance_of(l, r) {
    let l = l.__proto__;
    const o = r.prototype;
    
    while(true) {
        if (l === null) return false
        if (l === o) return true
        l = l.__proto__
    }
}
```

当我们使用基本类型的测试的时候，发现跟原生的 `instanceof` 有区别。

```js
let s = 1;
console.log(instance_of(s, Number)); // true
console.log(s instanceof Number); // false

// 因为第二步 l = l.__proto__ 的时候，对 l 进行了重新赋值，
// 所以需要改进一下，在最上面判断一下是否为基本类型即可。
```

`instanceof` 的缺点是只能判断对象跟函数之间的关系，无法判断两个对象之间的关系。

可以使用 `isPrototypeOf()` 来解决这个问题。

### `isPrototypeOf()`、`getPrototypeOf()`、`setPrototypeOf()`

**[isPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Object/isPrototypeOf)**

语法：

```js
prototypeObj.isPrototypeOf(object)
```

> `isPrototypeOf()` 与 `instanceof` 运算符不同。在表达式 "object instanceof AFunction"中，object 的原型链是针对 AFunction.prototype 进行检查的，而不是针对 AFunction 本身。 ——MDN

```js
// 检查 baz 对象是否继承自 Foo.prototype
// 或者说，Foo.prototype 是否在 baz 对象的原型链上
Foo.prototype.isPrototypeOf(baz)
```

总结一下：

- `isPrototypeOf` 用来判断构造函数的原型对象跟实例对象的关系。
- `isPrototypeOf` 用来判断两个对象是否有继承关系。

**[getPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Object/GetPrototypeOf)** 

返回值是对象的原型，不会在原型链上查找。如果没有继承属性，则返回 null 。

```js
Object.prototype === Object.getPrototypeOf( {} ) // true
```


### `Object.create()` 原理

> [`Object.create()`](https://developer.mozilla.org/zh-CN/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Object/create) 方法创建一个新对象，使用现有的对象来提供新创建的对象的 `__proto__`。
> <br>—— MDN

```js
let 对象 = Object.create(原型, 对象属性);

// Object.create 返回一个新对象
// 新对象的 __proto__ 是“原型”
```

验证一下上面的说法：

```js
const Foo = function() {}
const Bar = function() {}
Foo.prototype = Object.create(Bar.prototype);
const f = new Foo();

console.log(f instanceof Foo); // true
console.log(f instanceof Bar); // true

console.log(Object.getPrototypeOf(f) === Foo.prototype) // true
console.log(Object.getPrototypeOf(f) === Bar.prototype) // false

console.log(Foo.prototype.isPrototypeOf(f)); // true
console.log(Bar.prototype.isPrototypeOf(f)); // true
```

[`Object.create()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create) 的原理，MDN 上面有写出，去掉一些边界判断，看核心原理。

```js
Object.myCreate = function (proto) {
  function F() {}
  F.prototype = proto;

  return new F();
}
```

测试上面的方法

```js
function Parent() {
  this.name = "张三"
}

Parent.prototype.getName = function() {
  return `名字：${this.name}`;
}

let people = Object.myCreate(new Parent());
let p = Object.myCreate(people);

console.log(people.name) // 张三
console.log(people.getName()) // 名字：张三

console.log(p.name) // 张三
console.log(p.getName()) // 名字：张三
```

看下下面两种写法

```js
Foo.prototype = Bar.prototype
Foo.prototype = Object.create(Bar.prototype);

// 上面这种写法，修改 Foo.prototype 会影响 Bar.prototype
// 下面这种就不会
// 如果还有问题可以看看《你不知道的JavaScript（上卷）5.3》。
```

> `Object.create(null)` 会创建一个拥有空（或者说null）[[Prototype]]链接的对象，这个对象无法进行委托。由于这个对象没有原型链，所以instanceof操作符（之前解释过）无法进行判断，因此总是会返回false。这些特殊的空[[Prototype]]对象通常被称作“字典”，它们完全不会受到原型链的干扰，因此非常适合用来存储数据。
> <br>—— 《你不知道的JavaScript（上卷）5.4》。

## 构造函数的返回值

参考资料：

- [js 构造函数的返回值](https://www.cnblogs.com/kenanyah/p/13246934.html)
- [JavaScript之构造函数内有return详解](https://blog.csdn.net/SpicyBoiledFish/article/details/70858565)

函数作为构造函数(使用new调用)，如果没有return语句，或者return后是基本数据类型，会将this作为返回值；反之如果return了对象，则以此对象为返回值。

## Class

- ES6 的 `class` 可以看作只是一个语法糖。
- 完全可以看作构造函数的另一种写法。
- `class` 必须使用 `new` 调用，否则会报错。
- `class` 也可以使用表达式的方式来声明。
- 类和模块的内部，默认就是严格模式。
- `class` 不存在提升，所以跟 `var` 和 `function` 声明不太一样。

使用函数也可以达到跟 `class` 相同的效果为什么要使用 `class`:

- 语义化。告诉使用者这个类有什么方法属性。如果不使用面向对象的写法，当操作某个对象的时候，需要知道很多东西，比如哪些方法能操作这个对象，这些方法函数在哪里等等。维护起来也不方便。
- 分类。（封装）
- 快速创建对象。（实例化对象）

```js
class Point {
  // 实例属性的新写法
  // 这样写的好处：
  // 1.不需要在实例属性前面加上 this
  // 2.所有实例对象自身的属性都定义在类的头部，看上去比较整齐，一眼就能看出这个类有哪些实例属性。
  _count = 0;

  // 静态属性
  // 跟实例属性恰恰相反，只能在类上使用，无法在实例上使用
  static staticProp = 1;

  // 私有属性
  // 只能在类的内部用 this 使用，外部使用就会报错。
  // 私有属性不限于从this引用，只要是在类的内部，实例也可以引用私有属性。
  // this.#name 和 this.name 是两个属性
  #name = "jay";

  // 私有方法
  #privateMethod() {
    console.log("这是一个私有方法")
  }

  // 私有 getter 和 setter
  get #prop() {
    return this._count;
  }
  set #prop(value) {
    console.log('私有setter: '+ value);
  }
  
  // 构造方法
  // this 关键字则代表实例对象
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  // 不需要 function 关键字
  // 方法之间不需要逗号分隔，加了会报错
  // 这样定义的方法都在原型上面
  prototypeMethod() {
    return '(' + this.x + ', ' + this.y + ')';
  }
  
  // getter 和 setter
  get prop() {
    return this._count;
  }
  set prop(value) {
    console.log('setter: '+ value);
  }
  
  // 静态方法
  // 表示该方法不会被实例继承，而是直接通过类来调用，
  // 意思就是实例使用会报错，只有类才能使用
  // let instance = new Point()
  // instance.staticMethod() 这样使用会报错
  // Point.staticMethod() 只能这样调用
  // 并且类中的 this 指向的类，并不是实例
  // 父类的静态方法会被子类继承
  static staticMethod() {
    
  }
}
```














