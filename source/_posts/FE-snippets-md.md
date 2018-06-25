---
title: FE-snippets.md
date: 2018-03-03 21:20:24
tags:
---

1. ```<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
   <meta name="apple-mobile-web-app-capable" content="yes" />```

2. margin 百分值是相对于最近的块级容器计算的，而不是自身宽度
3. 等分宽度布局n列，需要利用容器的 overflow + margin负值 来调整宽度，使其等分又不影响布局，此时n*(each-width + gap) > container-width
4. 三列自适应布局（中栏宽度自适应）
    1. 古老的方法：两个浮动元素+一个block元素，浮动元素在前书写，，则block元素会自适应至浮动元素之间的空间，[demo](http://www.webfront-js.com/showDemo/float&clear%E5%AE%9E%E7%8E%B0%E6%A8%AA%E5%90%91flex%E5%B8%83%E5%B1%80.html)
    2. 两个浮动元素(或者两个伪元素)+一个block元素，浮动元素书写在后，两个float块需要固定长度。原理是利用float浮动到左右，然后通过设置margin负值来达到布局效果，举例：两个float元素，一个margin-left:-100%,一个margin-left:-(self-width),不知道是不是所谓“双飞翼”布局
    3. <<CSS设计指南>>里的方法，利用容器内子元素margin+容器-margin的方法来实现自适应布局，原理相同
