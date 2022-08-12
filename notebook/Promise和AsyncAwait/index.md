# Promise和AsyncAwait

## Promise

> Promise 是一个对象，它代表了一个异步操作的最终完成或者失败。 —— MDN

- Promise 是一个对象，从它可以获取异步操作的消息。
- Promise 是用来管理异步编程的，它本身不是异步的。
- thenable 即带有then方法的对象。

**Promise有2个特点**
- 对象的状态不受外界影响。
    - pending（进行中）
    - fulfilled（已成功）
    - rejected（已失败）
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。
- Promise 对象的状态改变，只有两种可能：从 `pending` 变为 `fulfilled` 和从 `pending` 变为 `rejected`。

**Promise的3个缺点**

- 无法取消 Promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。
- 当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

**如何使用 Promise**

- 通常是实例化一个 `Promise` 封装在一个函数当中，然后 `return` 这个 `Promise`，在需要的时候运行这个函数。
- 链式操作。
- 使用 `Promise` 对 Ajax 进行封装。[如何封装](http://es6.ruanyifeng.com/#docs/promise#%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)
- 使用 `Promise` 对 Axios 进行封装。
- `resolve` 函数的参数除了正常的值以外，还可能是另一个 `Promise` 实例。

一个简单的例子：

```js
function runAsync(data) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('promise内部执行完成')
      /* 异步操作成功 */
      if (data > 10) {
        resolve(data)
      } else { /* 异步操作失败 */
        reject(data)
      }
    }, 2000)
  })
}

runAsync(12).then(resolve => {
  // success
  console.log('成功', resolve)
}, reject => {
  // failure
  console.log('失败', reject)
})
```

链式操作的例子：

```js
function runAsync1(data) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('promise1 内部执行完成')
      resolve(data)
    }, 2000)
  })
}

function runAsync2(data) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('promise2 内部执行完成')
      resolve(data)
    }, 2000)
  })
}

function runAsync3(data) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('promise3 内部执行完成')
      resolve(data)
    }, 2000)
  })
}


runAsync1('then1 执行了').then(data => {
  console.log(data)
  return runAsync2('then2 执行了')
}).then(data => {
  console.log(data)
  return runAsync3('then3 执行了')
}).then(data => {
  console.log(data)
})
```

`resolve` 函数的参数是另一个 `Promise` 实例

```js
const p1 = new Promise(function (resolve, reject) {
  // ...
});

const p2 = new Promise(function (resolve, reject) {
  // ...
  resolve(p1);
})
```

### 错误处理

- 【未读】[Promise的错误处理](https://cloud.tencent.com/developer/article/1645539)
- 【MDN】[异常处理语句](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Control_flow_and_error_handling#%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86%E8%AF%AD%E5%8F%A5)

**常见的内置错误**

- 错误的类型
    - Error: 所有错误的父类
    - ReferenceError: 引用的变量不存在
    - TypeError: 数据类型不正确的错误
    - RangeError: 数值不在其所允许的范围内
    - SyntaxError: 语法错误
- 错误处理
    - 捕获错误：try/catch
    - 抛出错误：throw error
- 错误对象
    - message属性： 错误相关信息
    - stack属性：函数调用栈记录信息


### Promise.all()

- 提供并行执行异步操作的能力。
- "谁跑的慢，以谁为准执行回调"。
- 所有的 Promise 对象都变为 resolve 的时候，该方法才会返回。
- 如果参数中的任何一个 promise 为 reject 的话，则整个 Promise.all 调用会立即终止，并返回一个 reject 的新的 Promise 对象。

```js
const promise1 = new Promise((resolve, reject) => resolve("promise1执行成功"))
const promise2 = new Promise((resolve, reject) => resolve("promise2执行成功"))
const promise3 = new Promise((resolve, reject) => resolve("promise3执行成功"))

Promise
  .all([promise1, promise2, promise3])
  .then(function(results){
    console.log(results); // ["promise1执行成功", "promise2执行成功", "promise3执行成功"]
  });
```

### Promise.allSettled()

`Promise.allSettled` 跟 `Promise.all` 类似, 其参数接受一个 `Promise` 的数组, 返回一个新的 `Promise`, 唯一的不同在于, 它不会进行短路, 也就是说当 `Promise` 全部处理完成后,我们可以拿到每个 `Promise` 的状态, 而不管是否处理成功。

```js
const promise1 = new Promise((resolve, reject) => resolve("promise1执行成功"))
const promise2 = new Promise((resolve, reject) => reject("promise2执行失败"))

Promise.allSettled([promise1, promise2]).then(result => {
  console.log("result", result);
})
```

看下使用 `all` 需要怎么处理

```js
const promise1 = new Promise((resolve, reject) => resolve("promise1执行成功"))
const promise2 = new Promise((resolve, reject) => reject("promise2执行失败"))

Promise.all([promise1, promise2]).then(result => {
  console.log("result", result); // 没有执行
}).catch(reason => {
  console.log("reason", reason) // promise2执行失败
});
```

### Promise.race()

- "谁跑的快，以谁为准执行回调"
- `Promise.race` 只要有一个 `promise` 对象进入 `FulFilled` 或者 `Rejected` 状态的话，就会继续进行后面的处理。

### Promise.resolve()

快速得到一个 promise 对象

```js
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

所以可以使用这样的便捷写法。

```js
// 如果传入的参数为非 Promise 对象，则返回的结果为成功 promise 对象
const p1 = Promise.resolve('test');

// 如果传入的参数为 Promise 对象， 则参数的结果决定了 resolve 的结果
const p2 = new Promise((resolve, reject) => {
  // resolve('ok');
  reject('error')
})

const p3 = Promise.resolve(p2)
p3.then(success => {
  debugger
}, error => {
  debugger
});

```

### Promise.reject()

同上面的 `Promise.resolve()` 用法，永远快速返回一个失败的 promise 对象。

### Promise.prototype.finally()

ES9 新增 `finally()` 方法返回一个 `Promise`。在 `promise` 结束时，无论结果是 `fulfilled` 或者是 `rejected`，都会执行指定的回调函数。

**参考资料**：

- [ES6 Promise 用法讲解](https://www.cnblogs.com/whybxy/p/7645578.html)
- [你真的懂 Promise 吗？](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651558103&idx=1&sn=eff146603bdc9e1d1cd53fb275882ca5&chksm=80254716b752ce0048fb2e74cd4e632177d7babf222e33bf2bf1978635f7f88eb84eea842ab3&scene=126&sessionid=1585791205&key=d65ec04f290f1ced236b9d27a6b940e35720a1b4f29893cb1df9993105e60071783317a6e858ec7e6662539e189a7c6c092e6b567ce99466109409ce9bbf9e6c67f24ad80fd8c7e7a48d3645480ba71e&ascene=1&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62080079&lang=zh_CN&exportkey=AVfPkWBG0McF7aXAQTQ%2FhyE%3D&pass_ticket=Xva2sAgrlui4J%2BOuf924%2FzuVst0io8Zc57lY91fERmL4UFYdappr7vplV246pnV3)

## 实现 Promise

### 前置问题

**同步回调函数、异步回调函数**

- 同步函数
    - 立即执行，完全执行完后才结束，不会放在调用队列中
    - 数组遍历相关的回调函数，Promise 的 excutor 函数
- 异步函数
    - 不会立即执行，放入回调队列中将来执行
    - 定时器回调，ajax回调，Promise的成功和失败的回调

这里列出一些细节，在实现的时候都需要进行处理。

**1、如何改变 promise 状态**

```js
const p = new Promise((resolve, reject) => {
    // 1. resolve 函数
    resolve('success');
    // 2. reject 函数
    reject('error');
    // 3. 抛出错误
    throw '出问题了';
})
```

**2、可以执行多个回调**

```js
const p = new Promise((resolve, reject) => {
  resolve('success');
})

// 回调1
p.then(value => {
  console.log(value, '回调1');
})

// 回调2
p.then(value => {
  console.log(value, '回调2');
})
```

**3、改变 promise 状态和指定 then() 中的回调的先后顺序**

都有可能：

- 封装的是同步任务，先执行 `resolve()` 改变状态。
- 封装的是异步任务，先执行 `then()`，因为需要等待一段时间才能改变状态。

**4、`then()` 方法返回的是一个新的 Promise 对象**

所以 then 即使没有显示的 return 也可以链式调用

```js
const p = Promise.resolve("a");
p.then().then();
```

**5、`then()` 方法返回的结果由 `then()` 方法指定的回调函数的执行结果决定。**

**6、`then()` 中的回调函数是异步执行的**

**7、promise 链式调用**

**8、promise 异常穿透**

只需要在最后 `catch` 来处理错误，不用在每个 `then()` 里面处理，这就是异常穿透。

```js
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    // resolve('success');
    reject('error1');
  }, 1000)
});

