---
title: "Travis CI 自动化部署应用到服务器"
date: 2020-03-06T11:38:22+08:00
draft: true

tags: ['Travis-CI', 'Ruby']
categories: ['Programing']
---

基本原理是 Travis CI 使用远程登录服务器，执行指定脚本。但在 Travis CI 中不像交互式终端那样可以输入帐号密码，所以需要使用 SSH免密登录。

<!--more-->

**导入公钥**

要实现免密登录需要先将生成的密钥对中的公钥保存到服务器的 `~/.ssh/authrized_keys` 中。

```bash
# 密钥对使用 git 生成的就可以
$ ssh-copy-id -i ~/.ssh/id_rsa.pub account@ip_address
```

**导入私钥**

将私钥导入到 Travis CI 中使用的工具 Travis 需要通过 Ruby 的包管理工具 gem 安装。我当前使用的系统环境是 Manjaro，本身自带例 Ruby，但是试来试去总是报错，就卸载了并使用 RVM 重装了 Ruby 和 Gem。

卸载 Ruby 和 gem

```bash
# 删除当前安装的所有 gem 包
# 这一步可能会报错说缺少 Rdoc，先安装一下再执行后面的命令
# $ gem install rdoc 
# $ sudo gem install rdoc
$ gem list | cut -d" " -f1 | xargs gem uninstall -aIx
$ sudo gem list | cut -d" " -f1 | xargs gem uninstall -aIx

$ yay -R ruby rubygems
```

安装 RVM

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

安装 Ruby 和 gem

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

安装 Travis

```bash
$ gem install travis
```

终端登录 [travis-ci.com](https://travis-ci.com/)

```bash
# 因为我使用的是 .com 的网站，所以加了 --pro
$ travis login --pro --auto
```

导入私钥

```bash
$ travis encrypt-file ~/.ssh/id_rsa --add --pro
```

执行完这一步，`.travis.yml` 文件终会自动添加密钥匹配的代码。







1

**参考文档**

[RVM](https://rvm.io/)

[RVM 实用指南](https://ruby-china.org/wiki/rvm-guide)

[Rubygem 常用命令](https://www.jianshu.com/p/fac708c689b6)

[Travis CI 自动化部署博客](https://segmentfault.com/a/1190000011218410)

[Travis-ci远程部署到服务器](https://blog.csdn.net/sp1206/article/details/80430493)



https://www.changkun.us/archives/2017/06/232/

https://www.cnblogs.com/homehtml/p/11796836.html



\- ssh ubuntu@123.123.123.123 'cd ~/projects/proj && git pull && pkill uwsgi'



after_success: - ssh -tt ubuntu@123.123.123.123 < run.sh