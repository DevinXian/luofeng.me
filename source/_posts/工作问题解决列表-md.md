---
title: 工作问题解决列表.md
date: 2018-02-23 21:33:24
tags:
---
1. 丢失dubbo provider连接，上次是机器重启或者其他原因，全部断掉，重启服务解决；没有再出现
2. 短信不能发送，参数类型有误；确保白名单没有过期
3. 单机访问正常，集群不正常，间歇性404,优先考虑是不是有服务挂掉，查服务管理错误日志(pm2等)；多半是中间件出问题了
4. 每次提交代码，中间件是重中之重！
5. promise-request 获取图片，pipe 到文件流没有问题；通过获取数据，比如await结果，或者.then(() => fs.writeFile...)均有问题，
底层不详。如果上传到ali-oss, 用 request.on('response', (response) => ...response是可读流)
6. 页面存在多个分页时候，如果重用分页变量，一定不能同时获取列表数据，存在覆盖情况
7. **important:** chrome input 背景色不能被覆盖问题，hack方案：[参考](https://webagility.com/posts/remove-forced-yellow-input-background-in-chrome)
```
// 针对 chrome
    input:-webkit-autofill {
        -webkit-box-shadow: 0 0 0px 1000px white inset;
        &:hover, &:focus {
            -webkit-box-shadow: 0 0 0px 1000px white inset;
        }
    }
```
