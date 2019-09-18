---
title: 理解几种常见排序算法的实现
date: 2019-09-02 17:49:04
categories: 算法
tags: 排序算法
---

## 冒泡排序
### 原理
待补充...

### 实现
```javascript
function bubbleSort(arr) {
  var len = arr.length;

  if (len < 2) {
    return arr;
  }

  for (var i = len - 1; i >= 2; i--) {
    for (var j = 0; j <= i; j++) {
      if (arr[j] > arr[j+1]) {
        var temp = arr[j+1];
        arr[j+1] = arr[j];
        arr[j] = temp
      }
    }
  }

  return arr;
}
```

## 选择排序
### 原理
待补充...

### 实现
```javascript
function selectSort(arr) {
  var len = arr.length;

  if (len < 2) {
    return arr;
  }

  for (var i = 0; i < len; i++) {
    var minIndex = i;
    for (var j = i; j < len; j++) {
      if (arr[i] > arr[j]) {
        minIndex = j;
      }
      var temp = arr[i];
      arr[i] = arr[minIndex];
      arr[minIndex] = temp;
    }
  }

  return arr;
}
```

未完待续...