---
title: mictotask-vs-macrotask
date: 2018-05-17 15:37:31
tags: javascript,microtask,macrotask
---

## Javscript Event Loop - Macro task and Mirco task

原文地址[这里](https://stackoverflow.com/questions/25915634/difference-between-microtask-and-macrotask-within-an-event-loop-context)

#### 以下是试译

1. 在 event loop 的一次循环中，对于 macrotask 队列中的任务，当前时间有且仅有一个可以被处理（macrotask 队列在 WHATWG 规范中被叫做 task queue）。当 macrotask 完成，所有已就绪 microtask 会在同一次循环被执行。当 microtask 被处理时， microtask queue 还可以添加更多 microtask， 然后依次执行，直到 microtask 队列被消费完毕

2. 如上设计造成的结果：<br/>
<br/>
如果 microtask 嵌套另外的 microtask，则会导致下一个 macrotask 执行会有长时间的等待，这通常意味着 UI 界面的卡顿和 I/O 的闲置。<br/>
<br/>
Node 由于这个原因，就提供了一种预防机制 `process.maxTickDepth`，避免 process.nextTick 不断添加导致的 microtask 队列过长，默认值 1000。当 microtask 处理个数达到临界值，则切入下一个 macrotask 执行

3. 如何使用
当你需要以同步的方式执行异步代码的时候（比如 Promise 回调就使用 process.nextTick 添加至 microtask），即尽可能早执行 task；否则，就使用 macrotask

4. 实例
macrotask: `setTimeout, setInteval, setImmediate, requestAnimationFrame, I/O, UI rendering<br/>`
microtask: `process.nextTick, Promises, Object.observe, MutationObserver`

*另外参考：[生动例子]( https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)*