p.then(value => { 
  console.log(1);
  // throw 'error2';
}).then(value => { 
  console.log(2);
  // throw 'error3';
}).then(value => { 
  console.log(3);
  // throw 'error4';
}).catch(reason => {
  console.log(reason);
})
```

**9、如何中断 promise 链**

```js
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('success');
  }, 1000)
});

p.then(value => { 
  console.log(1);
  // 有且只有一种方式
  return new Promise(() => {})
}).then(value => { 
  console.log(2);
}).then(value => { 
  console.log(3);
}).catch(reason => {
  console.log(reason);
})
```

**10、promise 对象状态只能改变一次**

### Promise

**参考资料：**
- [我如何实现Promise](https://juejin.im/post/5d0da5c8e51d455ca0436271)
- [带你写出符合 Promise/A+ 规范 Promise 的源码](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651560944&idx=2&sn=3ad6231e87476243437bfe06ea7def60&chksm=80254c31b752c527d78cb21d90991496b8f6f4374c5121a98edf9abbf07de624be2cb65a8627&scene=126&sessionid=1596081027&key=f3a4eda7328a3025acaf0a443d172e0d5d2d27293ba0f260c8e789ae9b86ee576d9b0c3a6da62b9c6cc0f42a66d39f964d4df21ca75d18fb0a98222a95a9591bd91d3c908a845d437758d48b59734fc3&ascene=1&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10+x64&version=62090529&lang=zh_CN&exportkey=AYf0ewgcW6MSgxxlHPnUL%2FM%3D&pass_ticket=QPsPPK%2FIujHEwIoH0nO3RwT1dxFQLQLIDIVHnMFsrtAGZ07K7hARO%2Bl7o4mlW74H)
- [史上最易读懂的 Promise/A+ 完全实现](https://zhuanlan.zhihu.com/p/21834559)
- [【翻译】Promises/A+规范](https://www.ituring.com.cn/article/66566)
- [尚硅谷Promise教程(promise前端进阶必学)](bilibili.com/video/BV1MJ41197Eu?p=14)

**实现 Promise 的流程和注意事项**

- Promise 和 AJAX 本身都是同步的。
    - 使用 `Promise` 创建实例时会传入一个 executor 参数，并且会立即执行。executor 有 `resolve` 和 `reject` 这两个参数， 他们实际上是一个函数。
    - executor 里面通常是异步操作（AJAX等），在异步执行成功后调用 `resolve(data)`，如果失败则调用 `reject(reason)`。
    - 异步执行完毕后，Promsie 的状态就会改变，Promise 内部的 `resolve(data)`、`reject(reason)` 这两个函数会做 2 件事：
        - 修改状态为 fulfilled 或 rejected。
        - 并且保存异步操作的值（data或reason）。
- `Promise.prototype.then()` 为 `Promise` 的原型方法。它的作用是为 Promise 实例添加状态改变时的回调函数。
    - `then()` 有两个参数，分别为 Promise 状态变成 `fulfilled` （成功）时的回调函数，状态变成 `rejected`（失败）时候的回调函数。
    - 如果 Promise 的状态为 pending 。保存 callback 到实例数组当中。等到 Promise 状态改变后遍历执行数组中的 callback。
    - 如果 Promise 的状态为 resolved 或 rejected ，则立即执行 callback。
    - 因为 `Promise` 一旦状态改变，就不会再变，所以 `then` 返回的是一个新的 `Promise` 实例，而不是 `this`，这是由 Promise 内部实现的。`then` 永远会返回一个新的 `Promise` 实例，所以可以实现链式调用。

下面的代码是简单的实现，还无法通过全部的测试用例：

```js
function Promise(executor) {
  this.PromiseState = "pending";
  this.PromiseResult = null;
  this.callbacks = []; // 执行异步任务时，保存多个then的回调

  const that = this;

  // resolve/reject 做三件事
  // 1、修改 promise 状态
  // 2、保存 promise 执行的结果
  // 3、遍历执行 then 方法的回调函数
  function resolve(value) {
    // 状态只能修改一次
    if (that.PromiseState !== "pending") return;
    that.PromiseState = "fulfilled";
    that.PromiseResult = value;

    setTimeout(() => {
      that.callbacks.forEach(cb => cb.onFulfilled(value));
    })
  };

  function reject(value) {
    if (that.PromiseState !== "pending") return;
    that.PromiseState = "rejected";
    that.PromiseResult = value;

    setTimeout(() => {
      that.callbacks.forEach(cb => cb.onRejected(value));
    })
  }

  // 执行 executor
  // executor 的参数是两个函数
  try {
    executor(resolve, reject);
  } catch (error) {
    reject(error);
  }
}

