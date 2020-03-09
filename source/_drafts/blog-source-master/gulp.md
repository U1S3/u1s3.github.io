---
title: Gulp
comments: false
date: 2018-05-25 11:03:41
keywords: gulp
description: Gulp的用法。
summary: Gulp的用法。
categories: 工具箱
tags: [gulp]
---

## 常用依赖

```
$ yarn add -D babel-core babel-preset-env gulp pump del run-sequence browser-sync gulp-cached gulp-concat gulp-rename gulp-stylus gulp-autoprefixer gulp-clean-css gulp-babel gulp-uglify gulp-rev-append gulp-sourcemaps 
```

## 配置文件

```javascript
'use strict';    
const gulp = require('gulp'),
      pump = require('pump'),
      del = require('del'),
      runSequence = require('run-sequence'),
      browserSync = require('browser-sync').create(),
      cached = require('gulp-cached'),
      concat = require('gulp-concat'),
      rename = require('gulp-rename'),
      stylus = require('gulp-stylus'),
      autoprefixer = require('gulp-autoprefixer'),
      cleanCss = require('gulp-clean-css'),
      babel = require('gulp-babel'),
      uglify = require('gulp-uglify'),
      rev = require('gulp-rev-append'),
      sourcemaps = require('gulp-sourcemaps');

// 清空输出目录
gulp.task('clean', () => {
    return del(['dist']);
});

// 编译stylus文件
gulp.task('stylus', (cb) => {
    pump([
        gulp.src('src/stylus/app.styl'),
        stylus(),
        autoprefixer(),
        cleanCss(),
        rename({suffix: '.min'}),
        gulp.dest('dist/css')
    ], cb());
});

// 编译csslib文件
gulp.task('csslib', (cb) => {
    pump([
        gulp.src([
            'src/css/normalize.css',
        ]),
        cached('csslib'),
        concat('lib.min.css'),
        cleanCss(),
        gulp.dest('dist/css')
    ],cb());
});

// 编译js文件
gulp.task('js', (cb) => {
    pump([
        gulp.src('src/js/app.js'),
        cached('js'),
        babel(),
        uglify(),
        rename({suffix: '.min'}),
        gulp.dest('dist/js')
    ],cb());
});

// 编译jslib文件
gulp.task('jslib', (cb) => {
    pump([
        gulp.src([
            'src/css/jquery.js',
        ]),
        cached('jslib'),
        concat('lib.min.js'),
        uglify(),
        gulp.dest('dist/js')
    ], cb());
});

// 复制图片文件
gulp.task('copy', (cb) => {
    // 复制图片
    pump([
        gulp.src('src/img/*.*'),
        gulp.dest('dist/img')
    ],cb());
});

// 编译html文件
gulp.task('html', (cb) => {
    pump([
        gulp.src('src/*.html'),
        cached('html'),
        rev(),
        gulp.dest('dist')
    ],cb());
});

// 浏览器同步
gulp.task('browserSync', (cb) => {
    browserSync.init(
        // 监控目录
        ['dist/**/*.*'],
        {
            // 服务器根目录
            server: {baseDir: 'dist'}
        }
    );
    cb();
});

// 监听文件变动
gulp.task('watch', ['browserSync'], (cb) => {
    gulp.watch(['src/css/*.css'], ['csslib']);
    gulp.watch(['src/css/*.js', '!app.js'], ['jslib']);
    gulp.watch('src/stylus/app.styl', ['stylus']);
    gulp.watch('src/js/app.js', ['js']);
    gulp.watch('src/img/*.*', ['copy']);
    gulp.watch('src/*.html', ['html']);
    cb();
});

// 开始任务流
gulp.task('default', (cb) => {
    runSequence(
        'clean',
        ['csslib', 'jslib', 'stylus', 'js', 'html', 'copy'],
        'watch',
    );
    cb();
});
```

## Links

- [精通gulp常用插件](https://zhuanlan.zhihu.com/p/25243171)

