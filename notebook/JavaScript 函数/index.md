<!-- TOC -->

- [JavaScript 函数](#javascript-函数)
  - [定义函数的两种方式](#定义函数的两种方式)
  - [递归](#递归)
  - [尾递归](#尾递归)
  - [闭包](#闭包)
    - [闭包是什么？](#闭包是什么)
    - [变量的生存周期](#变量的生存周期)
    - [变量的作用域](#变量的作用域)
    - [词法作用域](#词法作用域)
    - [如何创建一个闭包？](#如何创建一个闭包)
    - [闭包的用途？](#闭包的用途)
    - [使用闭包的注意事项](#使用闭包的注意事项)
    - [练习题](#练习题)
  - [防抖和节流](#防抖和节流)
    - [函数防抖(debounce)](#函数防抖debounce)
    - [函数节流(throttle)](#函数节流throttle)

<!-- /TOC -->

# JavaScript 函数

**参考资料**

- [【阮一峰】学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
- [深入理解闭包之前置知识→作用域与词法作用域](https://juejin.im/post/5afb0ae56fb9a07aa2138425)
- [闭包详解一](https://juejin.im/post/5b081f8d6fb9a07a9b3664b6)
- [闭包详解二：JavaScript中的高阶函数](https://juejin.im/post/5b167b476fb9a01e5b10f19b)
- [JavaScript 闭包](https://juejin.im/entry/57d60f7067f3560057e37e25)
- 《JavaScript设计模式与开发实践》
- [教你分分钟学会javascript闭包操作](https://zhuanlan.zhihu.com/p/25309076)

## 定义函数的两种方式

1. 函数申明。函数申明提升，跟变量提升差不多。
```javascript
function funName(arg0, arg1, arg2) {
    // 函数体
}

```

2. 函数表达式。因为 `function` 关键字后面没有标识符，所以是**匿名函数**。
```javascript
let funName = function (arg0, arg1, arg2) {
    // 函数体
}
```

下面代码定义并立即调用了一个匿名函数。将函数声明包含在一对圆括号中，表示它实际上是一个函数表达式。而紧随其后的另一对圆括号会立即调用这个函数。

```javascript
(function(){
  // 这里是块级作用域
})();
```

这是一个名字是 `g` 的 function expression，然后又被赋值给了变量 `f`。

```js
var f = function g(){ return 23; };
typeof g();//报错
```


这里的函数名 g 和被其赋值的变量 f 有如下差异：

- 函数名 g 不能变动，而变量 f 可以被重新赋值；
- 函数名 g 只能在函数体内部被使用，试图在函数外部使用 g 会报错的。

**参考资料**：

- [使用函数表达式时，匿名函数和命名函数有区别吗？](https://segmentfault.com/q/1010000008209350)

## 递归

**递归的概念**

- 在程序中直接或间接的调用自己。
- 需要一个终止条件，跳出递归，得出结果。

**递归的优点**

- 可以使代码更精简。
- 锻炼递归思维。

**使用场景**

- 构建和遍历树状数据

**递归的步骤(技巧)**

1. 假设递归函数已经写好
2. 寻找递推关系
3. 将递推关系的结构转换为递归体
4. 将终止条件加入到递归体中


例：求1-100的和

```
// 1. 假设递归函数已经写好
let result = sum(100);

// 2. 寻找递推关系
let result = sum(100 - 1) + 100;

// 3. 将递推关系的结构转换为递归体
function sum(n){
  return sum(n-1) + n;
}

// 4. 将终止条件加入到递归体中
function sum(n){
  if(n === 1) return 1;
  return sum(n-1) + n;
}
```

```js
console.log(
  (function f(n) {
    return ((n > 1) ? n * f(n - 1) : n)
  })(5)
);

// 调用链：
n = 5 =>5 * f(4) => 4 * f(3) => 3 * f(2) => 2 * f(1)

// 返回链：
2 * f(1) = 2 * 1 => 3 * 2 * 1 => 4 * 3 * 2 * 1 => 5 * 4 * 3 * 2 * 1
```

**参考资料：**

- [JavaScript中的递归](https://juejin.im/post/5948c0d8fe88c2006a939e2a)
- [JS中的递归](http://www.cnblogs.com/liu666/p/5745301.html)
- [了解 JavaScript 的递归](https://juejin.im/post/5abb5a826fb9a028be360b37)

## 尾递归

**参考资料：**

- [JavaScript 调用栈、尾递归和手动优化](https://juejin.im/entry/6844903479845060616)
- [尾调用优化](https://www.ruanyifeng.com/blog/2015/04/tail-call.html)
- [尾递归以及在浏览器中的实现](https://blog.csdn.net/JimmyLuo17/article/details/54563739)
- [Javascript中的尾递归及其优化](https://zhuanlan.zhihu.com/p/47155064)

**调用栈**

普通递归会爆栈

**尾调用**

函数执行的最后一步是将另外一个函数调用并返回。

**尾递归**

函数最后一步调用自己并返回。

**浏览器实现**

将递归执行转为循环执行。


## 闭包

### 闭包是什么？

> 闭包是指有权访问另一个函数作用域中的变量的函数。 ——[【MDN】闭包](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)

> 闭包的原理，就是把闭包函数的作用域链保存了下来。 ——[【Alloy Team】搞懂闭包](http://www.alloyteam.com/2019/07/closure/)

> 对于那些有一点 JavaScript 使用经验但从未真正理解闭包概念的人来说，理解闭包可以看作是某种意义上的重生。<br>
> ——《你不知道的JavaScript》

**闭包可以访问三个作用域中的变量：**

- 在自己作用域中声明的变量；
- 在父函数中声明的变量；
- 在全局作用域中声明的变量。

```js
var globalVar = "abc";

// 自调用函数
(function outerFunction (outerArg) { // outerFunction作用域开始
  // 在outerFunction函数作用域中声明的变量
  var outerFuncVar = 'x';    
  // 闭包自调用函数
  (function innerFunction (innerArg) { // innerFunction作用域开始
    // 在innerFunction函数作用域中声明的变量
    var innerFuncVar = "y";
    console.log(         
      "outerArg = " + outerArg + "\n" +
      "outerFuncVar = " + outerFuncVar + "\n" +
      "innerArg = " + innerArg + "\n" +
      "innerFuncVar = " + innerFuncVar + "\n" +
      "globalVar = " + globalVar);
  // innerFunction作用域结束
  })(5); // 将5作为参数
// outerFunction作用域结束
})(7); // 将7作为参数


// -> outerArg = 7
// -> outerFuncVar = x
// -> innerArg = 5
// -> innerFuncVar = y
// -> globalVar = abc
```

### 变量的生存周期

> 全局变量的生存周期当然是永久的，除非我们主动销毁这个全局变量。
> 而对于在函数内用 var关键字声明的局部变量来说，当退出函数时，这些局部变量即失去了它们的价值，它们都会随着函数调用的结束而被销毁。


### 变量的作用域

- 作用域就是一套规则，用于确定在何处以及如何查找变量（标识符）的规则。
- 作用域就是查找变量的地方。
- 闭包的作用域链包含着它自己的作用域，以及包含它的函数的作用域和全局作用域。
- 顺着一条链条从下往上查找变量，这条链条，我们就称之为**作用域链**。

![变量的作用域1](./images/%E5%8F%98%E9%87%8F%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F1.png)

![变量的作用域2](./images/%E5%8F%98%E9%87%8F%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F2.png)

### 词法作用域

> 简单地说，词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写代码时将变量和块作用域写在哪里来决定的，因此当词法分析器处理代码时会保持作用域不变（大部分情况下是这样的）——《你不知道JavaScript上卷》

![变量的词法作用域](./images/%E5%8F%98%E9%87%8F%E7%9A%84%E8%AF%8D%E6%B3%95%E4%BD%9C%E7%94%A8%E5%9F%9F.png)

- ❶ 包含着整个全局作用域，其中只有一个标识符：foo。 
- ❷ 包含着foo所创建的作用域，其中有三个标识符：a、bar和b。
- ❸ 包含着bar所创建的作用域，其中只有一个标识符：c。

词法作用域，取决于函数的定义位置；动态作用域，取决于函数被调用的位置。

```js
var value = 1;

function foo(){
  console.log(value);
}

function bar(){
  var value=2;
  foo();
}

bar();//1
// JavaScript 语言使用的是词法作用域，所以打印出来的是 1
// 如果使用动态作用域的语言（bash等），打印出来的则是 2
```

既然 JavaScript 语言使用的是词法作用域为啥要知道动态作用域呢？那是因为当我们在函数中使用 `this` 的时候，`this` 的指向是动态的，会随着函数的调用位置变化而变化。

所以这里要说一下箭头函数。

> 对于普通函数来说，内部的this指向函数运行时所在的对象，但是这一点对箭头函数不成立。它没有自己的this对象，内部的this就是定义时上层作用域中的this。也就是说，箭头函数内部的this指向是固定的，相比之下，普通函数的this指向是可变的。 ——《ECMAScript 6 入门》 阮一峰

参考资料：[深入理解javascript作用域系列第一篇——内部原理](https://www.cnblogs.com/xiaohuochai/p/5699739.html)

### 如何创建一个闭包？

**闭包的特点：**

1. 函数内再嵌套函数
2. 内部函数可以引用外层的参数和变量
3. 参数和变量不会被垃圾回收机制回收

> 创建闭包的常见方式，就是在一个函数内部创建另一个函数。

### 闭包的用途？

- 自执行函
- 特权函数（不懂）

**1、使用闭包封装私有变量**

这种方法把一些变量封装在闭包的内部，只暴露一些接口跟外界通信：

```js
var user = (function () {
  var __name = 'sven',
    __age = 29;
  return {
    getUserInfo: function () {
      return __name + '-' + __age;
    }
  }
})();
```

**2、读取函数内部的变量**

既然 `f2` 可以读取 `f1` 中的局部变量，那么只要把 `f2` 作为返回值，我们不就可以在 `f1` 外部读取它的内部变量了吗！
```js
function f1() {
  var n = 999;
  function f2() {
    alert(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```

**3、让这些变量的值始终保持在内存中**

```js
function f1() {
  var n = 999;
  nAdd = function () { n += 1 }
  function f2() {
    alert(n);
  }
  return f2;
}

var result = f1();
result(); // 999
nAdd();
result(); // 1000
```

**4、循环中的闭包**


### 使用闭包的注意事项

1. 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
2. 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

### 练习题

**题目一**

```js
// 题目
var addSix = createBase(6);
addSix(10); // 返回16
addSix(21); // 返回27
```

```js
// 答案
// 可以创建一个闭包来存放传递给函数createBase的值。
// 被返回的内部函数是在外部函数中创建的，内部函数就成了一个闭包，它可以访问外部函数中的变量，在本例中是变量baseNumber。
function createBase(baseNumber) {
  return function(N) {
    // 我们在这里访问baseNumber，即使它是在这个函数之外声明的。
    // JavaScript中的闭包允许我们这么做。
    return baseNumber + N;
  }
}

var addSix = createBase(6);
addSix(10);
addSix(21);
```

**题目二**

[【不懂】破解前端面试（80% 应聘者不及格系列）：从 闭包说起](https://zhuanlan.zhihu.com/p/25855075)

**题目三**

下面这个ul，如何点击每一列的时候alert其index?（闭包）

```html
<ul id="test">
<li>这是第一条</li>
<li>这是第二条</li>
<li>这是第三条</li>
</ul>
```

```js
// 方法一：
var lis=document.getElementById('test').getElementsByTagName('li');
for(var i=0;i<3;i++)
{
    lis[i].index=i;
    lis[i].onclick=function(){
        alert(this.index);
    };
}
 
//方法二：
var lis=document.getElementById('test').getElementsByTagName('li');
for(var i=0;i<3;i++)
{
    lis[i].index=i;
    lis[i].onclick=(function(a){
        return function() {
            alert(a);
        }
    })(i);
}
```


## 防抖和节流

**参考资料**

- [JS的防抖与节流](https://juejin.im/entry/5b1d2d54f265da6e2545bfa4)
- [JS函数节流防抖](https://juejin.im/entry/59e631c46fb9a04525773144)
- [学习JS防抖](https://juejin.im/entry/5937cc8cb123db0064496405)

**为什么使用防抖和节流**

- 解决频繁操作带来的性能问题（卡顿，崩溃）。
- DOM操作、资源加载

### 函数防抖(debounce)

**使用场景**

- 实时搜索（keyup）
- 拖拽（mousemove）

**原理**

- 连续触发事件时，在指定时间内不会执行回调。
- 从最后一个触发事件开始计算指定时间后执行回调。

![函数防抖](./images/%E5%87%BD%E6%95%B0%E9%98%B2%E6%8A%96.jpg)

**实现**

```js
/*
 * 函数防抖 debounce
*/
function debounce(func, wait) {
  var timeout = null;
  return function () {
    if (timeout !== null) {
      clearTimeout(timeout);
    }
    timeout = setTimeout(func, wait);
  }
}
// 处理函数
function handle() {
  console.log(Math.random());
}
// 滚动事件
window.addEventListener('scroll', debounce(handle, 200));
```


### 函数节流(throttle)

**使用场景**

- 窗口调整（resize）
- 页面滚动（scroll）
- 抢购疯狂点击（mousedown）

**原理&&

- 连续触发事件时，在指定时间结束后执行一次回调函数。

![函数节流](./images/%E5%87%BD%E6%95%B0%E8%8A%82%E6%B5%81.jpg)

```js
/*
 * 函数节流 throttle
 * 时间戳
*/
let throttle = function (func, wait) {
  let prev = Date.now();
  return function () {
    let now = Date.now();
    if (now - prev >= wait) {
      func.apply(this, arguments);
      prev = Date.now();
    }
  }
}

function handle() {
  console.log(Math.random());
}
window.addEventListener('scroll', throttle(handle, 1000));
```

```js
/*
 * 函数节流 throttle
 * 定时器
 */
let throttle = function (func, wait) {
  let timer = null;
  return function () {
    if (!timer) {
      timer = setTimeout(function () {
        func.apply(this, arguments);
        timer = null;
      }, wait);
    }
  }
}

function handle() {
  console.log(Math.random());
}
window.addEventListener('scroll', throttle(handle, 1000));
```

```js
/*
  * 函数节流 throttle
  * 时间戳 + 定时器
  */
let throttle = function (func, wait) {
  let timer = null;
  let startTime = Date.now();
  return function () {
    let curTime = Date.now();
    let remaining = wait - (curTime - startTime);
    clearTimeout(timer);
    if (remaining <= 0) {
      func.apply(this, arguments);
      startTime = Date.now();
    } else {
      timer = setTimeout(func, remaining);
    }
  }
}

function handle() {
  console.log(Math.random());
}
window.addEventListener('scroll', throttle(handle, 1000));
```
