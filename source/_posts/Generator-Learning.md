---
title: Generator-Learning
date: 2018-05-31 15:40:47
tags:
---
# ES6 Generator Function

####参考文章[Generator](https://davidwalsh.name/es6-generators)

### Basic Down

1. `yield` 代表运行暂停，`next().value` 即是 yield 表达式的值。当`next(val)`重启 generator 时，val 作为 yield 表达式值被传入，如
```javascript
function* demo(x) {
  const y = yield x + 1
  const z = yield y * 2 
  console.log(y) // 5
  console.log(z) // 100
}
const d = demo(3)
d.next(111) // value: 4, done: false
d.next(5) // value: 10, done: false
d.next(100) // value: undefined, done: true
```
解析：第一次调用 next 方法，执行到第一个 yield 暂停，由于前面没有 yield 来接收 111，则该值被丢弃！！！ 此时 yield 表达式值为 3 + 1 = 4；d.next(5) 将重启生成器,且 yield 接收 5 并赋值给 y，则生成器此时 value 为 5 * 2 = 10，并打印此时 y 的值为 5；再次调用 d.next(100) -> {value: undefined, done: false} 则生成器执行完毕，z 被赋值为 100<br/>
把住一点：yield 右侧值是本次 next() 返回值，下次 next(val) 重启 generator，则带入的参数 val 会"替换上次暂停的 yield"，赋值给该 yield 左侧的变量
<br/>
*值得注意的是，`for...of`会忽略 return 语句返回值(`.next().done = true`), 例子*
```javascript
function * dem(x){
  const y = yield x + 1
  yield 4
  return y
}

for(let v of dem(2)) {
  console.log(v)
}
// 3
// 4
// 并不会再打印 4
```

### Error Handling
1. 类似`.next()`的`.throw()`方法可以主动让 Generator 异常，通过`try ... catch`语句即可捕获异常！
```javascript
function *foo() { }
const it = foo();
try {
  it.throw( "Oops!" );
} catch (err) {
  console.log( "Error: " + err ); // Error: Oops!
}
```

```javascript
function *foo() {
    var x = yield 3;
    var y = x.toUpperCase(); // could be a TypeError error!
    yield y;
}

const it = foo();
it.next(); // { value:3, done:false }
try {
  it.next( 42 ); // `42` won't have `toUpperCase()`
} catch (err) {
  console.log( err ); // TypeError (from `toUpperCase()` call)
}
```

### Delegating Generators

1. 语法 `yield *`  

```javascript
function *foo() {
    yield 3;
    yield 4;
    return 100; // 这里的返回值迭代时被忽略，因为 yield* 相当于 for...of；但是作为函数返回值可用
}

function *bar() {
    yield 1;
    yield 2;
    // 注意，此处x赋值方式仅仅取值 yield* 函数的返回值，不会像 yield 接受下一次 next(val) 入参
    const x = yield *foo(); // `yield *` delegates iteration control to `foo()`
    console.log(x) // x 为 yield* 返回值 100
    yield 5;
}

for (var v of bar()) {
    console.log( v );
}
// 1 2 3 4 5
```
```javascript
function* foo() {
  const z = yield 3
  const w = yield 4
  console.log(`z:${z}, w:${w}`)
}

function* bar() {
  const x = yield 1
  const y = yield 2
  yield *foo()
  const v = yield 5
  console.log(`x: ${x}, y: ${y}, z: ${z}`)
}

const it = bar()
it.next() // {value: 1, done: false}
it.next('X') // {value: 2, done: false}
it.next('Y') // {value: 3, done: false}
it.next('Z') // {value: 4, done: false}
it.next('W') // {value: 5, done: false}
// z: Z, w: W
it.next('V') // {value: undefined, done: false}
// x: X, y: Y, z: Z
```

2. `yield*` 错误处理
```javascript
function* foo() {
  try {
    yield 2
  } catch(err) {
    console.log(`foo caught: ${err}`)
  }
  yield //pause
  throw 'Oooooops!' // throw another error
}

function *bar() {
  yield 1
  try {
    yield* foo()
  } catch(err) {
    console.log('bar caught:' + err)
  }
}

const it = bar()
it.next() // {value: 1, done: false}
it.next() // {value: 2, done: false}
it.throw('Uh oh') // 此异常会被foo捕获 => foo caught: Uh oh! 假如foo不catch，则错误会被外层bar捕获，foo终止运行
it.next() // {value: undefined, done: true} -> 无错误,继续执行
// bar caught: Oooooops! 
```

3. generator 应用实例
```javascript
const fetch = require('isomorphic-fetch')

function makeAjaxCall(url, cb) {
  fetch(url)
  .then(resp => resp.text())
  .then(cb)
}

/*
function request(url) {
  makeAjaxCall(url, (data) => {
    it.next(data)
  })
}
*/
const cache = {}
function request(url) {
  if(cache[url]) {
    setTimeout(() => {// why setTimeout? generator is not in a paused state yet(start later)!
      it.next(cache[url])
    }, 0)
  } else {
    makeAjaxCall(url, (data) => {
      cache[url] = data
      it.next(data)
    })
  }
}

function* main() {
  const res1 = yield request('http://www.dianwoda.com')
  const res2 = yield request('http://www.baidu.com')

  console.log(res1, res2)
}

const it = main()
it.next()
// 注意 setTimeout 的 deferral hack 方式
```
