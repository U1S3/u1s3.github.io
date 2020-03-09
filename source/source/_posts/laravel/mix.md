---
title: Laravel Mix 编译前端资源
date: 2019-11-1 15:33:53
categories: Laravel
---



## 运行 Mix

### 普通编译

```bash
$ npm run dev
```

### 压缩编译

```bash
$ npm run production
```

### 监听资源变化

```bash
$ npm run watch-poll
```

<!-- more -->

## 合并原生 css

```php
mix.styles([
    'public/css/vendor/normalize.css',
    'public/css/vendor/videojs.css'
], 'public/css/all.css');
```

## css 预处理

### Stylus

普通编译

```php
mix.stylus('resources/stylus/app.styl', 'public/css');
```

多次编译

```php
mix.stylus('resources/stylus/app.styl', 'public/css')
   .stylus('resources/stylus/admin.styl', 'public/css');
```

自定义输出文件名

```php
mix.stylus('resources/stylus/app.styl', 'public/stylesheets/styles.css');
```

以安装额外的 Stylus 插件， 需要先通过 npm 或 yarn 安装插件 

```php
mix.stylus('resources/stylus/app.styl', 'public/css', {
    use: [
        require('rupture')()
    ]
});
```

### Less

普通编译

```php
mix.less('resources/less/app.less', 'public/css');
```

 覆盖隐含的 Less 插件选项

```php
mix.less('resources/less/app.less', 'public/css', {
    strictMath: true
});
```

### Sass

普通编译

```php
mix.sass('resources/sass/app.scss', 'public/css');
```

添加额外的 Node-Sass 插件选项

```php
mix.sass('resources/sass/app.sass', 'public/css', {
    precision: 5
});
```

## PostCSS

Mix 利用流行的 Autoprefixer 插件自动附加所有必要的 CSS3 第三方前缀。也可以自由添加所需的任何附加插件。安装插件后直接调用即可。 

```php
mix.sass('resources/sass/app.scss', 'public/css')
   .options({
        postCss: [
            require('postcss-css-variables')()
        ]
   });
```

##  sourceMaps 

```php
mix.js('resources/js/app.js', 'public/js').sourceMaps();
```

## 禁用 URL 处理

Mix 是基于 Webpack 构建的， 对于 CSS 编译，Webpack 将重写和优化任何带有样式表的 `url()` 调用。 

```scss
// Webpack 将找到 example.png, 将其复制到 public/images 文件夹，然后重写生成的样式表中的 url()
// scss
background: url('../images/example.png');
// css
background: url(/images/example.png?d41d8cd98f00b204e9800998ecf8427e);
```

> 注意：任何给定 `url()` 的绝对路径将被排除在 URL 重写之外。例如 `url('/images/thing.png')` 或 `url('http://example.com/images/thing.png')` 将不会被修改。 

但是在某些情况下，对已有文件夹结构可能已经按预期做了配置，此时可以禁用此功能：

```php
mix.sass('resources/app/app.scss', 'public/css')
   .options({
      processCssUrls: false
   });
```

此时，Mix 将不再匹配任何 `url()` 或者复制资源到 public 目录，编译后的 CSS 看上去和你原来输入的内容一样 。

