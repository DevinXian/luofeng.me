---
title: ES6-Generator-Runner
date: 2017-07-11 21:03:59
tags: ES6 Generator
---
1. GeneratorFunction 生成器函数，声明语法为：`function* (){}`; 生成器函数执行才会生成"生成器(Generator)"，即：生成器构造函数的对象，简称执行对象
```javascript
function* genFn(){
	yield 1
	return 2
	//...more
}
const gen = genFn()
const ret = gen.next()
if(ret.done) return

```

2. Generator 配合 ES2015 `yield` 关键字实现类协程异步处理方案，基本语法逻辑为：
```javascript
const input = yield output
```
即：`yield`可以将一个任意值带出协程，主线程也可以通过生成器的`next`方法把任意值带入生成器的执行对象中。进一步讲：生成器从协程切出执行对象并带出 `output`到主线程中，主线程进行同步或异步处理之后，
将处理之后的值通过生成器执行对象`.next(val)`传入生成器执行对象。

