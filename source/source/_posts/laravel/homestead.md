---
title: Laravel Homestead 开发环境搭建
date: 2017-12-25 22:45:56
categories: Laravel
---

## 安装虚拟机

### Windows

[VirtualBox](https://www.virtualbox.org/wiki/Downloads)、 [Vagrant](https://www.vagrantup.com/downloads.html)

### Manjaro

```bash
$ uname -r # 查看内核版本
$ yay -S virtualbox # 安装相应内核版本
$ yay -S vagrant
```

安装完重启。

## 安装 Homestead

```bash
$ vagrant box add laravel/homestead	# 执行命令 查看下载链接 使用下载工具下载
```

编写 `metadata.json` 文件，和 `virtualbox.box` 放同一目录，**使用非中文路径**。

```json
{
    "name": "laravel/homestead",
    "versions": 
    [
        {
            "version": "6.4.0",
            "providers": [
                {
                  "name": "virtualbox",
                  "url": "virtualbox.box"
                }
            ]
        }
    ]
}
```

导入盒子

```bash
$ vagrant box add metadata.json
```

<!-- more -->

## 环境配置

下载管理脚本 [查看最新版本](https://github.com/laravel/homestead/releases)

```bash
$ git clone https://github.com/laravel/homestead.git ~/Homestead
$ cd ~/Homestead
$ git checkout v7.8.0 # 切换最新分支
$ bash init.sh # 初始化环境参数
```

创建` ~/Code `文件夹

配置`Homestead.yaml`

```ymal
---
ip: "192.168.0.111"
memory: 2048
cpus: 2
provider: virtualbox

authorize: ~/.ssh/id_rsa.pub

keys:
    - ~/.ssh/id_rsa
    - ~/.ssh/id_rsa.pub

folders:
    - map: ~/Code
      to: /home/vagrant/Code

sites:
    - map: laravel.test
      to: /home/vagrant/Code/laravel/public

databases:
    - laravel

features:
    - mariadb: false
    - ohmyzsh: false
    - webdriver: false

# ports:
#     - send: 50000
#       to: 5000
#     - send: 7777
#       to: 777
#       protocol: udp

# variables:
#     - key: APP_ENV
#       value: local
```

启动&登录

```bash
$ vagrant up && vagrant ssh
```

切换国内镜像

```bash
$ npm config set registry https://registry.npm.taobao.org
$ yarn config set registry https://registry.npm.taobao.org
$ composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

配置git

```bash
$ git config --global user.name "name"
$ git config --global user.email "email@example.com"
$ git config --global push.default simple
```

添加`ssh-agent`

```bash
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_rsa
```

## 操作指令

### 启动

```bash
$ vagrant up
```

### 登录

```bash
$ vagrant ssh
$ ssh vagrant@127.0.0.1	-p 2222 # 如果上面登陆失败, 还可以通过此方式
```

### 重启

```bash
$ vagrant reload
```

### 关闭

```bash
$ vagrant halt
```

### 刷新配置

```bash
$ vagrant reload --provision
```

## 容器操作

### 查看列表

```bash
$ vagrant box list
```

### 删除容器

```bash
$ vagrant box remove boxname
```

### 删除虚拟机

```bash
$ vagrant destroy
```

## 局域网访问

修改 `Homestead.yaml` 中 `ip` 参数为当前局域网同网段

修改 `~/Homestead/scripts/homestead.rb` 中 `private_network` 参数为 `public_network`

```rb
# Configure A Private Network IP
...
config.vm.network :public_network, ip: settings['ip'] ||= '192.168.10.10'
...
```

## Windows优化

### NFS模式

安装插件

```bash
$ vagrant plugin install vagrant-winnfsd
```

修改文件`homestead/scripts/homestead.rb`, 将

```rb
# Register All Of The Configured Shared Folders
if settings.include? 'folders'
...
end
```

替换为

```rb
    # Register All Of The Configured Shared Folders
    if settings.include? 'folders'
        settings["folders"].sort! { |a,b| a["map"].length <=> b["map"].length }

        settings["folders"].each do |folder|
            config.vm.synced_folder folder["map"], folder["to"], 
            id: folder["map"],
            :nfs => true,
            :mount_options => ['nolock,vers=3,udp,noatime']
        end
    end
```

修改文件`Homestead.yaml`

```yaml
folders:
    - map: ~/Code
      to: /home/vagrant/Code
      type: nfs
```

### SMB模式

修改文件`Vagrantfile`

```yaml
...
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
#SMB
config.vm.synced_folder ".", "/vagrant", type: "smb",
    smb_username: "",
    smb_password: "",
    owner: "vagrant",
    group: "root"
...
```

修改文件`Homestead.yaml`

```bash
...
folders:
    - map: ~/Code
        to: /home/vagrant/Code
        type: smb
...
```

修改`nginx`参数

```
# FILE: /etc/nginx/nginx.conf
# 需要 sudo 权限
sendfile: off
```

## 科学上网

安装插件

```bash
$ vagrant plugin install vagrant-proxyconf
```

SSR客户端 -> 选项设置 -> 允许来自局域网的链接

修改文件`Vagrantfile`，代理ip为主机IPv4地址

```yaml
if Vagrant.has_plugin?("vagrant-proxyconf")
	config.proxy.http = "http://192.168.1.112:1080/"
	config.proxy.https = "http://192.168.1.112:1080/"
	config.proxy.no_proxy = "localhost,127.0.0.1"
end
```

测试连接

```bash
$ curl -v  "www.google.com"
```

## 延伸阅读

- [开发环境搭建 - Windows](https://learnku.com/docs/laravel-development-environment/5.8/development-environment-windows/4373)

- [如何升级 Homestead 盒子？](https://learnku.com/docs/laravel-development-environment/5.8/upgrade-homestead-box/4376)

- [Vagrant入门](http://www.cnblogs.com/davenkin/p/vagrant-virtualbox.html)

- [Homestead 官方文档](https://laravel.com/docs/5.7/homestead)

- [Laravel-Homestead 环境搭建的各种坑](https://laravel-china.org/topics/2990/laravel-homestead-environment-to-build-a-variety-of-pits)

- [Homestead 安装问题汇总](https://laravel-china.org/topics/4796/ssh-auth-method-private-key-solution-summary-when-homestead-starts)

