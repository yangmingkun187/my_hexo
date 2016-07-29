title: angularjs实践（有坑就记录）
date: 2015-09-21 14:33:35
tags: "Angularjs"
categories: "Angularjs"
---
自己对ng1的实践
<!-- more -->
使用Angularjs也有几个月了，在对这个比较新奇的框架赞美之外，也想把自己遇到的坑记录下来。
### 最近写了一个angular的调用模态框+在模态框中滚动分页加载的组件

[源码地址](https://github.com/yangmingkun187/angular_modal_scroll)
[demo地址](http://jasonyangblog.com/angular_modal_scroll/index.html)

### 对ng-click 进行trigger

有一次做注册模块的时候，当用户出现登录注册信息错误或者网络错误的时候，异步请求回来会触发一次trigger事件，当时是这样写的：

``` javascript
angular.element('.refresh').trigger('click');//refresh上绑了ng-click
```
可是测试的时候发现，这并没有什么卵用，
度娘了半天也没有什么卵用，果断翻墙，原因如下：
你以为当所有的数据在页面中加载完成后，你只需要trigger就可以了，可是这样并不行，我们先了解下angular的机制。
Angular的数据绑定是通过持续的digest循环实现的。基于此，Angular是‘动态’的，所以当我们用<code>angular.element('.refresh')</code>的时候，臣妾根本找不到好么。所以我们只需要把<code>angular.element('.refresh')</code>的调用放在当前digest循环外就行了，$timeout可以做到这一点。ex：
``` javascript
$timeout(function() {
  angular.element('.refresh').trigger('click');
}, 100);
```

### UI加载的时候会闪烁

当使用双花括号或者使用ng-if、ng-repeat等时候会出现ui闪烁的情况。

虽然angular的自动数据绑定功能是亮点，然而，他的另一面是：在angular初始化之前，页面中可能会给用户呈现出没有解析的表达式。当DOM 准备就绪，Angular计算并替换相应的值。这样就会导致出现一个丑陋的闪烁效果。

双花括号这种表达式很好解决，使用ng-bind就行了，

对于ng-if、ng-repeat等指使用ng-cloak就行了，工作原理就是在初始化阶段inject了css规则，或者你可以包含这个css 隐藏规则到你自己的stylesheet。Angular就绪后就会移除这个cloak样式，让我们的应用(或者元素)立刻渲染。

### 修改了变量但是视图并没有更新

与上面那个trigger有点点类似，造成这样的原因就是$apply没有执行，对于大多数包含angular功能的操作，$apply是内置在这些功能当中的，操作便执行，但是如果调用了setTimeout，使用了jquery的事件，从而改变了变量，但是这个时候$apply并没有执行，所以导致界面没有更新
举个栗子：
``` javascript
setTimeout(function() {
  $scope.time = new Date()
}, 1000);
```
这样的代码，视图是不会更新的
``` javascript
setTimeout(function() {
  $scope.$apply(function() {
    $scope.time = new Date();
  });
}, 1000);
```
不过，这样并不是最好的形式，angular有一个$timeout的内置服务：
``` javascript
$timeout(function() {
  $scope.time = new Date();
});
```
在controller中使用$timeout的时候记得把他注入进去
### ng-options
记得有天晚上就是因为这个加班，当时做了一个以小时为单位的下拉列表（0-23），一个以分钟为单位的下拉列表（0，15，30，45），还有一个天数的（1，2，3）
传过来的是类似这样的数组<code>[0,15,30,45]</code>通过ng-options出来的option的value，我本以为ng-model绑定的就是数组里面的值，因为小时这个拉下列表是符合的，—。—，但是其他的不行，
当时不停变换ng-options的姿势。。。。还是拿不到。
所以记住这里的值永远都会是 AngularJS 内部元素的 **索引**，并不是value。
当时是这样解决的，有多少个数组元素就写了多少个option。。。。。。。。。。
### ng-repeat
当repeat一个含有相同值的数组的时候，会报错，后来发现，其实ng-repeat需要根据一个唯一的key来repeat，默认就是数组中每个值的本身。所以数组中出现相同的值的时候，这个key就不唯一了，就会报错。
解决办法：
``` javascript
ng-repeat = "item in arrays track by $index"
```
根据数组的index索引去repeat，就可以了。
另外也只有在普通数据类型字符串，数字等才会出现这个问题
### ng-if ng-show
ng-if == '有没有'  ng-show == '看不看得见' 
之前业务中有一个改变id 然后调用directive的功能,但是发现id改变后,directive并没有实时的更新,因为directive其实算是一个实例,所以我们可以用ng-if来重新调用它
