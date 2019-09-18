---
title: 理解防抖和节流
date: 2019-09-04 17:34:20
categories: Javascript
tags: 
---

## 防抖
### 概念
相邻的两次函数执行间隔不得小于n秒，否则重新计时
### 实现
在每次函数执行之前先清空上一次设置的定时器，原因是：如果执行间隔大于n秒，那么先前的定时任务一定已经执行完毕，当执行`clearTimeout`时其实并没有定时器可清除；否则定时器就会被清除，然后重新计时
### 代码
```javascript
function debounce(fn, wait, immediate) {
    var context = this;
    var timer = null;
    return function() {
        // 判断首次是否需要立即执行
        if (immediate) {
            fn.call(context, ...arguments);
            immediate = false;
        }

        // 清除定时器
        clearTimeout(timer);
        timer = setTimeout(function() {
            fn.call(context, ...arguments);
        }, wait);
    };
}
```

## 节流
### 概念
n秒内函数只能被执行一次
### 实现
在每次函数执行之前先判断是否存在定时器，存在则跳过本次执行，否则设置新的定时器
### 代码
```javascript
function throttle(fn, wait, immediate) {
    var context = this;
    var timer = null;
    return function() {
        // 判断首次是否需要立即执行
        if (immediate) {
            fn.call(context, ...arguments);
            immediate = false;
        }

        // 如果当前存在定时器，返回；否则设置定时器
        if (timer) return;

        timer = setTimeout(function() {
            fn.call(context, ...arguments);
            // 函数执行完毕后，清除定时器
            clearTimeout(timer);
            timer = null;
        }, wait);
    };
}
```