---
title: 理解几种解决跨域问题方法的原理
date: 2019-09-04 16:52:05
categories: 计算机网络
tags: 跨域
---

## 跨域的定义？
两个url，协议、域名、端口，只要任意一个不同，就属于跨域

## 跨域有哪些限制？
1. Cookie无法读取
2. Dom无法获取
3. 窗口无法通信
4. Ajax请求无法发送


## 有哪些解决跨域的方法？
按解决的问题类型进行分类

### 解决Cookie无法获取的问题
#### document.domain
原理及特点：
1. 子域名下的页面只要将`document.domain`设置为一级域名，就可以访问保存在一级域名下的Cookie；另外，将Cookie统一保存在一级域名下，也有利于子域名页面访问和使用

### 解决Dom无法获取的问题
#### document.domain
原理及特点：
1. 父窗口、iframe窗口（或window.open打开的窗口）只要将各自的`document.domain`设置为一级域名，就可以获取到对方的Dom

### 解决跨域窗口无法通信的问题
#### window.name
原理及特点：
1. 一个窗口只要设置了`window.name`，即便这个窗口后来又加载了另一个页面，`window.name`会依然保存之前的值，而不是被情况
2. `window.name`同样存在跨域限制，那么只有窗口之间不存在跨域就能获取到对方的`window.name`，所以我们可以这么做：
iframe首先加载一个与父窗口跨域的页面，在这个页面中给`window.name`赋值，然后iframe加载另一个与父窗口不存在跨域的页面，这时就可以那到`window.name`了，从而达到通信的目的
3. `window.name`容量大，可以保存较长的字符串

#### window.postMessage
原理及特点：
1. H5新增的Api，用于窗口之间通信（父窗口与iframe窗口，或window.open打开的窗口）

#### location.hash & onhashchange
原理及特点：
1. 窗口Url的hash发生改变时，页面不会刷新
2. 窗口Url的hash发生改变时，onhashchange事件回调被执行，并且参数中会包含新的hash值

### 解决Ajax无法发送的问题
#### JSONP
原理及特点：
1. 加载无论哪一方的JS脚本都不会存在跨域限制
2. 服务端将数据放入前端已事先声明的回调函数中并返回，形如：`callback({name: 'abc'})`
3. 只支持Get请求，不支持Post请求
4. 兼容不支持CORS的老式浏览器

#### Websocket
原理及特点：
1. 不存在跨域限制

#### CORS
原理及特点：
1. XMLHttpRequest原生支持跨域请求，并且支持Post请求
2. 服务端通过响应头`Access-Control-Allow-Origin`来告知浏览器哪些域名下的页面可以使用服务器返回的资源
3. 如果想要在跨域请求中携带Cookie，一方面需要服务器同意（`Access-Control-Allow-Credentials: true`），另一方面也要指定请求携带（`xhr.withCredentials = true`），否则即使服务器同意，浏览器也不会发送Cookie
4. 如果跨域请求需要携带Cookie，那么`Access-Control-Allow-Origin`就不能设置为通配符（*），而必须设置为发起这个请求的那个Origin

未完待续...