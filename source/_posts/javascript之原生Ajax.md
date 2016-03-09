title: javascript之原生Ajax
date: 2015-6-11 10:26:18
tags: "Javascript"
categories: "Javascript"
---
### javascript原生ajax
以前写ajax都是用的jQuery，zepto封装过的，写起来很简单，也不用考虑浏览器兼容，最近看了一下ajax原生的写法，觉得木欲长固其根，这些东西还是需要了解。

javascript提供了XMLHttpRequest这个对象，这也是实现ajax最基础的一个对象。
<!-- more -->
首先我们要创建一个XMLHttpRequest对象，考虑兼容性，具体写法如下：
``` javascript
function createReq() {
  var req = null;
  if(window.XMLHttpRequest) {  //FF，MZ，Opera，Safari，IE7，IE8等适用
    req = new XMLHttpRequest();   
    if (req.overrideMimeType) { //由于某些特定版本的mozilla浏览器的存在一些BUG，所以用overrideMimeType方法进行修正    
       req.overrideMimeType("text/xml");     
    }  
  } else if (window.ActiveXObject){ //IE6及以下浏览器使用ActiveXObject对象创建。
    var activexName = [ "MSXML2.XMLHTTP", "Microsoft.XMLHTTP" ]; //用于创建XMLHTTPRequest对象的控件
    for ( var i = 0; i < activexName.length; i++) {     
        try {     
            //取出一个控件名进行创建，如果创建成功就终止循环     
            //如果创建失败，回抛出异常，然后可以继续循环，继续尝试创建     
            xmlHttpRequest = new ActiveXObject(activexName[i]);   
            if(xmlHttpRequest){  
                break;  
            }  
        } catch (e) {}     
     }     
  }
}
```
现在我们的XMLHttpRequest对象创建好了，可以发送请求了。
#### get请求
``` javascript
function ajaxGet() {
  var req = createReq();
  if(req) {
    req.open('GET','/',true);
    req.onreadystatechange = function(){  
            if(req.readyState == 4){  
                if(req.status == 200){  
                    alert("req success");  
                }else{  
                    alert("req error");  
                }  
            }  
        }  
    req.send();  
  }
}
```
解释下上面的代码，首先调用上面的函数创建一个叫做req的XMLHttpRequest对象，如果创建成功，执行
``` javascript
req.open('GET','/',true);
```
使用open函数创建了一个请求，其中包含了三个参数
* 第一个参数为发送请求的方式，这里是get请求
* 第二个参数是请求的路径，可以使相对路径，也可以使绝对路径
* 第三个是请求是否是异步，默认是true，当状态改变时会调用onreadystatechange属性指定的回调函数，false则发送同步请求，但由于不好的用户体验，已经没有什么卵用了
``` javascript
req.onreadystatechange = function(){  
        if(req.readyState == 4){  
            if(req.status == 200){  
                alert("req success");  
            }else{  
                alert("req error");  
            }  
        }  
    }  
```
请求发出后，一旦服务器返回了，将执行监听指定的函数(onreadystatechange);
readyState属性表示返回XMLHTTP请求的当前状态，
各状态值意义如下：
* 0 (未初始化)	对象已建立，但是尚未初始化（尚未调用open方法）
* 1 (初始化)	对象已建立，尚未调用send方法
* 2 (发送数据)	send方法已调用，但是当前的状态及http头未知
* 3 (数据传送中)	已接收部分数据，因为响应及http头不全，这时通过responseBody和responseText获取部分数据会出现错误，
* 4 (完成)	数据接收完毕,此时可以通过通过responseBody和responseText获取完整的回应数据
每一个状态的改变都会触发onreadystatechange，需要进行判断，当状态码为4时，说明数据接收完毕，可以开始进行数据处理。
status属性实际是一种辅状态判断，只是status更多是服务器方的状态判断。关于status，由于它的状态有几十种，我只列出平时常用的几种：
* 100——客户必须继续发出请求
* 101——客户要求服务器根据请求转换HTTP协议版本
* 200——成功
* 201——提示知道新文件的URL
* 300——请求的资源可在多处得到
* 301——删除请求数据
* 404——没有发现文件、查询或URl
* 500——服务器产生内部错误
最后一步就是发送请求了，通过send方法实现。

#### post请求
``` javascript
function post(){  
    var req = createReq();  
    if(req){  
        req.open("POST", "/", true);  
        req.setRequestHeader("Content-Type","application/x-www-form-urlencoded; charset=gbk;");     
        req.send('{name:'杨明昆'}');  
        req.onreadystatechange = function(){  
            if(req.readyState == 4){  
                if(req.status == 200){  
                    alert("success");  
                }else{  
                    alert("error");  
                }  
            }  
        }  
    }  
}  
```
post请求与get不同的是，post请求需要设置请求头，设置请求头的方法是setRequestHeader，传递两个参数，第一个是请求头字段名，第二个参数是该字段的值
对于GET请求，send方法不需要提供参数，如果是POST请求，则将提交的数据作为参数发送，通常是JSON格式。

### 总结
一个ajax的基本步奏也就四步：
1. 创建XMLHTTPRequest对象 注意浏览器差异
2. 定义状态变化时的处理函数 onreadystatechange
3. 创建请求 open方法 post请求需要setRequestHeader设置请求头
4. 发送请求 send方法
