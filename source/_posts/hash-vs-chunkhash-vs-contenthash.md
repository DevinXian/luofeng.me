---
title: hash-vs-chunkhash-vs-contenthash
date: 2019-06-30 10:35:23
tags: webpack hash chunkhash contenthash 缓存 webpack优化
---
#### 需求

1. webpack 打包文件请求充分利用浏览器缓存
2. 让浏览器能识别任意部分的变化

#### 正文

1. hash: 全局唯一 hash，所有文件共享；如果任意文件发生更改，则 hash 更改，显然不适用于线上使用
2. chunkhash: 每一个 chunk 有独立的 hash，比如常见的 vendor 和 app 代码分开打包（早先的 CommonChunkPlugin），单独修改 app 业务代码，则 vendor 不变，浏览器可以充分利用 vendor 缓存，只请求 app 最新代码
3. contenthash: 基于内容生成 hash。常见于使用了 ExtractTextWebpackPlugin 或 MiniCssExtractPlugin 的时候。考虑如下场景：如果 js 引入了 css，js chunk 内容不变，则 chunkhash 不变，对应引入的 css 文件由于和 js 处于同一个chunk，则 chunkhash 保持一致，因而 css 中的变化不能体现在 hash 中，导致浏览器不能获取到最新样式，针对 extract 出来的 css 文件使用 contenthash 皆可避免这种问题！

#### 解析见参考

1. [参考一](https://medium.com/@sahilkkrazy/hash-vs-chunkhash-vs-contenthash-e94d38a32208)
2. [参考二](https://github.com/jiangjiu/blog-md/issues/49)

