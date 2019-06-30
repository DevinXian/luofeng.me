---
title: hash-vs-chunkhash-vs-contenthash
date: 2019-06-30 10:35:23
tags: webpack hash chunkhash contenthash 缓存 webpack优化
---
#### 需求

1. webpack 打包文件请求充分利用浏览器缓存
2. 让浏览器能识别任意部分的变化

#### 准备

1. npm i -D webpack webpack-cli css-loader mini-css-extract-plugin
2. webpack 配置文件

    ```javascript
    // webpack.config.js
    const path = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin')

    module.exports = {
        entry: {
            vendor: './src/vendor.js',
            main: './src/index.js'
        },
        output: {
            path: path.join(__dirname, 'build'),
            filename: '[name].[chunkhash].js'
        },
        plugins: [
            new MiniCssExtractPlugin({
            filename: '[contenthash].css',
            }),
        ],
        module: {
            rules: [
                {
                    test: /\.css$/i,
                    use: [MiniCssExtractPlugin.loader, 'css-loader'],
                },
            ],
        }
    };
    ```

3. 文件列表： src/vendor.js src/index.js src/b.js src/index.css 其中 b.js 和 index.css 被 index.js 引入
4. 在不同的 hash 策略下，修改 js 和 css 并打包，对比生成文件名即可得出如下结论

#### 正文结论

1. hash: 全局唯一 hash，所有文件共享；如果任意文件发生更改，则 hash 更改，显然不适用于线上使用
2. chunkhash: 每一个 chunk 有独立的 hash，比如常见的 vendor 和 app 代码分开打包（早先的 CommonChunkPlugin），单独修改 app 业务代码，则 vendor 不变，浏览器可以充分利用 vendor 缓存，只请求 app 最新代码; 为了保证 module 和 chunk ID 稳定，还需要搭配使用 HashedModuleIdsPlugin 和 NamedChunksPlugin
3. contenthash: 基于内容生成 hash。考虑如下使用了 ExtractTextWebpackPlugin 或 MiniCssExtractPlugin 的场景：
    使用chunkhash：如果 js 引入了 css，只修改了 css 内容，则 chunkhash 变化，即使 css 被抽离，js 和 css 的都会获得生成新的 chunkhash，js 缓存失效了；
    使用contenthash：css 文件变化不会引起 js hash 变化
    
#### 示例及解析见参考

1. [参考一](https://medium.com/@sahilkkrazy/hash-vs-chunkhash-vs-contenthash-e94d38a32208)
2. [参考二](https://github.com/jiangjiu/blog-md/issues/49)

#### 疑问

1. 参考链接有一段话 

        In case of CSS, if you use name.[chunkhash].css in ExtractTextplugin, you will get same resulted hash for both css and js chunk. now if you will change any CSS, your resulting chunkhash will not get changed. So in order to work that properly, you need to use name.[contenthash].css. So that when there is change in CSS, your path for css will change in index.html.

    now if 后面这一句，说 css 被抽离，使用 chunkhash 策略，js 和 css 文件名均不变，css 获取出问题，记忆中遇到过一次；存疑， extract-text-webpack-plugin 只能搭配 webpack 3 及以下，就不深究了；
    MiniCssExtractPlugin 则没有这个问题