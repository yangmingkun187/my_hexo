title: angualr中的service provider factory
date: 2015-10-27 16:23:53
tags: "Angularjs"
categories: "Angularjs"
---
自己对service provider factory的理解
<!-- more -->
看着自己前段时间写的代码，发现controller中写了很多的业务逻辑，还记得当时自己还到处google如何在controller中保存数据。。。
其实这并不符合angular的设计思想，出于内存性能的考虑，当我们切换页面的时候，angular会清空当前的controller，所以当我们需要使用一些持久的数据的时候，我们需要使用 **服务** 来保存。
angular中提供了service provider factory这三种方法来创建 **服务**。
当初自己查询如何创建自己的服务的时候，经常性的会搜出 关于这三种方法的区别 的文章。
但是只看区别，不了解其原理的人都是耍流氓。
### $provider
$provide是在Auto模块中的一个服务，他有很多的方法，你可以使用$provide中的某一个方法来创建一个provider（其实，provider，value，constant，service，factory他们都是provider！），然后通过provide来定义服务。
举个栗子：
``` javascript
angular.module('app', []).config(function($provide) {
  $provide.provider('example', function() {
    this.$get = function() {
      return function(x) {
        console.log(x);
      };
    };
  });
});
```
其实这就是最基本的写法，service provider factory的区别就是，factory service其实就是基于provider的封装，
下面介绍一下provider。
### provider
provider有且必须有一个$get方法，在$get方法中我们需先定义一个对象，给这个对象添加属性和方法，然后返回这个对象，$get方法返回的这个对象就是这个服务的一个实例。而且需要注意的是provider是唯一一种可以被 .config() 函数配置的服务。当你想要在 service 对象启用之前，先进行模块范围的配置，那就应该用 provider。

``` javascript
var app = angular.module('app', []);

app.provider('person', function () {
  var defaultName = 'jason';
  var name = defaultName;
  return {
    setName: function (parameter) {
      name = parameter;
    },
    $get: function () {
      return {
          greeting: name + '说什么';
      }
    }
  }
});

app.config(function (personProvider) { //这里有个坑，参数应该是 服务名+Provider 的写法
  //如果只写person，并不会运行，因为person只是叫做person的服务的一个实例，config中只能注入服务的provider
  personProvider.setName('jasonyang');
});

app.controller('personController', function (person) {
  console.log(person.greeting); // 命令行显示 jasonyang说什么
});
```
### factory
它是一个可注入的function，factory可以认为是设计模式中的工厂模式，就是你提供一个返回一个对象的实例的方法，
和provider的定义的一样的：需先定义一个对象，给这个对象添加属性和方法，然后返回这个对象，这里要注意和provider用$get方法返回服务的实例不一样，它返回的就是对象。
``` javascript
var app = angular.module('app', []);

app.config(function ($provide) {
  $provide.factory('person', function () {
    return {
      name: 'jason';
    }
  });
});

app.controller('myController', function (person) {
  console.log(person.name);  // jason
});
```
factory可以返回任何东西，它实际上是一个只有$get方法的provider。
### service
它是一个可注入的构造器（constructor），有点像类的概念，在AngularJS中它是单例的，用它在Controller中通信或者共享数据都很合适，它和factory的区别就是：factory是普通function，而service是一个构造器(constructor)，这样Angular在调用service时会用new关键字，而调用factory时只是调用普通的function，所以factory可以返回任何东西，而service可以不返回。
``` javascript
var app = angular.module('app' ,[]);

app.config(function ($provide) {
  $provide.service('person', function () {
    this.name = 'jason';
  });
});

app.controller('myController', function (person) {
  console.log(person.name);
});
```
### Constant
Constant用于定义常量，定义的值可以被注入到任意地方且不能被修改。
栗子：
``` javascript
var app = angular.module('app', []);

app.config(function ($provide) {
  $provide.constant('personName', 'jason');
});

app.controller('myController', function (personName) {
  console.log(personName);
});
```
### value
value可以用于定义string,number,function,它和constant的不同之处在于，它可以被修改，不能被注入到config中，但是它可以被decorator装饰。
``` javascript
var app = angular.module('app', []);

app.config(function ($provide) {
  $provide.value('personName', 'jason')
});

app.controller('myController', function (personName) {
  console.log(personName);
})
```
### decorator
上面提到了decorator可以用来装饰value，其实他可以用来装饰除Constant以外的其他所有的provider（他不能装饰Constant，因为实际上Constant不是通过provider()方法创建的。）
``` javascript
var app = angular.module('app', []);

app.value('personName', 'jason');

app.config(function ($provide) {
  $provide.decorator('personName', function ($delegate) {
    return $delegate + 'is a F2E';
  });
});

app.controller('myController', function (movieTitle) {
  console.log(personName);//jason is a F2E
});
```
### 总结
* provider是一个可配置的factory
* factory是一个可注入的方法（function）
* service是一个可注入的构造器（constructor）
* value就是一个简单的可注入的值
* decorator可以修改或封装其他的provider（除了constant），