Promise.prototype.then = function(onFulfilled, onRejected) {
  const that = this;

  // 解决异常穿透
  if (typeof onRejected !== "function") {
    onRejected = reason => {
      throw reason;
    }
  }

  // 值传递
  // 需要判断是不是 function 类型
  // 如果不是函数则忽略，目的是 .then().then() 的穿透
  if (typeof onFulfilled !== "function") {
    onFulfilled = value => value
  }

  // then 返回的一个新的 Promise 实例
  return new Promise((resolve, reject) => {
    function callback(type) {
      try {
        const x = type(that.PromiseResult);
        if (x instanceof Promise) {
          x.then(v => {
            resolve(v);
          }, r => {
            reject(r);
          })
        } else {
          resolve(x)
        }
      } catch (error) {
        reject(error);
      }
    }

    if (this.PromiseState === "fulfilled") {
      setTimeout(() => {
        callback(onFulfilled)
      })
    }
    
    if (this.PromiseState === "rejected") {
      setTimeout(() => {
        callback(onRejected)
      })
    }
    
    // 如果异步操作还没有执行完，则保存回调函数
    if (this.PromiseState === "pending") {
      this.callbacks.push({
        onFulfilled: function() {
          callback(onFulfilled)
        },
        onRejected: function() {
          callback(onRejected)
        }
      });
    }
  })
}
```

测试代码

```js
const p1 = new Promise(function(resolve, reject) {
  reject("p1");
})

