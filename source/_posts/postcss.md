---
title: postcss
date: 2018-08-31 11:27:32
tags: css postcss
---
1. PostCSS 配置说明
```javascript
// postcss.config.js
module.exports = {
  plugins: {
    'postcss-import': {},
    'postcss-cssnext': {
      // cssnext deprecated, use postcss-preset-env instead
      browsers: ['last 2 versions', '> 5%']
    }
  }
}
```
2. postcss-import 排序在前，否则 cssnext 只对入口文件起作用，通过`@import`引入文件被忽略 

3. postcss-cssnext 的 option 是传给内部依赖 autoprefixer 的
