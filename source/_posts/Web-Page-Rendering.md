---
title: Web-Page-Rendering
date: 2020-01-15 14:32:45
tags: render DOM CCOM
---
1. 路径：DOM -> CCSOM -> Render Tree -> Layout -> Paint -> Composite
2. CSS 和 JS 内联，均同步解析；style 标签样式异步加载，同步解析，并且加载完成之前页面不渲染（Render Tree未构建完成）；script 则会阻塞解析
3. 参考1：https://itnext.io/how-the-browser-renders-a-web-page-dom-cssom-and-rendering-df10531c9969  里面有其他必读链接！
4. 参考2：https://hacks.mozilla.org/2017/09/building-the-dom-faster-speculative-parsing-async-defer-and-preload/