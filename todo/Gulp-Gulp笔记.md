Title: Gulp笔记
Date: 2015-07-01 8:42
Category: Web
Tags: Gulp
Author: 刘理想

[TOC]

##1. 准备工作

- 安装nodejs
- 安装http-server: `npm install -g http-server`
- 初始化项目：`npm init`
- 安装gulp: `npm install gulp --save-dev` 安装到依赖中

##2. 第一个Gulp Task

创建一个gulpfile.js，gulp会自动发现这个文件。
```
'use strict'; //使用严格的编译方法

var gulp = require('gulp');

//定义task，第一个参数是任务名
gulp.task("hello", function(){
  console.log("Hello");
})
```

运行任务：
```
$ gulp hello
[09:40:32] Using gulpfile G:\git\WebDesign\gulp_learn\gulp_basics\gulpfile.js
[09:40:32] Starting 'hello'...
Hello
[09:40:32] Finished 'hello' after 354 μs
```

定义默认的task：

```
//默认task，第二个参数是依赖任务，依赖任务会在当前任务之前执行
gulp.task("default", ["hello"], function(){
  console.log("This is the default task!");
});
```

运行任务，运行默认的任务不需要任务名：
```
$ gulp
[09:43:23] Using gulpfile G:\git\WebDesign\gulp_learn\gulp_basics\gulpfile.js
[09:43:23] Starting 'hello'...
Hello
[09:43:23] Finished 'hello' after 299 μs
[09:43:23] Starting 'default'...
This is the default task!
[09:43:23] Finished 'default' after 554 μs
```

##3. 使用Gulp管理Javascript 工作流

###3.1 使用Gulp第三方库
gulp社区提供了很多gulp的第三方库，来完成各种工作(比如编译sass, 压缩文件等)。

这里我们使用gulp-concat库，来分别压缩css和js文件到一个文件中。

**安装gulp-concat**
```
npm install gulp-concat --save-dev
```

假设我们页面里有很多js文件，当项目变得越来越大时，管理多个文件就很困难，所以我们可以使用gulp来将这些文件合并成一个文件。

```
var gulp = require('gulp'), 
  concat = require('gulp-concat');

//定义task，第一个参数是任务名
gulp.task("concatScripts", function(){
  //gulp.src可以接受数组和单独的字符串，注意js文件的顺序
  //pipe类似unix中管道的概念，前面的输出作为后面的输入，这里gulp.src的输出作为concat()的输入
  //concat()接受的字符串参数是合并后的文件名
  //gulp.dest()指定文件夹
  gulp.src([
    'js/jquery.js',
    'js/sticky/jquery.sticky.js',
    'js/main.js'])
  .pipe(concat("app.js"))
  .pipe(gulp.dest('js'));
});
```

###3.2 压缩JavaScript文件

安装gulp-uglify

```
npm install gulp-uglify --save-dev
```

利用前面concatScripts已经创建好的app.js，进行压缩：
```
gulp.task("minifyScripts", function(){
  gulp.src("js/app.js")
    .pipe(uglify())
    .pipe(gulp.dest('js'));
});
```

上面有一个问题，就是原有的app.js被压缩了，看不到原有未压缩前的js了。那我们可以使用gulp-rename来将压缩后的文件重命名。

安装gulp-rename:
```
npm install gulp-rename --save-dev
```

```
gulp.task("minifyScripts", function(){
  gulp.src("js/app.js")
    .pipe(uglify())
    .pipe(rename('app.min.js'))
    .pipe(gulp.dest('js'));
});
```

##4. 使用Gulp编译Sass

###4.1 Gulp自动编译Sass

要让Gulp能够编译Sass，第一步就是安装gulp-sass

```
npm install gulp-sass --save-dev
```

然后创建任务：
```
gulp.task("compileSass", function(){
  gulp.src('scss/application.scss')
    .pipe(sass())
    .pipe(gulp.dest('css'));
});
```

###4.2 给Sass添加Source Map

Source Map用来显示Sass和CSS之间的映射，当Sass文件较多时特别有用。

安装gulp-sourcemaps

```
npm install gulp-sourcemaps
```

定义source map有点不同：
```
var maps = require('gulp-sourcemaps');
gulp.task("compileSass", function(){
  gulp.src('scss/application.scss')
    .pipe(maps.init()) //初始化maps
    .pipe(sass())
    .pipe(maps.write('./')) //输出source maps文件路径是相对于输出的css文件路径的，这里让source maps也在css文件夹内
    .pipe(gulp.dest('css'));
});
```

这个时候在谷歌浏览器的开发工具里查看class，就会显示对应的sass文件了。

