---
title: 理解浏览器缓存机制
date: 2019-08-23 10:40:29
categories: 浏览器
tags: 缓存
---

### 1.浏览器缓存的作用
浏览器缓存通俗讲就是本地保存一份资源的副本，当浏览器再次请求这个资源时，如果发现本地的副本尚未过期，那么请求就无需发向服务器，资源从本地获取。亦或者本地的副本已过期（即超过当初指定的过期时间），那么浏览器会在请求中携带与资源相关的信息（最后修改时间、资源摘要等）发向服务器，如果这时服务器发现这个副本只是超过了当时指定的过期时间，并没有真正过期（服务器远端资源没有更新），那么服务器就会返回304状态码，并且响应体中不携带资源文件，浏览器继续使用本地的资源副本；但如果服务器发现这个副本真的过期了（也就是远端有更新），那么服务器就会返回200状态码，并且在响应体中携带资源文件，浏览器用最新的文件更新本地副本

### 2.浏览器缓存有哪些优点
1. 减少了网络请求次数（本地副本未过期，可以使用，请求不用发至服务器）
2. 降低了服务器负载（请求次数减少了，服务器负载自然也就降低了）
2. 缩短了资源响应时长（从本地获取资源要比从网络获取速度快得多）
3. 降低了网络带宽（即便协商缓存是一次完整的请求响应，但响应体中可能不包含资源文件，进而响应的体积缩小，带宽降低）

### 3.浏览器缓存有哪些缺点
1. 如果资源使用了强缓存，当远端的资源发生了更新时，则需要用户通过强制刷新浏览器的方式来获取最新资源；否则在本地副本过期之前，浏览器总会从缓存中拿这个资源，从而产生资源更新不及时的问题
2. 当浏览器渲染一个页面时，浏览器会把所有该页面存在硬盘中的缓存文件放入内存中，目的是为了当tab页刷新时，资源请求能够快速响应，毕竟memory cache与disk cache相比，优先级更高，读取速度更快，但这样会带来内存消耗的问题

### 4.强缓存机制
- 如果给某个资源指定过期时间，从浏览器缓存这个资源后，一直到这个资源过期之前，浏览器针对这个资源的请求使用的都是强缓存机制
- Expires、Cache-Control这两个Header字段用于告知浏览器缓存何时过期
- Expires是Http 1.0的Header，Cache-Control是Http 1.1的Header
- Expires的值是一个绝对时间，Cache-Control的值是一个时间长度(秒)，例如：max-age: 3600000
- 两者同时存在时，Cache-Control的优先级大于Expires
- Cache-Control的值除了一个时间长度，还可能有一个缓存权限，它的值有这几种：public、private、no-cache、no-store
- public表示响应链中的任何一方都可以缓存这个资源，包括：浏览器、代理服务器、缓存服务器等
- private表示只有浏览器可以缓存这个资源，其他方不可以
- no-cache表示任何方都可以缓存这个资源，但是缓存后立即过期，即任何方在使用缓存时需要与服务器确认
- no-store表示任何方不得缓存这个资源，包括浏览器

### 5.协商缓存机制
- 浏览器携带着资源的相关信息去与服务器进行确认，如果资源未过期则浏览器使用本地的资源副本，否则服务器会返回最新资源，浏览器用其更新本地副本
- 服务器如果想对某个资源使用协商缓存机制，那么就会在响应头中添加Last-Modified或Etag字段，或者两者同时存在
- Last-Modified是资源的最后修改时间，精确到秒级
- Etag是资源的MD5摘要，资源只要被修改过，Etag就会改变
- 浏览器会将这两个字段的值记录下来，并且会在下次请求时在请求头中带上这两个字段
- 服务器根据这两个Header字段判断浏览器本地的资源副本是否已过期，未过期则返回304，并且不携带资源文件；否则返回200，并携带资源文件
- Etag在某些场景要比Last-Modified更精确，例如：如果资源在一秒内被修改多次，那么Last-Modified是不会更新的，因为它只能精确到秒，而Etag是只要资源被修改过，它就会更新


未完待续...