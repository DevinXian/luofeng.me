---
title: unleashing-zalgo
date: 2018-06-28 18:03:59
tags:
---
### 同步异步中的魔鬼 ###

```javascript
const fs = require('fs')
const cache = {}
function inconsistentRead(filename, callback) {
  if(cache[filename]) {
    callback(cache[filename])
  } esle {
    // ignore err and repeat reading
    fs.readFile(filename, 'utf8', (err, data) => {
      cache[filename] = data
      callback(data)
    })
  }
}

function createFileReader(filename) {
  const listeners = []
  inconsistentRead(filename, value => {
    listeners.forEach(l => l(value))
  })
  return {
    onDataReady: listener => listeners.push(listener)
  }
}

const reader1 = createFileReader('data.txt')
reader1.onDataReady(data => {
  console.log('First call data: ' + data)
  const reader2 = createFileReader('data.txt')
  reader2.onDataReady( data => {
    console.log('Second call data: ' + data)
  })
})
// output First call data: some data
```
why?
第一次读取是异步，所以添加 listeners 的同步代码会先运行；第二次读取是同步，所以 listener 来不及添加，callback 同步执行掉了，listeners.forEach 时 length 为0
