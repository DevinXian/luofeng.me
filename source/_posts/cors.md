---
title: 浏览器内置对象 CORS
description: 使用浏览器内置对象 XMLHttpRequest 及 XDomainRequest 实现 CORS
date: 2017-06-27 17:30:31
tags: CORS
---


1. 浏览器的同源策略限制（规定）了文档和脚本如何同另外一个源进行交互。默认情况下，必须是同源（协议、主机名、端口完全一致）才能直接访问

2. 跨域解决方案很多，各有各的限制，网上资料也较多: 设置图片 src 方式及 JSONP 等等，不一而足；这里我们主要说说浏览器内置 CORS (Cross Origin Resource Sharing） 支持

3. 浏览器内置 CORS 机制起作用的前提是服务端开启了 CORS 机制，常见的处理方式是在响应头进行设置( nginx 代理时候也要记得设置)
```javascript
response.setHeader('Access-Control-Allow-Origin', '*');//如需要指定具体域名，替换 * 即可
response.setHeader('Access-Control-Allow-Headers‘， 'X-PINGARUNNER');
response.setHeader('Access-Control-Allow-Methods', 'GET,POST,OPTIONS');
//Max-Age, Content-Length, etc.
```


4. 对于支持 XMLHttpRequest 的浏览器，添加属性 `withCredentials = true` 可以让跨域请求带上 Cookie 等认证信息（在不同子域名设置顶级域名 Cookie，从而实现信息共享还是很常见的）；而且还可以在响应中返回 Cookie（IE10+，Chrome有效）
```javascript
var xhr = new XMLHttpRequest();
var url = 'http://www.example.com/sub';
xhr.open('GET', url, true);//POST 等方式也可
xhr.withCredentials = true
xhr.send(null);
//more readyState handling
```


5. 如果使用了 jQuery 则如下所示
```javascript
var url = 'http://www.example.com/sub';
$.ajax({
  url: url,
  type: 'GET',
  dataType: 'json',//或者使用 $.getJSON 更简便
  xhrFields: {
    withCredentials: true //重点在这里设置
  },
  success: function(data){
    //do sth.
  },
  error: function(){
    //handle error
  }
})
```


6. IE8 和 IE9 想要实现跨域请求，则必须使用另外一个东西 **XDomainRequest**（IE10 也支持，不过更推荐使用 XMLHttpRequest），而且有很多 [限制](https://blogs.msdn.microsoft.com/ieinternals/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds/)，比如：只有 GET/POST ，无法携带 Cookie，无法自定义 Headers，响应 Cookie 也无法获取等。这时候可以考虑使用加密的 QueryString 来实现部分数据交换，还可以考虑其他安全方案，比如 Auth 2.0。下面仅是一例：
```javascript
var xdr = new window.XDomainRequest();
xdr.open('get', url);
xdr.onerror = function(){
  //handle error
}
xdr.onload = function(){
  var data = xdr.responseText;
  //handle data
}
setTimeout(function(){//MDN suggestion
  xdr.send('param');//param should be string, such as: '123', 'abc', 'name=luo'
}, 0)
```


7. IE7 及以下没有内置对象可用于 CORS

8. 最新的 Fetch API，有个 credentials 选项可以控制是否发送 Cookie 等，mode 选项可以控制是否是 CORS 请求；希望将来浏览器在这方便能越来越统一，毕竟方便也是一种生产力


