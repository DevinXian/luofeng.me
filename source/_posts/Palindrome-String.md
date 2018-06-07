---
title: Palindrome-String
date: 2018-06-04 11:01:50
tags: 算法,回文字符串
---
## 求最长回文子串解法

1. 基础解法: 循环主字符串索引，在剩余部分查找最大回文子串；效率很低，回文判断没有和外部循环联系起来，复杂度提升一个level
```javascript
function maxPalindrome(src) {
  let dist = ''
  let max = 1

  for (let idx = 0; idx < src.length; idx++) {
    for (let end = idx; end < src.length; end++) {
      let tmp = src.slice(idx, end)
      if (isPalindrome(tmp)) {
        if (max < tmp.length) {
          max = tmp.length
          dist = tmp
        }
      }
    }
  }
  return dist
}

function isPalindrome(str) {
  const mid = Math.ceil(str.length / 2) - 1
  for (let i = 0; i <= mid; i++) {
    let endIndex = str.length - 1 - i
    if (str.charAt(i) !== str.charAt(endIndex)) {
      return false
    }
  }
  return true
}

console.log(maxPalindrome('aabbabcbddddbfeabc')) // bddddb
```

2. 动态规划解法：时间复杂度 O(n2)
```javascript
/** 
 * dp[j][i] = str[i] === str[j] && dp[j + 1][i - 1] 转移方程
 * dp[j][i] 代表索引j到索引i的子串是否是回文串
 */
function maxPalindrome(str) {
  const len = str.length
  let maxLen = 1
  let start = 0 // 最长回文子串起点
  // 此处也可用二位数组，但须注意 new Array(n) 得到的未赋值数组forEach遍历会失败，要使用for
  const dp = {} 
  for (let i = 0; i < len; i++) {
    const item = {}
    for (let p = 0; p < len; p++) {
      item[p] = false
    }
    dp[i] = item
  }

  for (let i = 0; i < len; i++) { // 索引全量移动，限定j移动范围；每次循环开放一个右侧位置给j使用
    for (let j = 0; j <= i; j++) { // 索引在[0, i]移动, 每次查看j到i是否为回文字符串;每次循环减少一个左侧位置,结合外部循环，达到遍历效果
      if (i - j < 2) {
        dp[j][i] = str[i] === str[j]
      } else {
        dp[j][i] = str[i] === str[j] && dp[j + 1][i - 1] // 转移方程, j+1 和 i-1的位置，上次循环肯定访问过,因为索引范围是不断扩大的
      }
      if (dp[j][i] && maxLen < i - j + 1) { // 更新结果
        maxLen = i - j + 1
        start = j
      }
    }
  }

  return str.substr(start, maxLen)
}

console.log(maxPalindrome('abbaabccbaabc'))
// baabccbaab
```
3. Manacher 算法：时间和空间复杂度O(n)
