title: css
date: 2016-03-29 18:37:49
tags: "css"
categories: "css"
---
记录一些css的知识
<!-- more -->
### 样式重置
开发中需要清除一些自带的样式
``` css
html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym, address, big, cite, code, del, dfn, em, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, var, b, u, i, center, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, embed, figure, figcaption, footer, header, hgroup, menu, nav, output, ruby, section, summary, time, mark, audio, video {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  outline: none;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
html { height: 101%; }
body { font-size: 62.5%; line-height: 1; font-family: Arial, Tahoma, sans-serif; }

article, aside, details, figcaption, figure, footer, header, hgroup, menu, nav, section { display: block; }
ol, ul { list-style: none; }

blockquote, q { quotes: none; }
blockquote:before, blockquote:after, q:before, q:after { content: ''; content: none; }
strong { font-weight: bold; } 

table { border-collapse: collapse; border-spacing: 0; }
img { border: 0; max-width: 100%; }
```

### 在可点击的项目上强制手型
``` css
a[href], input[type='submit'], input[type='image'], label[for], select, button, .pointer {
    cursor: pointer;
}
```

### 用CSS动画实现省略号动画
这个片段将帮助你制造一个ellipsis的动画，对于简单的加载状态是很有用的，而不用去使用gif图像。
``` css
.loading:after {
    overflow: hidden;
    display: inline-block;
    vertical-align: bottom;
    animation: ellipsis 2s infinite;
    content: "\2026"; /* ascii code for the ellipsis character */
}
@keyframes ellipsis {
    from {
        width: 2px;
    }
    to {
        width: 15px;
    }
}
```
[例子](http://jsfiddle.net/agusesetiyono/MDzsR/69/light/)

### Flexbox制作CSS布局
伸缩盒模型（flexbox）是一个新的盒子模型，主要优化了UI布局。
作为实际布局的第一个CSS模块（浮动真的应该主要用来制作文本围绕图片这样的效果），它使很多任务容易多。
Flexbox的功能主要包手：简单使用一个元素居中（包括水平垂直居中），可以让扩大和收缩元素来填充容器的可利用空间，可以改变源码顺序独立布局，以及还有其他的一些功能。
然而有一些事项需要注意。在IE中规范更改了他的语法，因此你将需要使用一个稍微不同的语法。
Chrome当前版本仍然需要添加前缀“-webkit-”，而Firefox和Safari仍然还在使用最老版本的语法。
Firefox已经更新为最新的规范，但是，在实际项目中目前最好还先别使用最新的规范，直到它被认为没有bug了或者更稳定了，在使用。
在这之前，Firefox还是使用最老的语法规范。
#### flex布局让水平和垂直居中变得容易
``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8"/>
  <title>Centering an Element on the Page</title>
</head>
<body>
  <h1>OMG, I’m centered</h1>
</body>
</html>	
```
``` css
html {
  height: 100%;
} 

body {
  display: -webkit-box;  /* 老版本语法: Safari,  iOS, Android browser, older WebKit browsers.  */
  display: -moz-box;    /* 老版本语法: Firefox (buggy) */ 
  display: -ms-flexbox;  /* 混合版本语法: IE 10 */
  display: -webkit-flex;  /* 新版本语法： Chrome 21+ */
  display: flex;       /* 新版本语法： Opera 12.1, Firefox 22+ */

  /*垂直居中*/	
  /*老版本语法*/
  -webkit-box-align: center; 
  -moz-box-align: center;
  /*混合版本语法*/
  -ms-flex-align: center; 
  /*新版本语法*/
  -webkit-align-items: center;
  align-items: center;

  /*水平居中*/
  /*老版本语法*/
  -webkit-box-pack: center; 
  -moz-box-pack: center; 
  /*混合版本语法*/
  -ms-flex-pack: center; 
  /*新版本语法*/
  -webkit-justify-content: center;
  justify-content: center;

  margin: 0;
  height: 100%;
  width: 100% /* needed for Firefox */
} 
/*实现文本垂直居中*/
h1 {
  display: -webkit-box; 
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
 
  -webkit-box-align: center; 
  -moz-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;

  height: 10rem;
}	
```
#### 规范版本
![](http://jasonyangblog.com/upload/flexbox-s4.jpg)
#### 开启flexbox：让一个元素变成伸缩容器
![](http://jasonyangblog.com/upload/flexbox-s5.jpg)
#### 主轴对齐方式：指定伸缩项目沿主轴对齐方式
![](http://jasonyangblog.com/upload/flexbox-s6.jpg)
#### 侧轴对齐方式：指定伸缩项目沿侧轴对齐方式
![](http://jasonyangblog.com/upload/flexbox-s7.jpg)
#### 单个伸缩项目侧轴对齐方式
![](http://jasonyangblog.com/upload/flexbox-s8.jpg)
#### 伸缩项目行对齐方式：指定伸缩项目行在侧轴的对齐方式
![](http://jasonyangblog.com/upload/flexbox-s9.jpg)
#### 显示顺序：指定伸缩项目的顺序
![](http://jasonyangblog.com/upload/flexbox-s10.jpg)
#### 伸缩性：指定伸缩项目如何伸缩尺寸   
![](http://jasonyangblog.com/upload/flexbox-s11.jpg)
#### 伸缩流：指定伸缩容器主轴的伸缩流方向
![](http://jasonyangblog.com/upload/flexbox-s12.jpg)
#### 换行：指定伸缩项目是否沿着侧轴排列
![](http://jasonyangblog.com/upload/flexbox-s13.jpg)

### CSS3：全屏背景
``` css
html { 
    background: url('images/bg.jpg') no-repeat center center fixed; 
    -webkit-background-size: cover;
    -moz-background-size: cover;
    -o-background-size: cover;
    background-size: cover;
}
```

### 禁用移动Webkit的选择高亮
``` css
body {
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
```