---
title: Promise-Learning.md
date: 2018-05-14 17:24:37
tags: Promise
---

## Promise 一些关键点记录
*tips: 本文 then/catch 等 Promise.prototype 属性一律简写作 Promise.xxx*


1. Promise Chain: Promise.then 不仅会注册回调，更会将回调函数返回值变换包装成 Promise 对象。不论是 then 还是 catch 都会返回新的 Promise 对象

```javascript
const promise = Promise.resolve(1)
function a(value) {
  return value + 1
}
function b(value) {
  return value * 2
}
promise.then(a).then(b).then(console.log) // => 4
```

2. 接上，Promise.then 不要并行使用，达不到流程控制效果，应该用 Promise Chain

```javascript
// 尽量避免使用, then 都是并行的
const aPromise = new Promise(function (resolve) {
 resolve(100);
});
aPromise.then(function (value) {
 return value * 2;
});
aPromise.then(function (value) {
 return value * 2;
});
aPromise.then(function (value) {
 console.log("1: " + value); // => 100
})
```
3. Promise 都会执行完毕，所以 Promise.race 即使总结果返回，未完成的 Promise 并不会中断 

4. `then(resolve, reject)` 如果 resolve 中抛出异常，则 reject 不能捕获此异常，因为 reject 并不是针对 resolve 的，而是针对当前 promise 或者之前的 promise；好的实践应该是：`then(resolve).catch(reject)`

5. `Promise.resolve` 接受 Thenable(Promise Like)，然后转换成 Promise 对象 

6. then 方法中不抛出异常，如何达到 reject 效果，答案是 resolve 时候返回一个 Promise。根据 Promise 规范，前面的 Promise 如果 rejected，则后续的 Promise 就会走 catch(reject) 分支

```javascript
Promise.resolve()
.then(function() {
  // 假如此处有异步处理，则返回 Promise 达到流程控制效果
  return new Promise(function(resolve, reject) {
    // reject 错误而不是抛异常，因为 catch 很难区分错误哪里来的
    reject(new Error('some error message'))
  })
})
.catch(console.log)
```

7. Promise 包装例子，来自《Promise 迷你书》

```javascript
// Array Promise wraaper
function ArrayAsPromise(array) {
  this.array = array
  this.promise = Promise.resolve()
}
ArrayAsPromise.prototype.then = function (onFulfilled, onRejected) {
  this.promise = this.promise.then(onFulfilled, onRejected)
  return this
}
// catch 在ECMA3 不能用
ArrayAsPromise.prototype['catch'] = function(onRejected) {
  this.promise = this.promise.catch(onRejected)
  return this
}
Object.getOwnPropertyNames(Array.prototype).forEach(function(methodName) {
  if(typeof ArrayAsPromise[methodName] !== 'undefined') {
    return
  }

  const arrayMethod = Array.prototype[methodName]
  if(typeof arrayMethod !== 'function') {
    return
  }

  ArrayAsPromise.prototype[methodName] = function() {
    const that = this
    const args = arguments
    this.promise = this.promise.then(function() {
      that.array = Array.prototype[methodName].apply(that.array, args)
      return that.array
    })
    return this
  }
})
module.exports = ArrayAsPromise
module.exports.array = function newArrayPromise(array) {
  return new ArrayAsPromise(array)
}

// Mocha 测试示例：
const assert = require('power-assert')
const ArrayAsPromise = require('./{path-to-previous-file}.js)

describe('array-promise-chain', function() {
  function isEven(value) {
    return value % 2 === 0
  }
  function double(value) {
    return value * 2
  }

  beforeEach(function() {
    this.array = [1, 2, 3, 4, 5]
  })

  describe('Native Array', function() {
    if('can method chain', function() {
      const result = this.array.filter(isEven).map(double)
      assert.deepEqual(result, [4, 8])
    })
  })

  describe('ArrayAsPromise', function() {
    if('can promise chain', function(done) {
      const array = new ArrayAsPromise(this.array)
      array.filter(isEven).map(double).then(function(value) {
        assert.deepEqual(value, [4, 8])
      }).then(done, done)
    })
  })
})
```
8. Promise.prototype.reduce 是控制 Promise 串行流程的良好工具，当然也可以用 Promise chain 来写，相比较代码多一点。
