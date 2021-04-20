title: 引入Gulp压缩整站资源进一步提高写作效率
entitle: 'use-gulp-to-speed-hexo'
author: 托码思
avatar: /images/favicon.png
authorLink: 'https://www.tangkunyin.com'
authorAbout: 'https://about.tangkunyin.com'
authorDesc: 一个写代码的「伪文人」
categories: 技术
timestamp: 1547999542
date: 2019-01-20 23:52:22
tags: 
	- hexo
	- web
keywords: jsimple,hexo主题,gulp加速hexo
description: 话说，上一篇的主题更新日志拖了半天还没有写完，这又开始写了，真是脸大了。主要是因为引入了gulp操作，把整站资源压缩了，试了一把效果杠杠滴。以至于我没忍住，凌晨还要搞个大新闻！
photos:
---

### 这篇文章基于`JSimple`最新版

上一篇的更新日志：[The-Update-for-JSimple-in-Early2019](https://shuoit.net/tech/the-update-for-jsimple-in-early2019-1547728233.html)

其实关于本文主题，Google一下文章到处都是，然而我找了几篇都是`gulp3`，对于我这种忍不了旧习的人来说当然不行，于是，我一把梭的弄了`gulp4`

#### 依赖情况

```json
"scripts": {
    "prepublish": "hexo clean && hexo g && gulp",
    "publish": "hexo d && hexo b"
  },
  "dependencies": {
    "@babel/core": "^7.2.2",
    "@babel/preset-env": "^7.2.3",
    "gulp": "^4.0.0",
    "gulp-babel": "^8.0.0",
    "gulp-htmlclean": "^2.7.22",
    "gulp-htmlmin": "^5.0.1",
    "gulp-imagemin": "^5.0.3",
    "gulp-minify-css": "^1.2.4",
    "gulp-uglify": "^3.0.1",
    "hexo": "^3.7.0",
    "hexo-deployer-git": "^0.3.1",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-baidu-sitemap": "^0.1.6",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-search": "^2.4.0",
    "hexo-generator-sitemap": "^1.2.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-git-backup": "^0.1.2",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-marked": "^0.3.2",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-server": "^0.3.2"
  }
```

新增的`scripts`操作，是在写完文章时便于梭哈。之于区分`prepublish`和`publish`，是想提醒各位，上线之前先检查下 ：）

#### Gulp任务

站点根目录新建：`gulpfile.js`

```javascript
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
var imagemin = require('gulp-imagemin');

// 引入babel，万一用了ES6呢
var babel = require('gulp-babel');

gulp.task('minify-html', function() {
    return gulp.src('./public/**/*.html')
        .pipe(htmlclean())
        .pipe(htmlmin({
            removeComments: true,
            collapseWhitespace: true,
            collapseBooleanAttributes: true,
            removeEmptyAttributes: true,
            removeScriptTypeAttributes: true,
            removeStyleLinkTypeAttributes: true,
            minifyJS: true,
            minifyCSS: true
        }))
        .on('error', function(err) {
            console.log('html Error!', err.message);
            this.end();
        })
        .pipe(gulp.dest('./public'))
});
gulp.task('minify-css', function() {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss())
        .pipe(gulp.dest('./public'));
});
gulp.task('minify-js', function() {
    return gulp.src(['./public/js/**/*.js', '!./public/js/**/*.{min,mini}.js'])
        .pipe(babel())
        .pipe(uglify())
        .pipe(gulp.dest('./public/js'));
});
gulp.task('minify-images', function() {
    return gulp.src('./public/img/**/*.*')
        .pipe(imagemin(
        [imagemin.gifsicle({'optimizationLevel': 3}),
        imagemin.jpegtran({'progressive': true}),
        imagemin.optipng({'optimizationLevel': 8}),
        imagemin.svgo()],
        {'verbose': true}))
        .pipe(gulp.dest('./public/img'))
});
gulp.task('default', gulp.parallel('minify-html','minify-css','minify-js','minify-images', function(done){
    done();
}));
```
 
 
####  Babel大法，毕竟你不能保证在激动时不用ES6（主要是这货uglify对此不友好）
 
 站点根目录新建：`.babelrc`
 
 ```json
 {
    "presets": ["@babel/env"]
 }
 ```
 
 
#### 验证一下

![](/img/2019/15480009042280.jpg)

如果没有报错，就很没问题了！目前为止，暂时没发现有不对的地方。嗯，跪求打脸...

👀️提醒我该休息了，大新闻到此为止。明天中文多打一份荤菜奖励自己......晚安 ~ 😴️

