<!-- TOC -->

- [JavaScript 引用类型](#javascript-引用类型)
  - [Object 类型](#object-类型)
    - [方法](#方法)
    - [判断对象是否为空](#判断对象是否为空)
    - [深拷贝 vs 浅拷贝](#深拷贝-vs-浅拷贝)
      - [解决深拷贝的循环引用问题](#解决深拷贝的循环引用问题)
      - [拷贝 RegExp](#拷贝-regexp)
      - [拷贝函数](#拷贝函数)
  - [Array 类型](#array-类型)
    - [数组方法](#数组方法)
    - [数组去除 `false` 值](#数组去除-false-值)
    - [数组去重](#数组去重)
    - [数组排序](#数组排序)
    - [数组拷贝](#数组拷贝)
    - [并集，交集，差集](#并集交集差集)
  - [Date 类型](#date-类型)
    - [UTC、CET、GMT](#utccetgmt)
    - [方法](#方法)
    - [getter](#getter)
  - [RegExp 类型](#regexp-类型)
    - [正则表达式是什么？](#正则表达式是什么)
    - [元字符](#元字符)
    - [反义](#反义)
    - [分组](#分组)
    - [支持正则的 String 对象的方法](#支持正则的-string-对象的方法)
      - [1. search()](#1-search)
      - [2. match()](#2-match)
      - [3. replace()](#3-replace)
      - [4. split()](#4-split)
    - [RegExp 实例方法](#regexp-实例方法)
      - [compile()](#compile)
      - [test()](#test)
      - [exec()](#exec)
  - [Function 类型](#function-类型)
    - [函数没有重载](#函数没有重载)
      - [什么是函数重载？](#什么是函数重载)
      - [如何实现函数重载？](#如何实现函数重载)
    - [函数申明和函数表达式](#函数申明和函数表达式)
    - [作为值的函数（高阶函数）](#作为值的函数高阶函数)
    - [函数内部属性 `callee`、`caller`](#函数内部属性-calleecaller)
      - [arguments.callee](#argumentscallee)
      - [caller](#caller)
    - [函数属性和方法](#函数属性和方法)
    - [立即调用函数表达式（IIFE）](#立即调用函数表达式iife)
  - [基本包装类型](#基本包装类型)
    - [Boolean 类型](#boolean-类型)
      - [获取对象的布尔值](#获取对象的布尔值)
      - [6 个值为false](#6-个值为false)
      - [其他都为true](#其他都为true)
      - [不同的对象比较](#不同的对象比较)
      - [Boolean对象](#boolean对象)
    - [Number 类型](#number-类型)
      - [精度丢失](#精度丢失)
      - [方法](#方法)
    - [String 类型](#string-类型)
      - [1. 字符方法](#1-字符方法)
      - [2. 字符串操作方法](#2-字符串操作方法)
      - [3. 字符串位置方法](#3-字符串位置方法)
      - [4. `trim()`、`trimStart()`、`trimEnd()`](#4-trimtrimstarttrimend)
      - [5. 字符串大小写转换方法](#5-字符串大小写转换方法)
      - [6. 字符串的模式匹配方法](#6-字符串的模式匹配方法)
      - [7. localeCompare() 方法](#7-localecompare-方法)
      - [8. fromCharCode() 方法 不太常用](#8-fromcharcode-方法-不太常用)
      - [9. split()](#9-split)
      - [10. `matchAll()`](#10-matchall)
  - [单体内置对象](#单体内置对象)
    - [Global 对象](#global-对象)
    - [Math 对象](#math-对象)

<!-- /TOC -->

# JavaScript 引用类型

最初学习 JS 时参考[《JavaScript高级程序设计（第3版）》](https://weread.qq.com/web/bookDetail/eb232f50718ff5e9eb2a999)书中的目录结构，所以导致这里的记录包含的东西太多，以后慢慢整理。

- 引用类型是一种数据结构，用于将数据和功能组织在一起。
- 描述的是一类对象所具有的属性和方法。
- 常被称为类，但这种称呼并不妥当。
- 对象是某个特定引用类型的实例。

## Object 类型

**创建 Object 实例的方式有两种:**

1. 使用 `new` 操作符后跟 `Object` 构造函数
2. 使用对象字面量

**访问对象属性的点表示法和方括号表示法的区别**

1. 方括号语法有一个优点：可以通过变量来访问属性。
2. 如果属性名中包含会导致语法错误的字符，或者属性名是关键字或者保留字，也可以使用方括号表示法。
3. 方括号访问的属性名时，可以在程序运行时创建和修改属性，点操作符就不行。

### 方法

1. [`Object.is(value1, value2)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 方法判断两个值是否为同一个值。
2. [`Object.assign(target, ...sources)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 合并对象。
3. [更多方法参考 MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)

### 判断对象是否为空

1. 将对象转化为 json 字符串，再判断该字符串是否为 `"{}"`

```js
JSON.stringify(data) == "{}"
```

2. for in 循环判断

```js
var obj = {};
var b = function() {
    for(var key in obj) {
        return false;
    }
    return true;
}
alert(b()); // true
```

3. 使用 `Object.getOwnPropertyNames()`
4. 使用 ES6 `Object.keys()`

```js
const isEmpty = val => val == null || !(Object.keys(val) || val).length;

isEmpty(new Map()); // true
isEmpty(new Set()); // true
isEmpty([]); // true
isEmpty({}); // true
isEmpty(''); // true
isEmpty([1, 2]); // false
isEmpty({ a: 1, b: 2 }); // false
isEmpty('text'); // false
isEmpty(123); // true - type is not considered a collection
isEmpty(true); // true - type is not considered a collection
```

### 深拷贝 vs 浅拷贝

参考资料：

- [【掘金】js 深拷贝 vs 浅拷贝](https://juejin.im/post/59ac1c4ef265da248e75892b)
- [【博客园】关于JS堆栈与拷贝](https://www.cnblogs.com/chengguanhui/p/4737413.html)
- [【掘金】JavaScript 深拷贝](https://juejin.im/entry/57bd3817a34131005b21cbdb)
- [如何写出一个惊艳面试官的深拷贝](http://www.conardli.top/blog/article/JS%E8%BF%9B%E9%98%B6/%E5%A6%82%E4%BD%95%E5%86%99%E5%87%BA%E4%B8%80%E4%B8%AA%E6%83%8A%E8%89%B3%E9%9D%A2%E8%AF%95%E5%AE%98%E7%9A%84%E6%B7%B1%E6%8B%B7%E8%B4%9D.html)

![深拷贝](./images/%E6%B7%B1%E6%8B%B7%E8%B4%9D.jpg)

#### 解决深拷贝的循环引用问题

```js
function deepCopy(obj) {
  // 初始化一个对象
  const res = Array.isArray(obj) ? [] : {};
  // 遍历对象，用 for in 遍历 array 和 object
  for (let key in obj) {
    if (typeof obj[key] === "object") {
      res[key] = deepCopy(obj[key]);
    } else {
      res[key] = obj[key];
    }
  }
  return res;
}
var obj = {
  a: 1,
  b: 2,
  c: [1, 2, 3],
  d: { aa: 1, bb: 2 },
};
obj.e = obj; // 这里循环引用
console.log("obj", obj); // 不会报错

const objCopy = deepCopy(obj);
console.log(objCopy); //Uncaught RangeError: Maximum call stack size exceeded
```

**使用 `weekMap` 解决**

```js
function isObject(obj) {
  return (typeof obj === "object" || typeof obj === "function") && obj !== null;
}
function cloneDeep(source, hash = new WeakMap()) {
  if (!isObject(source)) return source;
  if (hash.has(source)) return hash.get(source); // 新增代码，查哈希表

  var target = Array.isArray(source) ? [] : {};
  hash.set(source, target); // 新增代码，哈希表设值

  for (var key in source) {
    if (Object.prototype.hasOwnProperty.call(source, key)) {
      if (isObject(source[key])) {
        target[key] = cloneDeep(source[key], hash); // 新增代码，传入哈希表
      } else {
        target[key] = source[key];
      }
    }
  }
  return target;
}
```

**用 `set` 来解决**

```js
// 是否为原始数据类型， 其实可以用 typeof 来判断
function isPrimitive(val) {
  return Object(val) !== val;
}
const set = new Set();
function clone(obj) {
  const copied = {};
  for (const [key, value] of Object.entries(obj)) {
    if (isPrimitive(value)) {
      copied[key] = value;
    } else {
      if (set.has(value)) {
        copied[key] = { ...value };
      } else {
        set.add(value);
        copied[key] = clone(value);
      }
    }
  }
  return copied;
}
```

#### 拷贝 RegExp

克隆一个正则，Lodash 库的实现方式是：

参考资料：[如何clone一个正则？](https://zhuanlan.zhihu.com/p/56499547)

```js
const reFlags = /\w*$/

function cloneRegExp(regexp) {
  const result = new regexp.constructor(regexp.source, reFlags.exec(regexp))
  result.lastIndex = regexp.lastIndex
  return result
}

cloneRegExp(/xyz/gim)
// => /xyz/gim
```

#### 拷贝函数

使用deep clone无非两种情况：

1. 有个对象传给一个函数，不放心它会不会随便修改这个对象，所以要克隆一下
2. 想修改一个对象但又不想影响原对象

其中前者是因为没做好后者，如果前者里说的这个函数是你自己写的请好好反省，是第三方库的请试着抛弃掉这个库


## Array 类型

### 数组方法

1. `push()`
    - 向数组的末尾添加一个或多个元素
    - 返回新的长度
    - 语法：`arrayObject.push(newelement1, newelement2, ...., newelementX)`

2. `pop()`
    - 用于删除并返回数组的最后一个元素
    - 如果数组已经为空，则 `pop()` 不改变数组，并返回 `undefined` 值
    - 语法：`arrayObject.pop()`

3. `unshift()`
    - 向数组的开头添加一个或更多元素
    - 返回新的长度
    - 语法：`arrayObject.unshift(newelement1, newelement2, ...., newelementX)`

4. `shift()`
    - 用于把数组的第一个元素从其中删除，并返回第一个元素的值
    - 语法：`arrayObject.shift()`

5. `slice()` 切下
    - 从已有的数组中返回选定的元素。
    - 不会修改原数组，返回切下来的新数组。
    - 应用：使用 `call` 类数组转化成数组。
    
```js
arr.slice(start,end)
// start 可选，为负数时，从数组末尾开始计算起始位置。
// start 起始值为1，不是0。
// end 可选。
```

6. `splice()` 粘接(胶片、磁带等) 
    - 向/从数组中添加/删除项目，然后返回被删除的项目。
    
```js
arrayObject.splice(index, howmany, item1, ....., itemX)
// index 必须，
// howmany 必须，如果是0，就不删除元素。
// item1,.....,itemX 可选，向数组中添加的元素。
```

7. `concat()`
    - 合并两个或多个数组。
    - 不会更改现有数组，而是返回一个新数组。
    - `var new_array = old_array.concat(value1[, value2[, ...[, valueN]]])`

8. `join()`
    - 用于把数组中的所有元素放入一个字符串。
    - 语法：`arr.join(separator)`

9. `reverse()`
    - 颠倒数组中元素的顺序
    - 语法：`arr.reverse()`

10. `sort()`
    - 排序
    - `arrayObject.sort((firstEl, secondEl) => firstEl - secondEl)`

11. `forEach()`
    - 对数组中的每一项运行给定函数
    - 没有返回值
    - 没有办法中止或者跳出 `forEach` 循环，除了抛出一个异常。
    - 简单点来说，本质上跟 `for` 没有区别，只是写法不一样。

```js
/* currentValue：数组当前项的值
 * index：数组当前项的索引
 * array：数组对象本身
 * thisValue：可选。传递给函数的值一般用 "this" 值。
*/ 如果这个参数为空， "undefined" 会传递给 "this" 值
array.forEach(callback(currentValue, index, array){
  //do something
}, thisValue)

/*
 * 如果使用的是箭头函数，thisValue 将会被忽略
*/ 
let arr = [1, 2, 3];
let arr1 = [4, 5, 6];
arr.forEach(function(item, index, arr) {
  console.log(this) // [4, 5, 6]
}, arr1)

/*
 * 再写一个MDN上的例子
*/
function Counter() {
  this.sum = 0;
  this.count = 0;
}

Counter.prototype.add = function (array) {
  array.forEach(function (entry) {
    this.sum += entry;
    ++this.count;
  }, this);
  //console.log(this);
};

var obj = new Counter();
obj.add([1, 3, 5, 7]);

obj.count;
// 4 === (1+1+1+1)
obj.sum;
// 16 === (1+3+5+7)
```
12. [`fill()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/fill)
    - 使用一个固定的值填充数组。
    - `arr.fill(value[, start[, end]])`
    - 是个可变方法，返回修改后的数组，会修改原数组。
    - start 和 end 都是可选。end 默认为数组的长度。

13. `map()`
    - 对数组中每一项运行给定函数。
    - 不会更改现有数组，而是返回一个新数组，**每个元素都是回调函数的结果**。
    - 跟 `forEach` 的语法一样。

14. `filter()`
    - 对数组中的每一项运行给定函数。
    - 返回该函数会返回 `true` 的项组成的数组，如果没有符合测试条件的项，返回一个`[]`。

15. `indexOf()` `lastIndexOf() `
    - 查找一个给定元素的索引。
    - 返回元素的索引。
    - `arr.indexOf(searchElement[, fromIndex = 0])`。要查找的项(searchElement)和表示查找起点位置的索引(fromIndex)。
    - `indexOf()` 方法从数组的开头（位置 0）开始向后查找。
    - `lastIndexOf()` 方法则从数组的末尾开始向前查找。
    - 在没找到的情况下返回 `-1`。

16. `includes()`
    - 判断一个数组是否包含一个指定的值。
    - 返回一个 `boolean`值。
    - `arr.includes(searchElement[, fromIndex = 0])`。
    - 【技巧】如果是要判断数组中是否存在某一项，请使用 `includes` 代替 `indexOf`。因为 `includes` 返回的是一个 `boolean` 值。
    
17. `find()` 和 `findIndex()`
    - `find()`：方法返回传入一个测试条件（函数）符合条件的数组第一个元素。
    - 【技巧】如果只想找到数组中唯一的项，使用 `find` 代替 `filter`。
    - `findIndex()`：方法返回传入一个测试条件（函数）符合条件的数组第一个元素位置。

18. `every()` 和 `some()`
    - `every()` 对数组中的每一项运行给定函数，如果该函数对**每一项**都返回 `true`，则返回 `true`;
    - `some()` 对数组中的每一项运行给定函数，如果该函数对**任一项**返回 `true`，则返回 `true`;
    - 【技巧】如果是要判断数组中是否存在某一项，使用 `some` 代替 `find`。

> `Array.some()` 与 `Array.includes()` 使用上的区别:
>
> 两者都返回一个布尔值，表示某项是否存在于数组之中，一旦找到对应的项，立即停止遍历数组。
> 
> 不同的是 `Array.some()` 的参数是**回调函数**，而 `Array.includes()` 的参数是一个值（均不考虑第二个可选参数）。

> 假设希望知道值为 value 的项是否存在于数组中<br>
> 既可以编写代码：`[].includes(value)`， 也可以给 `Array.some()` 传入 `item => item === value` 作为回调函数。<br>
> `Array.includes()`  使用更简单，`Array.some()` 可操控性更强。

19. `reduce()`、`reduceRight()`

    - reduce()每次只能接受两个参数，我对着两个参数的理解就是，当前结果，和当前序列的下一项。
    - 作用效果和原理就是`[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)。`

20. `flat()`、`flatMap`



**使用场景**

数组求和、字符串拼接

```js
var total = [ 0, 1, 2, 3 ].reduce(
  ( acc, cur ) => acc + cur,
  0
);
```

数组中元素出现的次数

```js
var names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];

var countedNames = names.reduce(function (allNames, name) { 
  if (name in allNames) {
    allNames[name]++;
  }
  else {
    allNames[name] = 1;
  }
  return allNames;
}, {});
```
`reduce` 方法同时实现 `map` 和 `filter`

> 假设现在有一个数列，你希望更新它的每一项（map的功能）然后筛选出一部分（filter的功能）。如果是先使用map然后filter的话，你需要遍历这个数组两次。 在下面的代码中，我们将数列中的值翻倍，然后挑选出那些大于50的数。

```js
const numbers = [10, 20, 30, 40];
const doubledOver50 = numbers.reduce((finalList, num) => {
  num = num * 2;
  if (num > 50) {
    finalList.push(num);
  }
  return finalList;
}, []);
doubledOver50;            // [60, 80]
```

统计数组中相同项的个数

> 很多时候，你希望统计数组中重复出现项的个数然后用一个对象表示。那么你可以使用reduce方法处理这个数组。
>
> 下面的代码将统计每一种车的数目然后把总数用一个对象表示。

```js
var cars = ['BMW','Benz', 'Benz', 'Tesla', 'BMW', 'Toyota'];
var carsObj = cars.reduce(function (obj, name) {
  obj[name] = obj[name] ? ++obj[name] : 1;
  return obj;
}, {});

carsObj; // => { BMW: 2, Benz: 2, Tesla: 1, Toyota: 1 }
```

**参考资料**

- [【掘金】如何在 JavaScript 中更好地使用数组](https://juejin.im/post/5b8d0a74f265da431d0e7ec0)
- [【掘金】js数组操作--使用迭代方法替代for循环](https://juejin.im/post/5a0c16d16fb9a0450b65edad)
- [【掘金】js数组的常见操作（ push、pop、unshift、shift、splice、concat、 join）的用法](https://juejin.im/post/59f848026fb9a0450167420a)

### 数组去除 `false` 值

**将数组中的 `false` 值去除**：

```js
const arrContainsEmptyVal = [3, 4, 5, 2, 3, undefined, null, 0, ""];
const compact = arr => arr.filter(Boolean)
```

`Boolean` 是一个函数，它会对遍历数组中的元素，并根据元素的真假类型，对应返回 `true` 或 `false`。

实际上，下面这样的写法是一种简写模式 `b = a.filter(Boolean);`

它等价于 `b = a.filter(function (x) { return Boolean(x); });`

**参考资料**：
- [去除 false 值](https://www.cnblogs.com/lishidefengchen/p/8757638.html)
- [如何在 JS 代码中消灭 for 循环](https://zhuanlan.zhihu.com/p/51549583)


### 数组去重

- [【未读】也谈JavaScript数组去重](https://zhuanlan.zhihu.com/p/24753549)
- [JavaScript数组去重（12种方法，史上最全）](https://segmentfault.com/a/1190000016418021)

**基本数组去重**

```javascript
var arr = [1, 2, 3, 4, 5, 4, 3, 2, 1];

Array.prototype.unique = function () {
  let result = [];

  this.forEach(function (item, index, array) { // 循环数组
    if (!result.includes(item)) { // 判断result中是否存在item
      result.push(item);
    }
  })

  return result;
}

console.log(arr.unique());

const result = arr.filter((element, index, array) => array.indexOf(element) === index)

console.log(result);
```

**hash表方式**

原理： 循环数组的值放在hash对象的key中，如果key不存在，将对应的value设置为ture。每次跟取对象中的key


1. 不区分类型，字符串1和数字1会被去重。

```js
let arr = ['11', 12, 13, 14, 15, 14, 13, 12, 11];

Array.prototype.unique = function () {
  let result = [],
    hash = {};

  this.forEach(function(item, index, array) { // 循环数组
    if (!hash[item]) {
      hash[item] = true;
      console.log(hash);
      result.push(item);
    }
  })

  return result;
}

console.log(arr.unique());
```

2. 区分类型。

```js
var arr = ['11',12,13,14,15,'11',11,14,13,12,11, 'a','b','c','a'];

Array.prototype.unique = function () {
  let result = [],
    hash = {};

  this.forEach(function(item, index, array) { // 循环数组
    let type = typeof item; // 返回item的数据类型

    hash[item] || (hash[item] = new Array()); // hash[item]第一次不存在，设置成空数组对象。

    if (hash[item].indexOf(type) < 0) { // 判断类型是否相同，不同返回-1
      hash[item] = type;
      result.push(item);
    }
  })

  return result;
}

document.write(arr.unique());
```

### 数组排序

**随机排序**

```js
// 方法一：
var arr = [1,2,3,4,5,6,7,8,9,10];
function randSort1(arr){
  for(var i = 0,len = arr.length;i < len; i++ ){
  	var rand = parseInt(Math.random()*len);
  	var temp = arr[rand];
  	arr[rand] = arr[i];
  	arr[i] = temp;
  }
  return arr;
}
console.log(randSort1(arr));

// 方法二：
var arr = [1,2,3,4,5,6,7,8,9,10];
function randSort2(arr){
  var mixedArray = [];
  while(arr.length > 0){
  	var randomIndex = parseInt(Math.random()*arr.length);
  	mixedArray.push(arr[randomIndex]);
  	arr.splice(randomIndex, 1);
  }
  return mixedArray;
}
console.log(randSort2(arr));

// 方法三：
var arr = [1,2,3,4,5,6,7,8,9,10];
arr.sort(function(){
  return Math.random() - 0.5;
})
console.log(arr);
```
**升序降序**

[quickSort](http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html)

### 数组拷贝

**浅拷贝**

```js
var arr = ["One","Two","Three"];

var arrto = arr;
arrto[1] = "test";
document.writeln("数组的原始值：" + arr + "<br />"); // Export:数组的原始值：One,test,Three
document.writeln("数组的新值：" + arrto + "<br />"); // Export:数组的新值：One,test,Three
```
如果想arr的元素不变，就要用深拷贝。

**深拷贝**

1. Array.prototype.conact()

```js
let arr = [1, 2, 3];
let arr1 = arr.concat();
arr1.push(4)
console.log('arr:', arr) // [1, 2, 3]
console.log('arr:1', arr1) // [1, 2, 3, 4]
```

2. Array.prototype.slice()

```js
let arr = [1, 2, 3];
let arr1 = arr.slice();
arr1.push(4)
console.log('arr:', arr) // [1, 2, 3]
console.log('arr:1', arr1) // [1, 2, 3, 4]
```

3. ES6扩展运算符

```js
const arr = [1, 2, 3]
// 写法一
const arr1 = [...arr]
// 写法二
const [...arr1] = arr

arr1.push(4)
console.log('arr:', arr)
console.log('arr1:', arr1)
```

【重要】：1、2、3三种方法如果数组中元素有对象，就都是浅拷贝。

**参考资料**

- [JS中的数组过滤，从简单筛选到多条件筛选](https://juejin.im/post/5bc44a71e51d450e935caa11)

### 并集，交集，差集

![交集-并集-补集-差集](./images/%E4%BA%A4%E9%9B%86-%E5%B9%B6%E9%9B%86-%E8%A1%A5%E9%9B%86-%E5%B7%AE%E9%9B%86.png)

**参考资料**

- [js数组并集，交集，差集的计算方式汇总](https://www.haorooms.com/post/js_array_jiaojicjbj)
- [JS - 计算两个数组的交集、差集、并集、补集（多种实现方式）](http://www.hangge.com/blog/cache/detail_1862.html)


## Date 类型

1. 需要实例化。
2. 不实例化返回的是一个日期字符串，而不是日期对象。
3. `Date` 没有对象字面量形式。
4. 用年份除以 4 能整除的是闰年（比方说 2000/4=500 2000年是闰年）。
5. `1 3 5 7 8 10 12` 月是31天，`2`月 28 或 29 天（闰年2月是29天，平年时28天） ，`4 6 9 11` 月是30天

由于 `Date` 对象处理日期不是很方便，通常会使用 [Moment.js](https://momentjs.com/) 或者 [Day.js](https://dayjs.gitee.io/zh-CN/) 工具库。

新特性 `Temporal` 将会却带 `Date`，开发中。

### UTC、CET、GMT

- `UTC` 世界标准时间。
- `CET` 欧洲中部时间。
- `GMT` 格林威治标准时间。

### 方法

参考资料：

- [【MDN】Date()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)
- [从一个签到功能(日历)到 js Date 类型的全了解](https://juejin.im/post/5c8dd3a06fb9a049ab0e8783)

### getter

**getFullYear()**

- 使用 `getFullYear()` 在IE和FF中都可以正确获取年份：2010
- `getYear()`方法已经被弃用，不推荐使用。

**getMonth()**

- 得到一个数组从**0开始**，所以获取的数字要 +1 才等于当前的月份。

```js
// 获取当前月份
const date = new Date()
const manthMapping = [
  "January",
  "February",
  "March",
  "April",
  "May",
  "June",
  "July",
  "August",
  "September",
  "October",
  "November",
  "December"
]
console.log("The month is " + manthMapping[date.getMonth()])

// 获取当前时间，精确到秒
function current () {
  let d = new Date(), str='';
  str += d.getFullYear() + '年'; //获取当前年份
  str += d.getMonth() + 1 + '月'; //获取当前月份（0——11）
  str += d.getDate() + '日';
  str += d.getHours() + '时';
  str += d.getMinutes() + '分';
  str += d.getSeconds() + '秒';
  return str;
}

setInterval(function () {
  console.log(current())
}, 1000);

function nowTime(){
  let time = new Date();
  let timeYear = time.getFullYear();
  let timeMonth = time.getMonth()+1;
  let timeDate = time.getDate();
  let timeDay = time.getDay();//星期
  let timeHours = time.getHours();
  let timeSeconds = time.getSeconds();
  let timeShow = timeYear + "/" + timeMonth + "/" + timeDate + "，" + timeHours + ":" + timeSeconds + "，星期" + timeDay;
  return timeShow;
}

console.log(nowTime())
```

## RegExp 类型

### 正则表达式是什么？

- 文字处理工具。
- 用于描述这些规则的工具。换句话说，正则表达式就是记录文本规则的代码。
- 用于查找/编辑特定信息。
- 核心是匹配。匹配**位置**、**字符**
- 正则表达式之所以强大，是因为其能实现模糊匹配。

```js
var expression = /pattern/flags ;
```
- pattern 模式。包含字符类、限定符、分组、向前查找以及反向引用。
- flags 标志。一个或多个，用以标明正则表达式的行为

```js
// 以下三种方式均可构造新的RegExp对象
// 以下三者是等价的
var regObj1 = new RegExp("\\d{6}"); // (字符串中的转义字符"\"要写成"\\")
var regObj2 = RegExp("\\d{6}");
var regObj3 = /\d{6}/;

// 带有模式标志(g和i)的RegExp对象
var regObj4 = new RegExp("[a-z]{10}", "gi");
var regObj5 = /[a-z]{10}/gi;
```

### 元字符

**基本元字符**

| 代码 | 说明 |
|:------|-----|
|.      |匹配除换行符以外的任意字符
|\      |使用反斜杠`\`进行转义。
|\|     |逻辑或操作符
|[]     |定义一个字符集合，匹配字符集合中的一个字符，在字符集合里面像 .，\这些字符都表示其本身
|[^]    |对上面一个集合取非
|-      |定义一个区间，例如[A-Z]，其首尾字符在 ASCII 字符集里面

**位置元字符**

| 代码 | 说明 
|:------|-----
|^	    |匹配字符串的开始    
|$	    |匹配字符串的结束
|[^x]	|匹配除了x以外的任意字符   
|\b	    |匹配单词的开始或结束           
|\B	    |匹配不是单词开头或结束的位置
|(?=p)  |匹配 p 前面的位置
|(?!p)  |匹配不是 p 前面的位置


**特殊元字符**

| 代码 | 说明 |
|:------|-----|
|\w	    |匹配字母或数字或下划线或汉字。word
|\W	    |匹配任意不是字母，数字，下划线，汉字的字符
|\s	    |匹配任意的空白符。space character
|\S     |匹配任意不是空白符的字符
|\d	    |匹配数字。digit         
|\D	    |匹配任意非数字的字符


**数量（重复）元字符**

|代码/语法|说明|
|------|---------|
|*     |重复零次或更多次
|+     |重复一次或更多次
|?     |重复零次或一次
|{n}   |重复n次
|{n,}  |重复n次或更多次
|{n,m} |重复n到m次

### 反义

> 有时需要查找不属于某个能简单定义的字符类的字符。比如想查找除了数字以外，其它任意字符都行的情况，这时需要用到反义。

例子：
- `\S+`匹配不包含空白符的字符串。
- `<a[^>]+>`匹配用尖括号括起来的以a开头的字符串。

### 分组

> 我们已经提到了怎么重复单个字符（直接在字符后面加上限定符就行了）；但如果想要重复多个字符又该怎么办？你可以用小括号来指定子表达式(也叫做分组)，然后你就可以指定这个子表达式的重复次数了，你也可以对子表达式进行其它一些操作

```javascript
// 简单的IP地址匹配表达式:
let regExp = /(\d{1,3}\.){3}\d{1,3}/

// 正确的IP地址：
let regExp = /((2[0-4]\d|25[0-5]|[01]?\d\d?)\.){3}(2[0-4]\d|25[0-5]|[01]?\d\d?)/
```

### 支持正则的 String 对象的方法

#### 1. search()

- 接受一个正则作为参数，如果参入的参数不是正则会隐式的使用 new RegExp(obj)将其转换成一个正则。
- 返回匹配到子串的起始位置，匹配不到返回-1。

#### 2. match()

- 本质上与调用 RegExp 的 exec() 方法相同
- 只接受一个参数，要么是一个正则表达式，要么是一个 RegExp 对象。

#### 3. replace()

- 这个方法接受两个参数：
    - 第一个参数可以是一个 RegExp 对象或者一个字符串（这个字符串不会被转换成正则表达式）
    - 第二个参数可以是一个字符串或者一个函数。

#### 4. split()

- 这个方法可以基于指定的分隔符将一个字符串分割成多个子字符串，并将结果放在一个数组中。
- 分隔符可以是字符串，也可以是一个 RegExp 对象（这个方法不会将字符串看成正则表达式）。 
- split() 方法可以接受可选的第二个参数，用于指定数组的大小，
以便确保返回的数组不会超过既定大小。


### RegExp 实例方法

#### compile()

> 更改正则表达式模式，并将其编译为内部格式，从而执行得更快。

#### test()

> 测试正则表达式模式在被搜索的字符串中是否存在对应的匹配。

- 返回true或false
- 每次执行test()函数都只查找最多一个匹配，如果找到就立即返回true，否则返回false。
- 如果为正则表达式设置了全局标志(g)，test()函数仍然只查找最多一个匹配，不过我们再次调用该对象的test()函数就可以查找下一个匹配。

#### exec()

> 使用正则表达式模式在指定字符串中进行匹配查找，并将查找结果以数组形式返回。


**参考资料**
- [JavaScript RegExp对象的属性和方法](http://www.365mini.com/page/tag/javascript-regexp-object)
- [正则表达式30分钟入门教程](http://www.cnblogs.com/deerchao/archive/2006/08/24/zhengzhe30fengzhongjiaocheng.html)
- [【网易考拉前端团队】JS 的正则表达式](https://juejin.im/post/59b5e50f51882519777c4815)（里面有很多练习题）
- [正则可视化工具](https://regex101.com/)
- [js正则及常用方法函数总结](https://www.cnblogs.com/myzhibie/p/4365142.html)
- [你是如何学会正则表达式的？](https://www.zhihu.com/question/48219401)
- JavaScript 正则表达式迷你书
- [【入门教程】learn-regex](https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md)

## Function 类型

- 函数实际上是对象，因此函数名实际上也是一个指向函数对象的指针，函数名可以有多个。
- 每个函数都是 Function 类型的实例，而且都与其他引用类型一样具有属性和方法

**形参、实参**
- 形参：是在定义函数时使用的参数，目的是用来接收调用该函数时传进来的实际参数。
    - 形参在函数运行结束时将被释放。
    - 实参如果是引用类型。改变响应的形参会影响实参的值。
    - 形参跟函数体内的变量相同时是如何处理的？
- 实参：是在调用时传递给函数的参数 

```javascript
function myfun(a,b,c){
  // 函数体
}
myfun(1, 2, 3);
// 这里a,b,c就是形参。1,2,3为实参。 
```

参考资料：[Javascript函数形参与实参](https://blog.csdn.net/liu_shi_jun/article/details/51967436)

**构造函数**

- 是用来创建对象时初始化对象，并且总与new运算符一起使用。
- 在命名规则上，构造函数一般是首字母大写，普通函数则是遵照小驼峰式命名法。
- 构造函数内部会创建一个实例，调用普通函数时则不会创建新的对象。
- 构造函数内部的 `this` 指向是新创建的 `person` 实例，而普通函数内部的 `this` 指向调用函数的对象（如果没有对象调用，默认为 `window`）

```
//构造函数
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.sayName = function () {
    alert(this.name);
  }
}
var person1 = new Person('mike', '18'); //this-->person
person1.sayName();  //'mike'


//普通函数
function person(name, age) {
  this.name = name;
  this.age = age;
  this.sayName = function () {
    alert(this.name);
  }
}
person('alice', '23'); //this-->window
window.sayName();  //'alice'
```

### 函数没有重载

#### 什么是函数重载？
在同一个作用域中，如果有多个函数的名字相同，但是形参列表不同(参数类型不同或参数个数不同)，返回值类型可同也可不同，我们称之为重载函数。

#### 如何实现函数重载？

参考资料: [【翻译】JavaScript函数重载](https://blog.fundebug.com/2017/07/24/javascript_metho_overloading/)、[【原文】 JavaScript Method Overloading](https://johnresig.com/blog/javascript-method-overloading/)

```js
// 参考资料： 《secrets of the JavaScript ninja》
function addMethod(object, name, fn) {
  // 保存上一次执行 addMethod 的 “find” 函数
  const old = object[name];

  object[name] = function() {
    if (fn.length === arguments.length) {
      return fn.apply(this, arguments);
    } else if (typeof old === "function") {
      // 形参和实参不相等的时候就去 old 里面向上查找
      // 一直到形参和实参相等
      return old.apply(this, arguments);
    }
  }
}

var people = {
  values: ["Dean Edwards", "Alex Russell", "Dean Tom"]
};

addMethod(people, "find", function() {
  return this.values;
})

addMethod(people, "find", function(firstname) {
  return this.values.filter(item => item.indexOf(firstname) !== -1);
})

addMethod(people, "find", function(firstname, lastname) {
  const name = `${firstname} ${lastname}`
  return this.values.filter(item => item === name);
})

console.log(people.find()); // ["Dean Edwards", "Alex Russell", "Dean Tom"]
console.log(people.find("Dean")); // ["Dean Edwards", "Dean Tom"]
console.log(people.find("Dean", "Edwards")); // ["Dean Edwards"]
```

> 应该注意的是，在使用这个特定的技巧时，需要注意以下几点。
>
> - 重载只适用于不同数量的参数,但并不区分类型、参数名称或其他东西。这些才是我们经常想做的事情。
> - 这样的重载方法会有一些函数调用的开销。我们要考虑在高性能时的情况。
>
> —— 《secrets of the JavaScript ninja》

### 函数申明和函数表达式

### 作为值的函数（高阶函数）

- 把函数当成参数传递给另一个函数。
- 将一个函数作为另一个函数的结果返回


### 函数内部属性 `callee`、`caller`

- 参考资料：[js 中的立即执行函数](https://blog.csdn.net/csdn_yudong/article/details/77431106)
- 在函数内部，有两个特殊的对象： `arguments` 和 `this` 。
- callee 和 caller 在严格模式下已经不能使用了，会报错。所以不建议学习。

#### arguments.callee

`callee` 属性是一个指针，指向拥有这个 arguments 对象的函数。

```javascript
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1)
  }
}
```

#### caller

`caller` 属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为 null 。

```javascript
function outer() {
  inner();
}
function inner() {
  alert(inner.caller);
}
// function inner() {
//   alert(arguments.callee.caller);
// }
outer();
```

```js
// 那么问题来了？
// 如果一对兔子每月生一对兔子；一对新生兔，从第二个月起就开始生兔子；
// 假定每对兔子都是一雌一雄，试问一对兔子，第n个月能繁殖成多少对兔子？（使用callee完成）
var result = [];

function fn(n) { //典型的斐波那契数列
    if (n == 1) {
        return 1;
    } else if (n == 2) {
        return 1;
    } else {
        if (result[n]) {
            return result[n];
        } else {
            //argument.callee()表示fn()
            result[n] = arguments.callee(n - 1) + arguments.callee(n - 2);
            return result[n];
        }
    }
}
```

参考资料：[严格模式之caller callee](https://www.jianshu.com/p/a426979b9605)

### 函数属性和方法

- 每个函数都包含两个属性： `length` 和 `prototype` 。
- `length` 属性表示函数希望接收的命名参数的个数。该值是指 “该函数有多少个必须要传入的参数”，即形参的个数。
- 引用类型而言， `prototype` 是保存它们所有实例方法的真正所在。
- `prototype` 属性是不可枚举的。
- 每个函数都包含两个非继承而来的方法： `apply()` 和 `call()` 。

### 立即调用函数表达式（IIFE）

> 它是立即调用函数表达式（Immediately-Invoked Function Expression），简称 IIFE。<br>
函数被创建后立即被执行：

在避免污染全局命名空间时经常使用这种模式，因为IIFE（与任何其他正常函数一样）内部的所有变量在其作用域之外都是不可见的

```js
(function IIFE(){
    console.log( "Hello!" );
})();
// "Hello!"
```

## 基本包装类型

- 基本包装类型的作用是：便于操作基本类型值（String，Number，Boolean）。
- 每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而让我们
能够调用一些方法来操作这些数据。
    1. 创建 String 类型的一个实例；
    2. 在实例上调用指定的方法；
    3. 销毁这个实例。

可以将以上三个步骤想象成是执行了下列 ECMAScript 代码。

```
var s1 = new String("some text");
var s2 = s1.substring(2);
s1 = null;
```

经过此番处理，基本的字符串值就变得跟对象一样了。而且，上面这三个步骤也分别适用于 Boolean
和 Number 类型对应的布尔值和数字值。

- 引用类型与基本包装类型的主要区别就是对象的生存期。使用 new 操作符创建的引用类型的实例，
在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，则只存在于一
行代码的执行瞬间，然后立即被销毁。这意味着我们不能在运行时为基本类型值添加属性和方法。
- 把字符串传给 Object 构造函数，就会创建 String 的实例；而传入数值参数会得到 Number 的实
例，传入布尔值参数就会得到 Boolean 的实例。


### Boolean 类型

#### 获取对象的布尔值
1. 两次取非
2. Boolean()函数
3. Boolean 对象在 ECMAScript 中的用处不大，因为它经常会造成人们的误解。

#### 6个值为false
1. 0
2. null
3. undefined
4. NaN
5. false
6. ""

#### 其他都为true

- 包括`{}`, `[]`, `Infinity`
- 对象的布尔值是true，即使是对象{}。

#### 不同的对象比较

- 不同的对象比较返回false，只有引用相同的对象才返回true

```javascript
var a = b = {};
console.log(a == b);   // true
console.log({} == {}); // false
console.log([] == []); // false,因为[]是object
```

#### Boolean对象

- 通过 `new Boolean(false)` 能获得一个初始值为 `false` 的布尔对象。
- 但是不能用 `new Boolean()` 来获取对象的布尔值，因为这样我们就 `new` 了一个布尔对象，既然是对象，我们获得的就永远是 `true` ，所以：

```js
if(new Boolean(false)) {
    // 这里面的代码一定会执行，即使布尔对象的值是false
}

if(new Boolean(false) == true) {
    // 这里面的代码不会执行，new Boolean(false)返回的是一个布尔类型的false值与true不相等
}
// 再次强调，用 Boolean() 或两次取非来获取布尔值.
```



```js
// 如果0, '', null, undefined, NaN，{}, [], Infinity,false, true互相比较(执行"=="运算)会怎么样呢？
function k(r) {
  console.log(r, ": ", r == false, r == true, r == 0, r == '', r == undefined, r == null, r == NaN, r == Infinity, r == {}, r == []);
}

k(true);
k(false);
k(0);
k('');
k(undefined);
k(Infinity);
k(NaN);
k({});
k([]);


// 由于将null作为参数传给函数时，相当于传入了undefined，所以对于null的检验，需要在修改k函数，即在函数内部让r=null;
// 从结果（结果就不列出来了）可以得到一些结论：

// 1. 0, '', [], false 互相比较(包括和自身比较)都返回true, 与其他值比较返回false;
// 2. undefined 和 null 互相比较返回true, 和自身比较也返回true, 其他情况返回false;
// 3. Infinity只和自身比较返回true;
// 4. NaN,{}和任意值比较都是返回false;
// 5. 除了true==true以外, true和其他值哪怕非0的数字，非空字符串，非空对象，非空数组都是返回false。
```

**参考资料：**
- [总结js中数据类型的bool值及其比较](http://www.cnblogs.com/suspiderweb/p/4908572.html)

### Number 类型

#### 精度丢失

**参考资料：**

- [JavaScript数字精度丢失问题总结](https://www.cnblogs.com/snandy/p/4943138.html)，讲的非常详细，忘记了再回头看看。

**精度丢失的原因**

浮点数，比如

```js
0.1 >> 0.0001 1001 1001 1001…（1001无限循环）
0.2 >> 0.0011 0011 0011 0011…（0011无限循环）
```

此时只能模仿十进制进行四舍五入了，但是二进制只有 0 和 1 两个，于是变为 0 舍 1 入。这即是计算机中部分浮点数运算时出现误差，丢失精度的根本原因。

> 看似有穷的数字, 在计算机的二进制表示里却是无穷的，由于存储位数限制因此存在“舍去”，精度丢失就发生了。

**如何解决进度丢失的问题**

- 把小数位移动到整数位（乘以倍数），然后还原小数位（除以倍数）。
- 使用 [math.js](https://www.jianshu.com/p/4f63f0003a56) 库。

#### 方法

```js
// 0. Number.EPSILON 是 JavaScript 表示的最小精度
// EPSILON 屈性的值接近广 2.2204460492503130808472633361816E-16

function equal(a, b){
  if(Math.abs(a-b) < Number.EPSILON){
    return true;
  }else{
    return false;
  }
}
console.log(0.1 + 0.2 === 0.3);
console.log(equal(0.1 + 0.2, 0.3))

// 1. 二进制和八进制
let b = 0bl010;
let o = 00777;
let d = 100;
let x = 0xff;

// 2. Number.isFinite检测一个数值是否为有限数
console.log(Number.isFinite(100));
console.log(Number.isFinite(100/0));
console.log(Number.isFinite(Infinity));

// 3. Number.isNaN检测一个数值是否为 NaN 
console.log(Number.isNaN(123));

// 4. Number.parselnt Number.parseFloat字符串转整数
console.log(Number.parselnt('52113141ove'));
console.log(Number.par$eFloat( '3.T415926神奇'));

// 5. Number.islnteger判断一个数是否为整数
console.log(Number.islnteger(5));
console.log(Number.islnteger(2.5));

// 6. Math.trunc将数字的小数部分抹掉
console.log(Math.trunc(3.5));

// 7. Math.sign判断一个数到底为正数 负数 还是零
console.log(Math.sign(100));
console.log(Math.sign(0));
console.log(Math.sign(-20000));
```

### String 类型

#### 1. 字符方法

- charAt()

```js
var stringValue = "hello world";
alert(stringValue.charAt(1)); //"e"
```

- charCodeAt()

```js
var stringValue = "hello world";
alert(stringValue.charCodeAt(1)); // 输出"101"
```

#### 2. 字符串操作方法

- concat() **实践中使用更多的还是加号操作符（+）**
- slice() 、 substr() 和 substring() 
    - 返回备操作字符串的指定部分。不会改变字符串自身。
    - 接受1到2个参数。
        - 第一个参数是字符串的开始位置。
        - 第二个参数 `slice` 、`substring` 是字符串结束的位置。
        - 第二个参数 `substr` 是返回字符串的个数。
    - 如果只有一个参数，字符串的长度则作为第二个参数。
        - 所以三个方法的返回值都是一样的。
    - 如果有两个**正数**参数。
        - 则 `slice` 、`substring` 返回值是一样的。
    - **负值**的情况，三个方法返回的值就都不一样了。
        - slice() 方法会将传入的负值与字符串的长度相加。
        - substr() 方法将负的第一个参数加上字符串的长度，而将负的第二个参数转换为 0。
        - substring() 方法会把所有负值参数都转换为 0。

#### 3. 字符串位置方法

- indexOf() 和 lastIndexOf()
    - 接受两个参数。第一个参数是要查找的字符串，第二个参数是开始查找的位置（可选）。
    -  这两个方法都是从一个字符串中搜索给定的子字符串，然后返子字符串的位置（如果没有找到该子字符串，则返回 -1 ）。

```js
let stringValue = "Lorem ipsum dolor sit amet, consectetur adipisicing elit";
let positions = new Array();
let pos = stringValue.indexOf("e");
while (pos > -1) {
  positions.push(pos);
  pos = stringValue.indexOf("e", pos + 1);
}
alert(positions); //"3,24,32,35,52"
```

#### 4. `trim()`、`trimStart()`、`trimEnd()`

- 这个方法会创建一个字符串的副本，删除前置及后缀的所有空格，然后返回结果。

#### 5. 字符串大小写转换方法

- toLowerCase() 、 toLocaleLowerCase() 、 toUpperCase() 和 toLocaleUpperCase() 。
    - 在不知道自己的
代码将在哪种语言环境中运行的情况下，还是使用针对地区的方法更稳妥一些。

#### 6. 字符串的模式匹配方法

参看字符串正则相关方法

#### 7. localeCompare() 方法

#### 8. fromCharCode() 方法 不太常用

#### 9. split()

- `split([separator[, limit]])`
- 把字符串分割成字符串数组。
- `separator` 为分隔符。
- `limit` 为整数，限制分隔片段的个数。

#### 10. `matchAll()`

## 单体内置对象

### Global 对象

### Math 对象

**常用的方法**

```js
Math.abs(x); //返回x绝对值。
Math.max(x,y,...); //返回x，y，...中的最最大值。
Math.min(x,y,...); //返回x，y，...中的最最小值。
Math.floor(x); //返回x的下舍入(返回小于x的最大整数)。1.9 => 1。 floor是地板的意思
Math.ceil(x); //返回x的上舍入(返回大于x的最大整数)。1.1 => 2。 ceil是天花板的意思。
Math.round(x); //四舍五入。
Math.random(); //返回一个0-1之间的随机数（包含0，不包括1）。
```

**Math.random()**

得到一个两数之间的随机整数，包括两个数在内

```js
// 参考MDN
function getRandomIntInclusive (min, max) {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
```

