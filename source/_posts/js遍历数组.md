title: js一些基础的东西
date: 2014-11-17 17:52:04
tags: "Javascript"
categories: "Javascript"
---
记录一些js的东西
<!-- more -->
### 快速理解闭包
各种专业文献上的闭包定义非常抽象，很难看懂。我的理解是，闭包就是能够读取其他函数内部变量的函数。
由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。
所以，在本质上，闭包就是将函数内部和函数外部连接起来的纽带。
要理解闭包，首先必须理解Javascript特殊的变量作用域。

#### 变量作用域
变量的作用域无非就是两种：全局变量和局部变量。
Javascript语言的特殊之处，就在于函数内部可以直接读取全局变量。
例如:
``` javascript
var name='jason';

function showName(){
    console.log(name);
}

showName(); // jason
```
在函数外部自然无法读取函数内的局部变量。
例如:
``` javascript
function showName(){
    var name='jason'; //这里有需要注意，函数内部声明变量的时候，如果不用var的话，你实际上声明了一个全局变量！
}
console.log(name); // undefined
```

#### 如果我想在外部读取局部变量呢?
出于种种原因，我们有时候需要得到函数内的局部变量。
但是，前面已经说过了，正常情况下，这是办不到的，只有通过变通方法才能实现。
那就是在函数的内部，再定义一个函数。
``` javascript
function person(){
    var name='jason';
    function showName(){
        console.log(name);
    }
}
```
在上面的代码中，函数f2就被包括在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。
但是反过来就不行，f2内部的局部变量，对f1 就是不可见的。
这就是Javascript语言特有的“链式作用域”结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量。
所以，父对象的所有变量，对子对象都是可见的，反之则不成立。
既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！
试一试:
``` javascript
function person(){
    var name='jason';
    function showName(){
        console.log(name);
    }
    return showName;
}
var p = person();
p(); // jason
```
* 这就是闭包

#### 那闭包的用途呢?
闭包可以用在许多地方。
它的最大用处有两个:
1.一个是前面提到的可以读取函数内部的变量;
2.另一个就是让这些变量的值始终保持在内存中;
怎么来理解这句话呢？请看下面的代码。

``` javascript
function person() {
    var age = 19;
    
    addAge = function() {
        age += 1;
    }
    
    function showAge(){
        console.log(age);
    }
    return showAge;
}
var p = person();
p(); // 19
addAge();
p(); // 20
```
在这段代码中，p实际上就是闭包showAge函数。它一共运行了两次，第一次的值是19，第二次的值是20。
这证明了，函数person中的局部变量age一直保存在内存中，并没有在person调用后被自动清除(js的垃圾回收机制)。
为什么会这样呢？
原因就在于person是showAge的父函数，而showAge被赋给了一个全局变量，
这导致showAge始终在内存中，而showAge的存在依赖于person，因此person也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。
这段代码中另一个值得注意的地方，就是addAge这个函数，因为在addAge前面没有使用var关键字，因此addAge是一个全局变量，而不是局部变量。
其次，addAge的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以addAge相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。
其实闭包也不是那么不好理解,不要被书上抽象的概念吓到..

#### 使用闭包的注意点
1. 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
2. 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

#### 闭包的例子
``` javascript
var name = "The Window"; 
var object = { 
    name : "My Object", 
    getNameFunc : function(){ 
        return function(){ 
            return this.name;  // 这里的匿名函数他的this指向window
        }; 
    } 
}; 
console.log(object.getNameFunc()()); //The Window
```

### 数组合并
一般是使用concat函数来进行数组合并,但是如果两个数组很大的时候,会消耗大量的内存来存储新创建的数组.
可以使用:
``` javascript
var array1 = [1,2,3];
var array2 = [4,5,6];
console.log(array1.push.apply(array1,array2));
```
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
### 数组截断
长度为5的数组,但是现在只想用到前三个元素, 可以使用 <code>array.length = 3</code>

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

### 数字数组去除,效率很高(先把数组排序，然后比较相邻的两个值)
``` javascript
Array.prototype.unique = function()
{
	this.sort();
	var resultArray=[this[0]];
	for(var i = 1; i < this.length; i++) {
		if( this[i] !== resultArray[resultArray.length-1]) {
			resultArray.push(this[i]);
		}
	}
	return resultArray;
}
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
