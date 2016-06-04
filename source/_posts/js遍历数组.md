title: js一些基础的东西
date: 2014-11-17 17:52:04
tags: "Javascript"
categories: "Javascript"
---
记录一些js的奇技淫巧
<!-- more -->
### 数组元素洗牌
``` javascript
var array = [1,2,3,4,5];
var randomArray = function(array) {
    return array.sort(function() {
        Math.random() - 0.5
    })
}
console.log(randomArray(array));
```
### 数组降维
利用concat转换:
``` javascript
function reduceArray(arr) {
    var reducedArray = [];
    for(var i = 0, len = arr.length; i < len; i++) {
        reducedArray = reducedArray.concat(arr[i]);
    }
    return reducedArray;
}
```
更巧妙的利用apply来实现
``` javascript
function reduceArray(arr) {
    return Array.prototype.concat.apply([],arr);
}
// 这里利用apply方法的第一个参数回座位被调用函数的this值
// 第二个参数(一个数组,或者类数组的对象)会作为被调用对象的arguments值
// 也就是说该数组的各个元素将会依次成为被调用函数的各个参数.
```
### js取整

``` javascript
parseInt(1.4) === ~~1.5;
```
### 将DOM元素的数组转换成数组
当使用document.querySelector("div")函数时,获得的是DOM数组,也就是NodeList对象,但是这个对象不具有数组的函数功能,
比如sort() map()等.为了使用这些方法,我们需要将其转化成数组.
``` javascript
var domDiv = document.querySelector("div");
var array = [].slice.call(domDiv);
// or
var array = Array.from(domDiv);
```
### 复制一个数组（这里并非索引，而是一个新的数组）

``` javascript
var originArray = [1,2,3];
var newArray = originArray.slice();
originArray.push(4);
console.log(originArray);// [1,2,3,4]
console.log(newArray);// [1,2,3]
```

### js遍历数组

##### 一般写法

``` javascript
var array = [1,2,1,4,5,2,3];
for (var i = 0; i < array.length; i++) {
	// array[i]
}
```

##### 比较好的写法

上面的写法由于每次循环都会去计算array.length的值，所以推荐这样写：

``` javascript
var array = [1,2,1,4,5,2,3];
for (var i = 0, len = array.length; i < len; i++) {
	// array[i]
}
```

##### 如果确认数组中没有假值元素（如'' 0 undefined）有一种更好的写法：

``` javascript
var array = [1,2,1,4,5,2,3];
for (var i = 0, item; item = array[i++];) {
	// array[i]
}
```
