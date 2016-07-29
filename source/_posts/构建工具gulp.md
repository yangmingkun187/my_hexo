title: 构建工具gulp
date: 2015-7-22 10:50:01
tags: "gulp"
categories: "构建工具"
---
gulp初探
<!-- more -->
### gulp总结
以前也是浅尝辄止的用过grunt，但是尝的不是怎么明白，因为grunt配置的东西比较多，会引用大量你实际上并不需要的对象属性，Grunt比Gulp更加频繁地操作文件系统，使用数据流的Gulp总是比Grunt快。对于一个小的LESS文件，gulpfile.js通常需要6ms，而gruntfile.js则需要大概50ms——慢8倍多。

### 安装gulp

先全局装一遍
```
npm install gulp -g
```
然后进项目里面再装一遍
```
npm install gulp --save-dev
```

### 安装插件

gulp有很多的插件，具体看这里 [gulp插件](http://gulpjs.com/plugins/)

下面的例子中，我们要用到这些插件：

* less的编译（gulp-less）
* 自动添加css前缀（gulp-autoprefixer）
* 压缩css（gulp-minify-css）
* js代码校验（gulp-jshint）
* 合并js文件（gulp-concat）
* 压缩js代码（gulp-uglify）
* 压缩图片（gulp-imagemin）
* 自动刷新页面（gulp-livereload）
* 图片缓存，只有图片替换了才压缩（gulp-cache）
* 更改提醒（gulp-notify）
* 清除文件（del）

安装的话，使用下面的命令：
```
npm install gulp-less gulp-autoprefixer gulp-minify-css gulp-jshint gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-cache del --save-dev
```

### 创建gulpfile.js文件
需要注意的是名字不能更改，需在项目的根目录下，

### 在gulpfile.js 内加载插件
``` javascript
var gulp = require('gulp'),
    less = require('gulp-less'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    imagemin = require('gulp-imagemin'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    notify = require('gulp-notify'),
    cache = require('gulp-cache'),
    livereload = require('gulp-livereload'),
    del = require('del');
```
### 建立任务
#### 我们先创建一个编译less，自动加css前缀，压缩css的任务
``` javascript
gulp.task('styles', function() {
  return gulp.src('app/css/style.less')
    .pipe(less())
    .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
    .pipe(gulp.dest('dist/css'))
    .pipe(rename({suffix: '.min'}))
    .pipe(minifycss())
    .pipe(gulp.dest('dist/css'))
    .pipe(notify({ message: '任务完成！' }));
});
```
然后在命令行执行
```
gulp styles
```
执行成功后，你会发现项目里面多了一个dist文件夹，下面就有由less编译的.css文件，和压缩后的.min.css

ok，我们再来看看上面的任务代码
#### gulp.task
```
gulp.task('styles', function () {...});
```
gulp.task这个API用来创建任务，在命令行下可以输入gulp styles来执行上面的任务。

#### gulp.src
gulp.src()方法输入一个glob(比如匹配一个或多个文件的字符串)或者glob数组，然后返回一个可以传递给插件的数据流。

Gulp使用 node-glob 来从你指定的glob里面获取文件，这里列举下面的例子来阐述，方便大家理解：

* js/app.js 精确匹配文件
* js/*.js 仅匹配js目录下的所有后缀为.js的文件
* js/**/*.js 匹配js目录及其子目录下所有后缀为.js的文件
* !js/app.js 从匹配结果中排除js/app.js，这种方法在你想要匹配除了特殊文件之外的所有文件时非常管用
* *.+(js|css) 匹配根目录下所有后缀为.js或者.css的文件
* 此外，Gulp也有很多其他的特征，但并不常用。如果你想了解更多的特征，请查看 Minimatch 文档。*

js目录下包含了压缩和未压缩的JavaScript文件，现在我们想要创建一个任务来压缩还没有被压缩的文件，我们需要先匹配目录下所有的JavaScript文件，然后排除后缀为.min.js的文件:
```
gulp.src(['js/**/*.js', '!js/**/*.min.js'])
```
#### gulp.pipe(less())

我们使用.pipe()这个API将需要处理的文件导向less插件，那些插件的用法可以在github上找到。

#### .pipe(gulp.dest('dist/css'))
gulp.dest()API设置生成文件的路径，一个任务可以有多个生成路径，一个可以输出未压缩的版本，另一个可以输出压缩后的版本。

#### 具体的可以了解下gulp的API

### 继续建立任务

#### js代码校验、合并和压缩

跟上面styles的代码一样：

``` javascript
gulp.task('scripts', function() {
  return gulp.src('app/js/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter('default'))
    .pipe(concat('main.js'))
    .pipe(gulp.dest('dist/js'))
    .pipe(rename({suffix: '.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('dist/js'))
    .pipe(notify({ message: 'js任务完成！' }));
});
```
上面需要注意的是，程序中JSHint插件执行了2次，这是因为第一次执行JSHint只是给文件对象附加了jshint属性，并没有输出。你可以自己读取jshint的属性或者传递给默认的JSHint的接收函数或者其他的接收函数,比如 jshint-stylish。还有我们将jshint（js代码校验）的reporter设置成了默认，当然你也可以自定义检验规则，具体[戳这里](http://jshint.com/docs/reporters/)，当校验不通过时，会给出相应位置的提示，方便修改自己的代码。
执行
```
gulp scripts
```
好的，我们的js校验，合并，压缩也完成了
#### 压缩图片
``` javascript
gulp.task('images', function() {
  return gulp.src('app/img/*')
    .pipe(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true }))
    .pipe(gulp.dest('dist/img'))
    .pipe(notify({ message: '压缩图片完成！' }));
});
```
这个任务使用imagemin插件把所有在app/img/目录以及其子目录下的所有图片（文件）进行压缩，我们可以进一步优化，利用缓存保存已经压缩过的图片，使用之前装过的gulp-cache插件，不过要修改一下上面的代码：
``` javascript
.pipe(cache(imagemin({ optimizationLevel: 5, progressive: true, interlaced: true })))
```
现在，只有新建或者修改过的图片才会被压缩了。
执行完<code>gulp images</code>后看看img文件的大小，是不是小了很多。

#### 清除文件

在每次执行任务前，最好清除之前生成的文件：
``` javascript
gulp.task('clean', function(callback) {
    del(['dist/css', 'dist/js', 'dist/img'], callback)
});
```
在这里没有必要使用Gulp插件了，可以使用NPM提供的插件。我们用一个回调函数确保在退出前完成任务。

### 设置默认任务

我们在命令行下输入gulp执行的就是默认任务，现在我们为默认任务指定执行上面写好的三个任务：
``` javascript
gulp.task('default', ['clean'], function() {
    gulp.start('styles', 'scripts', 'images');
});
```
在这个例子里面，clean任务执行完成了才会去运行其他的任务，在gulp.start()里的任务执行的顺序是不确定的，所以将要在它们之前执行的任务写在数组里面。
### 监听任务
监听文件的变化并执行task是由<code>gulp watch</code>实现的。
使用gulp.watch()方法可以监听文件，它接受一个glob或者glob数组（和gulp.src()一样）以及一个任务数组来执行回调。
``` javascript
gulp.task('watch', function() {
  // Watch .scss files
  gulp.watch('app/css/*.less', ['styles']);
  // Watch .js files
  gulp.watch('app/js/*.js', ['scripts']);
  // Watch image files
  gulp.watch('app/img/*', ['images']);
});
```
我们将不同类型的文件分开处理，执行对应的数组里的任务。现在我们可以在命令行输入<code>gulp watch</code>执行监听任务，当.less .js和图片修改时将执行对应的任务。

Gulp.watch()的另一个非常好的特性是返回我们熟知的watcher。利用watcher来监听额外的事件或者向watch中添加文件。例如，在执行一系列任务和调用一个函数时，你就可以在返回的watcher中添加监听change事件:
除了change事件，还可以监听很多其他的事件:
* end 在watcher结束时触发（这意味着，在文件改变的时候，任务或者回调不会执行）
* error 在出现error时触发
* ready 在文件被找到并正被监听时触发
* nomatch 在glob没有匹配到任何文件时触发

Watcher对象也包含了一些可以调用的方法：

* watcher.end() 停止watcher（以便停止执行后面的任务或者回调函数）
* watcher.files() 返回watcher监听的文件列表
* watcher.add(glob) 将与指定glob相匹配的文件添加到watcher（也接受可选的回调当第二个参数）
* watcher.remove(filepath) 从watcher中移除个别文件

### 自动刷新页面

当一个文件被修改或者Gulp任务被执行时可以用Gulp来加载或者更新网页。LiveReload和BrowserSync插件就可以用来实现在游览器中加载更新的内容。

#### LiveReload
LiveReload结合了浏览器扩展（包括 Chrome extension ），在发现文件被修改时会实时更新网页。它可以和 gulp-watch 插件或者前面描述的gulp-watch()函数一起使用。下面有一个 gulp-livereload 仓库中的README文件提到的例子:
``` javascript
var gulp = require('gulp'),
   less = require('gulp-less'),
   livereload = require('gulp-livereload'),
   watch = require('gulp-watch');

gulp.task('less', function() {
  gulp.src('less/*.less')
    .pipe(watch())
    .pipe(less())
    .pipe(gulp.dest('css'))
    .pipe(livereload());
});
```
这会监听到所有与less/*.less相匹配的文件的变化。一旦监测到变化，就会生成css并保存，然后重新加载网页.*
#### BroserSync
BroserSync 在浏览器中展示变化的功能与LiveReload非常相似，但是它有更多的功能。
当你改变代码的时候，BrowserSync会重新加载页面，或者如果是css文件，会直接添加进css中，页面并不需要再次刷新。这项功能在网站是禁止刷新的时候是很有用的。假设你正在开发单页应用的第4页，刷新页面就会导致你回到开始页。使用LiveReload的话，你就需要在每次改变代码之后还需要点击四次，而当你修改CSS时，插入一些变化时，BrowserSync会直接将需要修改的地方添加进CSS，就不用再点击回退。
BrowserSync提供了一种在多个浏览器里测试网页的很好方式:
![](http://p7.qhimg.com/t01b21adb0b23364a0c.gif)
BrowserSync也可以在不同浏览器之间同步点击翻页、表单操作、滚动位置。你可以在电脑和iPhone上打开不同的浏览器然后进行操作。所有设备上的链接将会随之变化，当你向下滚动页面时，所有设备上页面都会向下滚动（通常还很流畅！）。当你在表单中输入文本时，每个窗口都会有输入。当你不想要这种行为时，也可以把这个功能关闭。
BrowserSync不需要使用浏览器插件，因为它本身就可以给你提供文件:
![](http://p4.qhimg.com/t011034e5a90ce13015.gif)

实际上BrowserSync对于Gulp并不算一种插件，因为BrowserSync并不像一个插件一样操作文件。然而， npm上的BrowserSync模块 能在Gulp上被直接调用。
如何使用：
```
npm install browser-sync --save-dev
```
然后gulpfile.js会启动BrowserSync并监听文件：
``` javascript
var gulp = require('gulp'),
   browserSync = require('browser-sync');

gulp.task('browser-sync', function () {
  var files = [
    'app/**/*.html',
    'app/css/*.css',
    'app/img/*',
    'app/js/*.js'
  ];

  browserSync.init(files, {
    server: {
      baseDir: './app'
    }
  });
});
```
执行gulp browser-sync后会监听匹配文件的变化，同时为app目录提供文件服务,具体使用请看 [官方文档](https://github.com/BrowserSync/gulp-browser-sync)

### 简单的使用了一下gulp，也查阅了相关的blog，资料，官方文档，如有问题，欢迎指教
