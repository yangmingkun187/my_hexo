title: js遍历数组
date: 2014-11-17 17:52:04
tags: "Javascript"
categories: "Javascript"
---

### 一般写法

``` javascript
var array = [1,2,1,4,5,2,3];
for (var i = 0; i < array.length; i++) {
	// array[i]
}
```

### 比较好的写法

上面的写法由于每次循环都会去计算array.length的值，所以推荐这样写：

``` javascript
var array = [1,2,1,4,5,2,3];
for (var i = 0, len = array.length; i < len; i++) {
	// array[i]
}
```

### 如果确认数组中没有假值元素（如'' 0 undefined）有一种更好的写法：

``` javascript
var array = [1,2,1,4,5,2,3];
for (var i = 0, item; item = array[i++];) {
	// array[i]
}
```