const p2 = new Promise(function(resolve, reject) {
  resolve("p2");
})

const p3 = new Promise(function(resolve, reject) {
  resolve("p3");
})

p3.then(function(value) {
  debugger
  console.log(value); // p3
}).then(value => {
  debugger
  console.log(value) // undefined
  return p1
}).then(value => {
  debugger
  console.log(value) // p1
},reason => {
  debugger;
})
```

下面的测试会使 `onFulfilled` 出现多个回调

```js
var p = new MyPromise(resolve => {
  setTimeout(resolve, 0, "test");
});
p.then(() => {
  console.log(1)
})
p.then(() => {
  console.log(2)
})
p.then(() => {
  console.log(3)
})
p.then(() => {
  console.log(4)
})
```

### Promise.all

```js
//为类的静态方法，而不是在原型上
static all(promises = []) {
  let count = 0, 
      result = [];
  return new Promise((resolve, reject) => {
    for(let i = 0; i < promises.length; i++){
      promises[i].then(val => {
        count++;
        result[i] = val;
        if(count === promises.length){
          resolve(result)
        }
      }, reject); // 这个reject是简写
    }
  })
}
```

### Promise.race

```js
Promise.race = function(promises) {
  return new Promise((resolve, reject) => {
    promises.forEach(promise => {
      promise.then(value => {
        resolve(value);
      }, reason => {
        reject(reason)
      })
    })
  })
}
```

### Promise.prototype.catch()

`catch` 之后可以继续使用 `then`

```js
MyPromise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected)
}
```

### Promise.prototype.finally()

`finally` 本质上是 `then` 方法的特例。

```js
promise.finally(() => {
  // 语句
});