###4.3 JavaScript的Source Map

gulp-sourcemaps不仅能映射sass和css，还能给js做映射，方法同Sass映射：

```
gulp.task("concatScripts", function(){
  //gulp.src可以接受数组和单独的字符串，注意js文件的顺序
  //pipe类似unix中管道的概念，前面的输出作为后面的输入，这里gulp.src的输出作为concat()的输入
  //concat()接受的字符串参数是合并后的文件名
  //gulp.dest()指定文件夹
  gulp.src([
    'js/jquery.js',
    'js/sticky/jquery.sticky.js',
    'js/main.js'])
  .pipe(maps.init())
  .pipe(concat("app.js"))
  .pipe(maps.write('./'))
  .pipe(gulp.dest('js'));
});
```

##5. 进一步提高Gulp任务管道

###5.1 多个任务放在一起

```
gulp.task("build", ["concatScripts", "minifyScripts", "compileSass"]);
```

但这样做会有一个问题，3个任务是一起执行的，而minifyScripts需要在concatScripts之前执行。要解决这个问题，我们对gulpfile.js进行改造：

```
'use strict'; 
var gulp = require('gulp'), 
  concat = require('gulp-concat'),
  uglify = require('gulp-uglify'),
  rename = require('gulp-rename'),
    sass = require('gulp-sass'),
    maps = require('gulp-sourcemaps');

gulp.task("concatScripts", function(){
  return gulp.src([
    'js/jquery.js',
    'js/sticky/jquery.sticky.js',
    'js/main.js'])
  .pipe(maps.init())
  .pipe(concat("app.js"))
  .pipe(maps.write('./'))
  .pipe(gulp.dest('js'));
});

gulp.task("minifyScripts", ["concatScripts"], function(){
  return gulp.src("js/app.js")
    .pipe(uglify())
    .pipe(rename('app.min.js'))
    .pipe(gulp.dest('js'));
});

gulp.task("compileSass", function(){
  return gulp.src('scss/application.scss')
    .pipe(maps.init())
    .pipe(sass())
    .pipe(maps.write('./'))
    .pipe(gulp.dest('css'));
});

gulp.task("build", ["minifyScripts", "compileSass"]);

gulp.task("default", ["build"]);
```

首先所有的任务都用`return`进行返回，这样gulp能够在运行需要依赖的任务时，知道依赖任务什么时候结束。

然后将concatScripts作为minifyScripts的依赖，并从build任务中删除concatScripts，从而实现concatScripts在minifyScripts前运行。

###5.2 使用watch方法自动运行任务

gulp的watch方法能够自动检测到文件的改变，并且自动执行任务。

```
gulp.task("watchSass", function(){
  gulp.watch(['scss/**/*.scss'], ['compileSass']);
});
```

运行：
```
gulp watchSass
```

###5.3 构建和开发的pipeline

如果需要部署，则将文件拷贝到dist，但是要提供`{base: './'}`，否则只拷贝文件不拷贝目录。

```
gulp.task("build", ["minifyScripts", "compileSass"], function(){
  return gulp.src(["css/application.css", "js/app.min.js",
                  "index.html", "img/**", "fonts/**"], {base: './'})
        .pipe(gulp.dest('dist'));
});
```

安装del，删除多余的文件
```
npm install del --save-dev
```

创建清除
```
gulp.task("clean", function(){
  del(['dist', 'css/application.css*', 'js/app*.js*']);
});
```

最后，整个流程是清除原先生成的文件，然后再运行build任务。
gulpfile.js中启动任务是`gulp.start('任务名')`

```
gulp.task("clean", function(){
  del(['dist', 'css/application.css*', 'js/app*.js*']);
});

gulp.task("build", ["minifyScripts", "compileSass"], function(){
  return gulp.src(["css/application.css", "js/app.min.js",
                  "index.html", "img/**", "fonts/**"], {base: './'})
        .pipe(gulp.dest('dist'));
});

gulp.task("default", ["clean"], function(){
  gulp.start('build');
});
```

###5.4 深入development pipeline

如果要监视Sass和JS的变化，利用前面创建的watchSass，改为:

```
gulp.task("watchFiles", function(){
  gulp.watch(['scss/**/*.scss'], ['compileSass']);
  gulp.watch('js/main.js', ['concatScripts']);
});
```

也可以把两个watch合并为一个：
```
gulp.watch(['scss/**/*.scss', 'js/main.js'], ['compileSass', 'concatScripts']);
```

这样，无论是sass还是js文件发生改变，都会引起compileSass和concatScripts任务重新执行，这并不是一个特别好的方法，因为js并不需要compileSass，同样，sass也并不需要concatScripts.


