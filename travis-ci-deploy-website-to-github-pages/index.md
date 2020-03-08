# 使用 Travis CI 自动部署站点到 Github Pages


以前使用 Hexo 在 Github Pages 上搭建博客的时候，一直使用 Hexo 自带的 `hexo d` 命令，但是切换到 Hugo 后，没发现类似的命令，那部署就有点麻烦了，要先在本地生成然后在手动提交到仓库。

幸好在刷知乎的时候发现了 Travis CI 这个东西，可以实现将源码提交到仓库后自动部署，感觉发现了新大陆。

<!--more-->

## 准备开始

目的是在一个新的 Github 仓库中，创建 `master` 和 `code` 两个分支，`master` 是 Github Pages 指定的分支，`code` 用于存放源码，之后仅需要提交 `code` 分支，Travis CI 会处理其他工作。

[Hugo 的安装](https://gohugo.io/getting-started/installing/) 和 [站点的创建](https://gohugo.io/getting-started/quick-start/)在官网文档可以找到各种系统的安装方法，不多赘述。


## 远程仓库

在 Github 上创建名为 `username.github.io` 的新仓库，其中 `username` 是当前登录的 Github 账户的用户名。

## 本地仓库

### code 分支

```bash
$ git init
$ git checkout -b code
# public 文件夹是要推送到 master 分支的站点文件
# 但是因为后面会 Travis CI 实现自动部署，所以这个文件夹可以忽略不上传
$ echo '/public/' >> .gitignore
$ git add .
$ git commit -m "init"
$ git remote add origin git@github.com:username/username.github.io.git
$ git push -u origin code
```

### master 分支

```bash
# 生成一次网站文件，用于初始化 master 分支
$ hugo

# 创建临时文件夹 用于存储网站文件
$ HUGO_TEMP_DIR=$(mktemp -d)
# 复制网站文件到临时文件夹
$ cp -R public/* "$HUGO_TEMP_DIR"

# 创建空的 master 分支
$ git checkout --orphan master
# 删除 当前分支的索引文件
$ rm .git/index
# 清空当前分支
$ git clean -fdx
# 复制临时文件夹里的网站文件到当前目录
$ cp -R "$HUGO_TEMP_DIR"/. .

# 提交 master 分支到仓库
$ git add .
$ git commit -m 'init'
$ git push -u origin master

# 删除临时文件夹
$ [[ -d "$HUGO_TEMP_DIR" ]] && rm -rf "$HUGO_TEMP_DIR"
```

`master` 分支操作中的关于临时文件夹的步骤来自于 [利用Travis CI和Hugo將Blog自動部署到Github Pages](https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/)，但是我感觉临时文件夹这一步有点多余，生成网站文件后直接通过 `$ git checkout --orphan master ` 创建并切换到 `master` 分支，把 *public* 内的网站文件复制到根目录后删除无需提交的目录和文件，效果也是一样的。

## Travis CI 配置

### 生成 Token

依次点击 <kbd>Settings</kbd>/<kbd>Developer settings</kbd>/<kbd>Personal access tokens</kbd>/<kbd>Generate new token</kbd>，勾选 `repo`，保存后生成的 40 位密钥保存好。

### 配置环境变量

使用 Github 账户登录 [Travis CI](https://travis-ci.com/) ，并授权给予仓库权限，找到 `username.github.io`，依次点击 <kbd>More options</kbd> / <kbd>Settings</kbd>，然后找到 `Environment Variables` 创建如下变量：

| NAME         | VALUE                 |
| ------------ | --------------------- |
| CNAME_URL    | 独立域名，如果有的话  |
| GITHUB_NAME  | git config user.name  |
| GITHUB_EMAIL | git config user.email |
| GITHUB_TOKEN | Github 生成的40位密钥 |

### 开启监听

确保`Build pushed branches`，`Build pushed pull requests`，`Auto cancel branch builds`， `Auto cancel pull request builds` 处于开启状态。

## 部署逻辑

### .travis.yml

```yml
install:
  - wget https://github.com/gohugoio/hugo/releases/download/v0.66.0/hugo_0.66.0_Linux-64bit.deb
  - sudo dpkg -i hugo*.deb
  - rm -rf public 2> /dev/null
  
script:
  - HUGO_ENV=production hugo --gc --minify 2> /dev/null
  
deploy:
  provider: pages
  skip_cleanup: true
  github_token: "$GITHUB_TOKEN"
  email: "$GITHUB_EMAIL"
  name: "$GITHUB_NAME"
  verbose: true
  keep_history: true
  local_dir: public
  target_branch: master
  on:
    branch: code
```

## 完成

到这里就结束了，往后只需要专心在 `code` 分支写文章，完成后提交到仓库，Travis CI 会自动进行生成网站文件并提交到 `master` 分支的操作。

### 后续

[使用 Travis CI 自动部署应用到服务器](/travis-ci-deploy-application-to-server/)

## 参考文档

[Hugo](https://gohugo.io/)

[利用Travis CI和Hugo將Blog自動部署到Github Pages](https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/)
