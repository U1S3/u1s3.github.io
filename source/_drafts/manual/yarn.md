---
title: Yarn
date: 2018-01-05 11:55:08
categories: 说明书
tags: Yarn
---

## 国内镜像

```bash
$ yarn config set registry https://registry.npm.taobao.org
```

## 版本更新

### node

```bash
$ sudo npm cache clean -f
$ sudo npm install n -g
$ sudo n stable
```

### npm

```bash
$ sudo npm -g install npm
```

## 创建配置文件

```bash
$ yarn init
```

## 依赖管理

### 依赖安装

自动安装`package.json`文件中所有依赖

```bash
$ yarn # 两种用法相同
$ yarn install --no-bin-links # --no-bin-links 避免某些错误
```

### 新增依赖

#### 当前应用

```bash
$ yarn add [package] --dev # 最新版本 --dev --peer --optional 环境类别
$ yarn add [package]@[version] # 指定版本
$ yarn add [package]@[tag] # 指定标签
```

#### 全局安装

```bash
$ yarn global add [package]
```

### 升级依赖

```bash
$ yarn upgrade [package]
```

### 移除依赖

```bash
$ yarn remove [package]
```

### 清除缓存

```bash
yarn cache clean [package]
```

### 重建依赖

#### 指定依赖

```bash
$ yarn install --force [package]
```

#### 应用重建

```bash
$ yarn install --force
```

## 常见问题

### node-sass

node-sass: Command failed.

```bash
$ SASS_BINARY_SITE=http://npm.taobao.org/mirrors/node-sass yarn
# 或
$ yarn config set sass-binary-site https://npm.taobao.org/mirrors/node-sass
# 或
$ npm config set sass-binary-site https://npm.taobao.org/mirrors/node-sass
```

[关于安装 node-Sass 报错的解决记录](https://laravel-china.org/articles/5083/about-the-installation-of-node-sass-error-resolution-records)

### pngquant-bin

pngquant-bin: Command failed.

```bash
$ wget -q -O /tmp/libpng12.deb http://mirrors.kernel.org/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb   && sudo dpkg -i /tmp/libpng12.deb   && rm /tmp/libpng12.deb
$ yarn install --no-bin-links
```

Error: pngquant failed to build, make sure that libpng-dev is installed

```bash
$ sudo apt-get install libpng-dev
# $ sudo apt-get install -y build-essential libpng-dev
```

### cross-env

cross-env: not found

删除 `package.json` 中 `scripts` 中的 `cross-env`

## Links

[在评论区查看问题交流解决](https://learnku.com/courses/laravel-essential-training/5.8/style-beautification/4067)

