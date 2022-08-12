<!-- TOC -->

- [this、apply、call、bind](#thisapplycallbind)
  - [this](#this)
  - [call、apply、bind 区别](#callapplybind-区别)
  - [call、apply、bind 常见用法](#callapplybind-常见用法)
  - [`call` 原理](#call-原理)
  - [`Function.prototype.bind()` 的原理](#functionprototypebind-的原理)

<!-- /TOC -->

# this、apply、call、bind

## this

**[`this`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this) 是如何工作的？**

如果要判断一个运行中函数的 `this` 绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面这四条规则来判断 `this` 的绑定对象。

1. 由 `new` 调用？绑定到新创建的对象。
2. 由 `call` 或者 `apply`（或者 `bind`）调用？绑定到指定的对象。
3. 由上下文对象调用？绑定到那个上下文对象。
4. 默认：在严格模式下绑定到 `undefined`，否则绑定到全局对象。

ES6 中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 `this`，具体来说，箭头函数会继承外层函数调用的 `this` 绑定（无论 `this` 绑定到什么）。这其实和 ES6 之前代码中的 `self = this` 机制一样。

上述资料引用自[《你不知道的JavaScript（上卷）》](https://weread.qq.com/web/bookDetail/8c632230715c01a18c683d8)

在事件中，`this` 指向触发这个事件的对象，特殊的是，IE 中的 `attachEvent` 中的 `this` 总是指向全局对象 `Window` 。


## call、apply、bind 区别

语法：

```js
func.call(thisArg, arg1, arg2, ...)
func.apply(thisArg, [argsArray])
func.bind(thisArg[, arg1[, arg2[, ...]]])
```

> `call()` 方法的作用和 `apply()` 方法类似，区别就是 `call()` 方法接受的是参数列表，而 `apply()` 方法接受的是一个参数数组。
> <br><br>
> [`bind()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
> <br> —— MDN

`bind()` 跟 `call()` 一样，唯一的区别是，`bind()` 不会立即被执行。

## call、apply、bind 常见用法

**改变this指向**

这就是这三个方法最重要的作用。

```js
const obj1 = {
  name: "小王子",
}

const obj2 = {
  name: "小约翰",
  getName() {
    return this.name;
  }
}

const name1 = obj2.getName.call(obj1);
const name2 = obj2.getName();
console.log(name1); // 小王子
console.log(name2); // 小约翰
```
**调用函数**

```js
function  f() {
  console.log("我被调用了")
}
f.call();
// 如果不加参数，好像没有什么卵用。
// 跟执行 f() 是一样的效果。
```

**将类数组对象转换成数组对象**

```js
function test (a, b, c) {
    console.log(arguments)
    let result = Array.prototype.slice.call(arguments);
    console.log(result);
}
test(1,2,3);
// 改变 Array 的 this 指向
// 让 arguments 类数组可以使用数组的 slice 方法
// 执行 slice 方法后，返回一个新的数组
```

**借用别人的方法**

```JS
// 找出最大值
const numbers = [1,2,3,4,5,6];
const max = Math.max.apply(null,numbers);
console.log(max);
// 其实跟上面那个例子是一样的
```

**继承**

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.getPersonInfo = function() {
    return `姓名：${this.name}，年龄：${this.age}`;
  }
}

function Student(name, age, sex) {
  Person.apply(this, arguments);
  this.sex = sex;
  this.getStudentInfo = function() {
    return `姓名：${this.name}，年龄：${this.age}，性别：${this.sex}`;
  }
}

Person.prototype.getPerson = function() {
  return "getPerson"
}

Student.prototype.getStudent = function() {
  return "getStudent"
}

const zs = new Student("张三", 20, "男");
console.log(zs.getPersonInfo()); // 姓名：张三，年龄：20
console.log(zs.getStudentInfo()); // 姓名：张三，年龄：20，性别：男
console.log(zs.getStudent()); // getStudent
console.log(zs.getPerson()); // Uncaught TypeError: zs.getPerson is not a function

const ls = new Person("张三", 20, "男");
console.log(ls.getPerson()); // getPerson
```

从上面的例子其实可以看到，这种写法无法继承到原型链上的方法和属性。

> 原因是这样写只改变了 this 指向，并没有修改原型链。具体要怎么写参考 new 的原理。

**this 被忽略**

有时候我们会遇到这种情况 `Math.max.apply(null, [1,2,3,4])`。对象为 `null` 或者 `undefined`。

> 如果函数并不关心 `this` 的话，需要传一个占位值，就可以传 `null` 。

**总结**

```js
方法.call(对象, 参数)
Function.prototype.方法.call(对象, 参数)

// 可以看做：对象执行了某个原本不具备的方法，也可以说执行了方法但是修改了他的 this 指向为某个对象。
// 如果是 bind，则是对象拥有了原来不具备的方法，但是方法没有执行。
```

## `call` 原理

一句话介绍 call：

> `call()` 方法在使用一个指定的 `this` 值和若干个指定的参数值的前提下调用某个函数或方法。

这篇文章 [JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11) 对 `call` 和 `apply` 原理讲的很通俗易懂，就不多说了，总结下实现原理：

1. 将函数设为对象的属性
2. 执行这个函数
3. 删除这个函数

代码实现：

```js
Function.prototype.call1 = function(context) {
  // 首先要获取调用 call 的函数，用 this 可以获取
  context.fn = this;
  context.fn();
  delete context.fn;
}

// 测试一下
var foo = {
  value: 1
};

function bar() {
  console.log(this.value);
}

bar.call1(foo); // 1
```

还需要解决3个问题：

1. 当对象为 `null`、`undefined`，需要处理；
2. `call` 传参问题；
3. 函数可能有返回值。

代码实现：

```js
Function.prototype.call2 = function(context, ...args) {
  // 处理 null 和 undefined
  context = context || window;

  context.fn = this;
  // 处理传参
  const result = context.fn(...args);
  delete context.fn;

  // 处理返回值
  return result;
}

// 测试一下
var foo = {
    value: 1
};

var value = 2;

function bar(...arg) {
  if (arg.length) {
    console.log(arg)
  } else {
    console.log(this.value);
  }

  return arg;
}

bar.call2(null); // 2
bar.call2(foo); // 1
const result = bar.call(foo, 1, 2, 3);
console.log(result) // [1,2,3]
```

`apply` 的原理跟 `call` 的原理一样，只是函数参数的格式不同，就不赘述了。

## `Function.prototype.bind()` 的原理

`bind` 的原理可以去 MDN 看看 Polyfill 的实现，也可以看看这个[讲解](https://github.com/mqyqingfeng/Blog/issues/12)。

跟 `call` 不同的是，使用 `bind` 后函数并没有立即执行，需要手动调用。

简单说下 bind 的原理：

> 使用闭包返回一个匿名函数，当中只有一行代码，<br>
> 执行函数，并且 `this` 指向传入对象。

代码实现：

```js
Function.prototype.bind1 = function(context) {
  const that = this;

  return function() {
    that.apply(context);
  }
}

// 测试
const obj = { name: 1 }
function foo() {
  console.log(this.name);
}

const bindFoo = foo.bind1(obj);
bindFoo(); // 1
```

还需要解决 2 个问题：

- 函数传参
- 将返回的函数当做构造函数使用。 `const newFun = new f.call(obj)`

**解决函数传参问题**

```js
Function.prototype.bind2 = function () {
  var slice = Array.prototype.slice;
  var thatFunc = this,
    thatArg = arguments[0];
  var args = slice.call(arguments, 1);

  return function () {
    var funcArgs = args.concat(slice.call(arguments))
    return thatFunc.apply(thatArg, funcArgs);
  };
};

// 测试
const obj = { name: 1 }
function foo() {
  console.log(Array.from(arguments));
}

const bindFoo = foo.bind2(obj, 1, 2);
bindFoo(3, 4); // [1,2,3,4]
```

**解决 `bind` 返回的函数当成构造函数使用的问题**

当 bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效。

所以这里要知道 `new` 做了什么，也就是：

- 修改 `this` 的指向
- 修改 `prototype` 的指向

修改之后的代码：

```js
Function.prototype.bind3 = function() {
  const that = this;
  const [obj, ...args] = Array.from(arguments);

  const fBound = function() {
    // 拼装 arguments
    const realArgs = [...args, ...Array.from(arguments)];
    // 这里判断是普通函数，还是构造函数的方式使用
    // this 是 fBound 的实例对象,则说明是构造函数，
    // 否则是普通方式的调用
    const realObj = this instanceof fBound ? this : obj;
    that.apply(realObj, realArgs);
  }

  // 修改返回函数的原型对象，实现继承
  fBound.prototype = this.prototype;

  return fBound;
}

// 测试
const value = "value1";

const obj = { value: "value2" }
function foo(name, age) {
  this.name = name
  this.age = age
  console.log(this.value)
  console.log(this.name)
  console.log(this.age)
  console.log(this.sex)
}

foo.prototype.sex = "男"

// 测试原生 bind 跟 bind3 的表现是否相同
const bindFoo = foo.bind3(obj, "王麻子", 24);
// const bindFoo = foo.bind(obj, "王麻子", 24);
const newFoo = new bindFoo();
```

上面的代码还可以优化，并且有些边界还需要判断，最终的实现参照 MDN 。