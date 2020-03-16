# 使用 Travis CI 自动部署应用到服务器


之前基于 Hugo 在 Github Pages 上搭建了博客（ [使用 Travis CI 自动部署站点到 Github Pages](/posts/travis-ci-deploy-website-to-github-pages/)），又因为之前折腾 Wordpress 的时候买了个 VPS，本着钱不能白花的原则，就打算国内访问走 VPS，国外走 Github Pages。

<!--more-->

## 准备开始

目的是在提交 Github Pages 的 `code` 分支后，通过 Travis CI 远程登录服务器，执行脚本拉取 Github Pages 的 `master` 分支。但在 Travis CI 中不像交互式终端那样可以输入帐号密码，所以需要使用 SSH免密登录。

## 导入公钥

要实现 Travis CI 免密登录服务器，需要先将生成的密钥对中的公钥保存到服务器的 `~/.ssh/authrized_keys` 中。

```bash
# 这里使用 git 生成的密钥对就可以
$ ssh-copy-id -i ~/.ssh/id_rsa.pub server_user@server_ip
```

## 导入私钥

将私钥导入到 Travis CI 中使用的工具 Travis 需要通过 Ruby 的包管理工具 gem 安装。我当前使用的系统环境是 Manjaro，本身自带 Ruby，但是试来试去总是报错，就卸载并使用 RVM 重装了 Ruby 和 Gem。

### 卸载 Ruby 和 gem

```bash
# 删除当前安装的所有 gem 包
# 这一步可能会报错说缺少 Rdoc，先安装一下再执行后面的命令
# $ gem install rdoc 
# $ sudo gem install rdoc
$ gem list | cut -d" " -f1 | xargs gem uninstall -aIx
$ sudo gem list | cut -d" " -f1 | xargs gem uninstall -aIx

$ yay -R ruby rubygems
```

### 安装 RVM

RVM 和 Node.js 的 NVM 属于一类东西，都是环境的版本管理工具。

```bash
# 安装 GPG keys
$ gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
# 安装 RVM
$ \curl -sSL https://get.rvm.io | bash -s stable
# 刷新终端
$ source ~/.zshrc
# 切换 RVM 的 Ruby 安装源到国内镜像
$ echo "ruby_url=https://cache.ruby-china.com/pub/ruby" > ~/.rvm/user/db
```

### 安装 Ruby 和 gem

```bash
# 列出所有可装版本
$ rvm list known
# 安装制定版本
$ rvm install 2.7 --disable-binary
# 使用上面安装的版本
$ rvm use 2.7 --default

# 切换国内镜像
$ gem source -r https://rubygems.org/
$ gem source -a https://gems.ruby-china.com
# 查看镜像列表 保证只有 https://gems.ruby-china.com
$ gem sources -l
```

### 安装 Travis

```bash
$ gem install travis
```

### 终端登录

登录 Travis CI 使用的是 Github 账户。

```bash
# 这里有点要注意的是，Travis CI 有两个网站，一个 .org 一个 .com*
# 因为我使用的是 .com 的网站，所以加了 --pro
# 这个 --auto 必须加，不加会报错
$ travis login --pro --auto
```

### 导入私钥

```bash
# 因为我使用的是 .com 的网站，所以加了 --pro
$ travis encrypt-file ~/.ssh/id_rsa --add --pro
```

执行完这一步，`.travis.yml` 文件终会自动添加密钥匹配的代码，类似于下面这样。

```yml
after_success:
  - openssl aes-256-cbc -K $encrypted_fdda3485b8d6_key -iv $encrypted_fdda3485b8d6_iv -in id_rsa.enc -out ~/.ssh/id_rsa -d
```

但是！这里自动生成的代码有个**坑**！生成的代码会多一个转义符：`~\/.ssh/id_rsa -d`，去掉之后就正常了。

## 部署逻辑

### .travis.yml

```yml
# 下面一部分是将站点部署到 Github Pages
# 开始
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
# 结束

# 这里从 after_success 改到了 after_deploy
# 因为根据 Travis CI 的生命周期，after_success 的时候 还没有执行 deploy
after_deploy:
  - openssl aes-256-cbc -K $encrypted_fdda3485b8d6_key -iv $encrypted_fdda3485b8d6_iv -in id_rsa.enc -out ~/.ssh/id_rsa -d
  # 设置文件权限
  - chmod 600 ~/.ssh/id_rsa
  # 通过 SSH 登录服务器并执行脚本
  - ssh server_user@server_ip 'bash ~/pull_blog.sh'

addons:
  # 第一次远程登录服务器会出现 SSH 主机验证，添加信任 IP 可通过验证
  ssh_known_hosts: server_ip
```

### pull_blog.sh

很简单的脚本，就是进入文件夹然后 pull 一下。

```sh
#!/bin/bash
cd /www/wwwroot/u1s3.com
git pull
```

## 完成

到这里就结束了，往后只需要专心在 `code` 分支写文章，完成后提交到仓库，Travis CI 会自动将网站部署到 Github Pages 和 VPS中。

## 参考文档

[RVM](https://rvm.io/)

[RVM 实用指南](https://ruby-china.org/wiki/rvm-guide)

[Rubygem 常用命令](https://www.jianshu.com/p/fac708c689b6)

[Travis CI 自动化部署博客](https://segmentfault.com/a/1190000011218410)

[Travis-ci远程部署到服务器](https://blog.csdn.net/sp1206/article/details/80430493)

[Travis-CI自动化测试并部署至自己的CentOS服务器](https://www.cnblogs.com/homehtml/p/11796836.html)

[Hexo + GitHub + Travis CI + VPS 自动部署](https://www.changkun.us/archives/2017/06/232/)

[使用 Travis CI 部署博客 - 踩坑记录](https://dryyun.com/2018/06/25/travis-ci-deploy-blog/)
