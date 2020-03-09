---
title: Docker 国内镜像
date: 2019-12-05 17:25:14
categories: Docker
---

## 切换国内镜像

*/etc/docker/daemon.json*

```json
{
  "registry-mirrors" : [
    "http://ovfftd6p.mirror.aliyuncs.com",
    "http://registry.docker-cn.com",
    "http://docker.mirrors.ustc.edu.cn",
    "http://hub-mirror.c.163.com"
  ],
  "insecure-registries" : [
    "registry.docker-cn.com",
    "docker.mirrors.ustc.edu.cn"
  ],
  "debug" : true,
  "experimental" : true
}
```

## linux镜像下载慢

出现和 `dl-cdn.alpinelinux.org` 相关的问题时，添加下面语句：

*Dockerfile*

```dockerfile
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

# 在这条上面添加
RUN apk add --update nodejs nodejs-npm
```

## git依赖下载慢

出现和 git 相关的问题时，添加下面语句：

```dockerfile
# 在这条下面添加
RUN apk add git

RUN git config --global http.https://github.com.proxy 127.0.0.1:1080
RUN git config --global https.https://github.com.proxy 127.0.0.1:1080
```

## 延伸阅读

- [Go 应用构建 Docker 镜像 - 解决依赖下载慢、失败问题](https://blog.csdn.net/u013360850/article/details/90602149)

- [alpine linux v3.6-使用国内镜像源进行加速](https://www.songma.com/news/txtlist_i29123v.html)





