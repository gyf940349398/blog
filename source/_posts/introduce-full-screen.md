---
title: 介绍浏览器全屏模式
date: 2018-08-15 15:07:59
categories: 浏览器
tags: 全屏
---

> 全屏 API 为使用用户的整个屏幕展现网络内容提供了一种简单的方式。这种API让你可以简单地控制浏览器，使得一个元素与其子元素，如果存在的话，可以占据整个屏幕，并在此期间，从屏幕上隐藏所有的浏览器用户界面以及其他应用

### 概览

- document.fullscreen
- document.fullscreenElement
- document.fullscreenEnabled
- elem.requestFullscreen
- document.exitFullscreen
- document.onfullscreenchange
- document.onfullscreenerror

### 属性
#### document.fullscreen
用于检测当前文档是否处于全屏模式，返回值为布尔类型

#### document.fullscreenElement
当前激活全屏模式的元素

#### document.fullscreenEnabled
当前文档是否支持全屏

### 方法
#### 请求全屏模式
一个全屏的请求应该由一个具体的元素发出，以video元素为例，请求方式如下：

```html
<video controls id="video">
  <source src="somevideo.webm"></source>
  <source src="somevideo.mp4"></source>
</video>
```

```javascript
var elem = document.getElementById("video");
elem.addEventListener('click',function () {
	if (elem.requestFullscreen) {
    elem.requestFullscreen();
  }
});
```

> 全屏请求必须在事件处理函数中调用，否则将会被拒绝

#### 退出全屏模式
- 方式一：按 `ESC` 或 `F11` 键退出全屏
- 方式二：`[keydown event]`  + `document.exitFullscreen`自定义全屏退出逻辑

```javascript
// 按Enter建退出全屏
document.addEventListener("keydown", function(e) {
  if (e.keyCode == 13) {
    document.exitFullscreen();
  }
}, false);
```

> exitFullscreen方法只存在于document对象上，而不存在与elem

### 事件
#### onfullscreenchange
onfullscreenchange事件触发的时刻的有2个：一是进入全屏时，二是退出全屏时。也就是说如果全屏模式的状态发生了改变，那么onfullscreenchange事件就会被触发

```javascript
document.onfullscreenchange = function ( event ) {
  console.log("全屏模式状态改变");
};
```

#### onfullscreenerror
当尝试在不支持全屏模式的元素上请求全屏时，则会发生错误，这个错误会触发onfullscreenerror事件

```javascript
document.onfullscreenerror = function ( event ) { 
  console.log("全屏模式失败");
};
```

验证onfullscreenerror事件最简单的方式就是：在事件监听器之外发起全屏请求。
因为**全屏请求必须在事件处理函数中调用，否则将会被拒绝。**在拒绝的同时会产生一个错误，这个错误会触发onfullscreenerror事件

```javascript
document.onfullscreenerror = function ( event ) { 
  console.log("全屏模式失败");
}; 

// requestFullscreen()将会失败，因为它在事件处理器之外
document.documentElement.requestFullscreen();
```

### 全屏样式
Gecko会自动为进入全屏模式的元素添加额外样式：`width: 100%; height: 100%;` ，目的是使其铺满整个屏幕；而webkit没有这种默认行为，所以需要我们手动补充这个样式

```css
#video:-webkit-full-screen {
  width: 100%;
  height: 100%;
}
```

### 兼容性

#### PC端
![兼容性-PC端](/uploads/js/fullscreen-pc.png)

#### 移动端
![兼容性-移动端](/uploads/js/fullscreen-mb.png)

### 结语
本文绝大部分参考MDN文档，目的是整理浏览器全屏模式相关的API，非原创