// 等同于
promise.then(
  result => {
    // 语句
    return result;
  },
  error => {
    // 语句
    throw error;
  }
);
```

内部实现：

```js
Promise.prototype.finally = function (callback) {
  let P = this.constructor;
  return this.then(
    value  => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => { throw reason })
  );
};
```

### Promise.resolve()

将现有对象转为 Promise 对象

```js
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

内部实现

```js
MyPromise.resolve = function(value) {
  // 如果 value 是 promise 则直接返回这个 promise
  if (value instanceof MyPromise) {
    return value
  }

  // 如果不是就使用 promise包装一下
  return new MyPromise(resolve => {
    resolve(value)
  })
}
```

### Promise.reject()

```js
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});

// 内部实现
Promise.reject = function(value) {
  return new Promise((resolve, reject) => {
    reject(value);
  })
}
```

## Async/Await

ES7中新增的异步编程方法，async/await 的实现是基于 `Promise` 的，简单而言就是 `async` 函数就是返回 `Promise` 对象，是 `generator` 的语法糖。

**为什么使用 `async`？**
- 真正地用同步的方式写异步代码；
- 不用写 `then` 及其回调函数，减少代码行数，也避免了代码嵌套；
- 所有异步调用可以写在同一个代码块中，无需定义多余的中间变量；
- async 函数会隐式地返回一个 `Promise`，因此可以直接 return 变量，无需使用 `Promise.resolve` 进行转换；
- Promise 中不能自定义使用 `try/catch` 进行错误捕获，但是在 Async/await 中可以像处理同步代码处理错误。

**async**

在任何函数之前加上 async 关键字意味着该函数会返回 Promise 对象。

- 返回非 Promise `'string'`;
- 抛出异常 `throw "error"`;
- 返回 Promise 对象。

```js
// 即使没有显式地这样做，它也会在内部使它返回 promise。
const aFunction = async () => {
  return '测试'
}

aFunction().then(alert) // 这会 alert '测试'
```

**await 表达式**

- await 右侧的表达式一般为 promise 对象，但也可以是其他值；
- 如果表达式是 promise 对象， await 返回的是 promise 成功的值；
- 如果表达式是其他值，直接将此值作为 await 的返回值。
- 注意：
    - await 必须写在 async 函数中，单 async 函数中可以没有 await；
    - 如果 await 的promise 失败了，就会抛出异常，需要通过 `try/catch` 捕获错误

**参考资料**

- [重构：从Promise到Async/Await](https://cnodejs.org/topic/5a30958dd92f2f5b185acdfa)
- [当async/await遇到map和reduce](https://www.dazhuanlan.com/2019/10/04/5d96efe7cdee8/)

## 测试

```js
let p1 = new Promise((resolve, reject) => {
  // resolve("fulfilled");
  reject("rejected");
});
let p2 = p1
  .then(
    value => console.log(value),
    reason => console.log(reason)
  )
  .then(
    a => console.log(a),
    b => console.log(b)
  );
```

```js
const p1 = new Promise((resolve, reject) => {
  debugger;
  // resolve("fulfilled");
  reject("rejected");
});
const p2 = new Promise(resolve => {
  resolve(p1);
}).then(
  value => {
    console.log(value);
  },
  reason => {
    debugger;
    console.log(reason);
  }
);
```

```js
let promise = new Promise((resolve, reject) => {
  reject("reject");
});
let p2 = promise.then(() => { });
p2.then(null, null).then(null, reject => {
  console.log(reject);
});
```