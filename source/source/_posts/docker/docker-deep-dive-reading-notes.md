---
title: 深入浅出 Docker 读书笔记
date: 2019-12-05 15:55:14
categories: Docker
---

# 服务检测

## Docker 

```bash
$ yay -S docker docker-compose # 安装
$ docker version # 检查版本
```

> 同时出现 Client 和 Server 时表示服务已启动。

## Docker daemon

```bash
# 根据不同的操作系统使用不同的命令
$ service docker status
$ systemctl is-active docker
$ systemctl daemon-reload # 重启 daemon
$ systemctl restart docker # 重启 docker
```

# 加入本地用户组

```bash
# 创建 docker 用户组 并将当前用户加入到其中
$ sudo groupadd docker
$ sudo usermod -aG docker ${USER}
$ sudo systemctl restart docker
```

# 镜像加速

*/etc/docker/daemon.json*

```json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

# 容器

## 创建容器

```bash
$ docker container run --name container_name -it ubuntu:latest /bin/bash
```

参数解析：

- `--name container_name`：命名容器，在其他操作中，可以使用 `container_name` 代替 `container_id`；
- `-p 80:8080`：将主机的80端口映射到容器8080端口；
- `-it`：将 shell 切换到容器终端；
- `-d`：让应用以守护线程的方式在后台运行；
- `ubuntu:latest`：指定镜像；
- `/bin/bash`：运行容器中的程序；

## 启动容器

```bash
$ docker container start container_id
```

## 容器列表

```bash
$ docker container ls -a # -a 包含已停止的容器
$ docker ps -a
```

## 后台运行

可以使用 `Ctrl+PQ` 来退出容器并且保持容器运行。

## 连接容器

```bash
# 需要容器已启动
$ docker container exec -it container_id bash
```

## 暂停容器

```bash
$ docker container pause container_id
```

## 停止容器

```bash
$ docker container stop container_id
```

> 如果容器10秒钟之内没有得到清理并停止运行，将会自动强制停止。

**停止所有容器**

```bash
$ docker container stop $(docker container ls -aq) -f # -f 强制执行
$ docker stop $(docker ps -aq)
```

## 删除容器

删除前先停止容器是比较『优雅』的做法，这样会给进程机会来有序的处理停止前要做的事。

```bash
$ docker container rm container_id -f # -f 强制执行 即使未停止的容器也删除
$ docker rm container_id
```

**删除全部容器**

暴力删除所有容器，**一定不能**在生产环境或运行重要容器的系统上执行。

```bash
$ docker container rm $(docker container ls -aq) -f # -f 强制执行 即使未停止的容器也删除
$ docker rm $(docker ps -aq)
```

**删除所有停止的容器**

```bash
$ docker container prune -f
```

## 自我修复

在容器运行时配置好重启策略是推荐的方式，它可以在指定事件或错误后重启来完成自我修复。

重启策略应用于每个容器，可以作为参数传入 `docker container run` 中，也可以在 Compose 文件中声明。 

```bash
$ docker container run ... --restart always
```

容器支持的重启策略有：

- always：除非容器被明确停止，否则会一直尝试重启处于停止状态的容器。这个策略有一个很有意思的地方是，当 `daemon` 重启时，停止的容器也会被启动；
- unless-stopped：与 `always` 最大的区别在于不会在 `daemon` 重启时启动；
- on-failed：会在退出容器且返回值不是 `0` 的时候重启，也会随 `daemon` 启动。

## 启动运行

可查看到容器被使用时将运行的应用列表。

```bash
$ docker container inspect container_id
```

# 镜像

## 搜索镜像

```bash
$ docker search ubuntu
```


搜索时还可通过指定参数，返回需要的镜像

- 显示官方镜像：

  ```bash
  $ docker search ubuntu --filter="is-official=true"
  ```

- 显示自动创建的镜像：

  ```bash
  $ docker search ubuntu --filter="is-automated=true"
  ```

默认情况下返回 25 行数据，可指定行数：

```bash
$ docker seatch ubuntu --limit=100
```

## 拉取镜像

```bash
$ docker pull ubuntu
$ docker pull ubuntu:16.04
$ docker image pull ubuntu:latest
```

> 在不使用镜像 tag 时，会自动拉取 tag 为 latest 的镜像，但这**不代表** latest 镜像是最新版本。

**非官方仓库/非官方镜像仓库服务**

```bash
$ docker pull microsoft/dotnet:latest # 非官方仓库
$ docker pull gcr.io/microsoft/dotnet:latest # 非官方镜像仓库服务
```

**拉取仓库中的全部镜像**

```bash
# 拉取 microsoft/dotnet 的全部 tag
$ docker pull -a microsoft/dotnet
```

> 如果拉取的仓库同时包含多个平台，可能会失败。

## 删除镜像

删除镜像会删除当前主机上该镜像及相关的镜像层，对应包含镜像层数据的目录也会被删除。

但是如果某个镜像层被多个镜像共享，那只有当以来该镜像层的镜像都被删除后，该镜像层才会被删除。

```bash
$ docker image rm ubuntu:latest
```

**删除全部镜像**

可以通过在 `docker image rm` 后传入当前系统的全部镜像 ID 来删除所有镜像：

```bash
$ docker image rm $(docker images -q) -f # -q 获取镜像id
$ docker rmi $(docker images -q)
```

**删除所有不使用的镜像**

```bash
$ docker image prune -f -a # --force --all
```

## 镜像摘要

摘要是镜像内容的一个散列值，镜像内容的改变会造成散列值的改变。

```bash
# 查看本地镜像摘要
$ docker images --digests
```

使用摘要可准确拉取镜像：

```bash
$ docker pull ubuntu:sha256............
```

> 但是目前镜像摘要只能先通过标签方式拉取镜像到本地再获取，然后自己维护镜像的摘要列表。

## 本地列表

```bash
$ docker image ls
$ docker images
```

**格式化输出**

```bash
$ docker images --format "{{.Size}}"
```

```bash
$ docker images --format "{{.Repository}}:{{.Tag}}:{{.Size}}"
```

## 悬虚镜像

没有添加 tag 的镜像被称为悬虚镜像『dangling』，通常时因为构建镜像时使用了已经存在的 tag，docker 会移除旧版本镜像上的 tag。

```bash
$ docker images --filter dangling=true
```

**移除所有悬虚镜像**

```bash
$ docker images prune
```

## 镜像过滤

Docker 镜像过滤用法参照上面悬虚镜像，目前支持的 filter 参数有：

- dangling：可指定 true 或 false，返回悬虚镜像或非悬虚镜像；

- is-official：可指定 true 或 false，返回官方镜像或非官方镜像；

- is-automated：可指定 true 或 false，返回自动创建镜像或非自动创建镜像；

- before：需要镜像名或 ID 作为参数，返回在之前创建的全部镜像；

- since：与 before 类似，返回指定镜像之后创建的镜像；

- label：根据标注的名称或值对镜像过滤，`docker images` 不会输出标注内容；

- reference：其他过滤：

  ```bash
  # 获取所有 tag 为 latest 的镜像
  $ docker images --filter=reference="*:latest"
  ```

## 镜像分层

```bash
$ docker image inspect ubuntu
```

## 构建记录

```bash
$ docker history ubuntu
```

## 启动运行

查看镜像被使用时将运行的应用列表。

```bash
$ docker image inspect ubuntu
```

# 应用的容器化

Docker 的核心思想即是将应用整合到容器中。

## 一般步骤

完整的应用容器化主要分为：

- 编写应用代码；
- 创建 Dockerfile，包含应用描述、依赖及如何运行；
- 执行 `docker image build`；
- 等待构建；

一旦应用容器化完成，就能以镜像的形式交付并以容器的方式运行。

## Dockerfile

Dockerfile 文件包含两个用途：描述当前应用和指导 Docker 完成应用的容器化，它能实现开发和部署之间**无缝切换**。

```dockerfile
# Linux x64
# 新建镜像层 每个 Dockerfile 文件的第一行都是 FROM 指令，其作用在于指定基础镜像层
FROM alpine

# LABEL 指定了当前镜像的维护者
# 这个标签是键值对，在一个镜像中可以通过增加 LABEL 的方式来为镜像添加自定义元数据
LABEL maintainer="nigelpoulton@hotmail.com"

# 新建镜像层 安装 Node 和 NPM
# 安装命令根据实际情况会有不同
RUN apk add --update nodejs nodejs-npm

# 新建镜像层 将应用相关文件从构建上下文复制到镜像中
COPY . /src

# 指定工作目录
WORKDIR /src

# 新建镜像层 执行命令
RUN npm install

# 指定端口 会作为元数据保存
EXPOSE 8080

# 指定镜像入口程序 会作为元数据保存
ENTRYPOINT ["node", "./app.js"]
```

Dockerfile 文件中有几点是需要注意的：

- 注释以 `#` 开头；
- 除了注释以外的每一行都是一条指令；
- 指令不区分大小写，但通常会写成大写来增加可读性；
- 指令会按照顺序执行；
- FROM 指令 通常是第一条指令；
- 使用 `FROM` 指令引用官方基础镜像并选择较小的镜像文件通常能规避一些且在问题；
- 不是所有指令都会新建镜像层，有些指令仅会新增或修改元数据；
- 关于如何判断指令是否会新增镜像层的基本原则是看指令是否会向镜像中增添新的文件或程序；
- 可以通过 `$ docker image history web:latest` 来查看镜像构建过程中所执行的指令，结果中 SIZE 不为 0 的就是新建了镜像层；

## 构建镜像

```bash
$ docker image build -t web:latest .
```

> 这里的点不能省略 表示 Docker 在构建时，使用当前目录作为构建上下文。

参数解析：

- `-t`：tag 为镜像打标签；
- `-f`：指定 Dockerfile 的路径和名称，可以指定位于任意路径下的任意名称的 Dockerfile；
- 构建上下文：指应用文件存放的位置，可以是本地 Docker 主机上的一个目录或远程 Git 仓库；

构建完成后可通过 `$ docker image inspect web:latest` 来确认构建的镜像是否正确。

## 多阶段构建

> 对 Docker 镜像 来说，过大的体积带来的弊端不限于慢、难用、脆弱和易受攻击。

鉴于此， Docker 镜像应该尽可能的小，对生产环境来说，目标是缩小到仅包含运行的**必需**内容就行。但问题在于生成体积较小的镜像并非易事。

开发者通常不会在构建完成后进行清理，当使用 RUN 执行一个命令时，可能会拉取一些构建工具，这些工具会留存在镜像中移交至生产环境，这是很不合适的。

有多种方式来改善这个问题，常见的是采用建造者模式（Builder Pattern）和多阶段构建（Multi-Stage Build），但无论采取哪种方式，通常都需要额外的培训，并且会增加构建的复杂度。

**建造者模式**

> 建造者模式是一种比较复杂的方式。

建造者模式至少需要两个 Dockerfile，一个用于开发一个用于生产。

- 首先需要编写 Dockerfile.dev，其基于一个大型基础镜像（Base Image），拉取所需的构建工具，并构建应用；
- 然后基于 Dockerfile.dev 构建镜像，并使用这个镜像穿件容器；
- 再编写 Dockerfile.prod ，其基于一个较小的基础镜像开始构建，并从刚才创建的容器中将应用程序相关的部分复制过来。

这种方式是可行的，但是整个过程需要编写额外的脚本才能串联起来，比较复杂。

**多阶段构建**

> 多阶段构建是一种更好的方式！其可以在不增加复杂性的情况下优化构建过程。

多阶段构建是随 Docker 17.05 新增的一个特性，用于构建精简的生产环境镜像。

其在构建时使用一个 Dockerfile，其中包含多个 FROM 指令，每个 FROM 指令都是一个新的构建阶段（Build Stage），且可以方便的复制之前阶段的构件。

*Dockerfile*

```dockerfile
# 阶段0 storefront
FROM node:latest AS storefront
WORKDIR /usr/src/atsea/app/react-app
COPY react-app .
RUN npm install
RUN npm run build

# 阶段1 appserver
FROM maven:latest AS appserver
WORKDIR /usr/src/atsea
COPY pom.xml .
RUN mvn -B -f pom.xml -s /usr/share/maven/ref/settings-docker.xml dependency:resolve
COPY . .
RUN mvn -B -s /usr/share/maven/ref/settings-docker.xml package -DskipTests

# 阶段2 production
FROM java:8-jdk-alpine production
RUN adduser -Dh /home/gordon gordon
WORKDIR /static
# 重点在这里 复制了前面阶段打包的文件
COPY --from=storefront /usr/src/atsea/app/react-app/build/ .
WORKDIR /app
# 重点在这里
COPY --from=appserver /usr/src/atsea/target/AtSea-0.0.1-SNAPSHOT.jar .
ENTRYPOINT ["java", "-jar", "/app/AtSea-0.0.1-SNAPSHOT.jar"]
CMD ["--spring.profiles.active=postgres"]
```

其中每个 FROM 指令构成一个单独的构建阶段，各个阶段在内部从 0 开始编号，可通过 `AS` 为每个阶段其别名。

重点在于 `COPY --from ....` 这条指令，它从之前的构建阶段的镜像中**仅复制生产环境相关的应用代码**，而不会复制生产环境不需要的构件。

还有就是多阶段构建仅用到一个 Dockerfile 文件，且构建命令不需要增加额外的参数。

## 最佳实践

> 部分最佳实践。

**连接命令**

Dockerfile 中不同的写法就会对镜像体积产生显著影响，常见的像是每一个 RUN 指令都会新增一个镜像层，因此通过使用 `&&` 来连接多个命令以及使用 `\` 换行的方法将多个命令包含在一个 RUN 指令中，通常时一种值得提倡的方式；

**利用构建缓存**

Docker 在构建的过程中会充分利用缓存机制，如果重复构建同一内容，第二词构建几乎可以立即完成，因为第一次的构建的内容会被缓存下来，并被后续的构建过程复用。

- 每执行一条指令，Docker 都会检查缓存中是否已经有与该指令对应的镜像层；
- 如果有，则为缓存命中（Cache Hit），会直接使用这个镜像层；
- 如果无，则为缓存未名中（Cache Miss），Docker 会基于该指令构建新的镜像层；
- 缓存命中可以显著加快构建过程；

```dockerfile
# 引入基础镜像，如果这个镜像存在，则跳到下一条指令，不存在则从 Docker Hub 拉取
FROM alpine

# 检查缓存中是否存在基于同一基础镜像，且执行了同样指令的镜像层
# 找到 则跳到下一条指令，并链接到已经存在的镜像层
# 没找到 则设置缓存无效并构建该镜像层
# 这里『设置缓存无效』作用于本次构建的后续部分，接下来的指令将全部执行而不会再尝试查找构建缓存
RUN apk add --update nodejs nodejs-npm

# 如果上一条指令缓存命中，则会继续查找是否有一个缓存也执行了这条命令
# COPY 和 ADD 指令会检查复制到镜像中的内容自上一次构建之后是否发生了变化，Docker 会计算每一个被复制文件的 Checksum 值，并一一比对，如果不匹配，则也会设置缓存无效
COPY . /src

......
```

所以，在编写 Dockerfile 文件时要**特别注意**的一点是：尽量将易于发生变化的指令置于 Dockerfile 文件的后方执行，这可以将缓存未名中的情况放到构建的后期，从而减少构建所需时间。

如果需要忽略缓存，可以在构建时加入 `--nocache=true` 参数来强制忽略缓存的使用。

**使用 no-install-recommends**

构建 Linux 镜像时，如果使用 APT 包管理器，则应该在执行 `apt get install` 命令时增加 `no-install-recommends`，这可以确保仅安装核心依赖，不安装推荐和建议的包，能显著减少不必要的包的下载量；

**不要安装 MSI 包**

构建 Windows 镜像时，要避免使用 MSI 包管理器，其对空间的利用率不高，会大幅度增加镜像体积；

**合并镜像**

```bash
$ docker image build --squash # 通过添加 --squash 来创建一个合并的镜像
```

合并镜像其实并非一个最佳实践，其可以说是利弊参半。总体来说，Docker 会遵循正常的方式构建镜像，但之后会增加一个额外的步骤，将所有内容合并到一个镜像层中。

优点：当镜像层数太多时，合并是一个不错的优化方式，比如当创建一个新的基础镜像，以便基于它来构建其他镜像的时候，这个基础镜像就最好被合并为一层。

缺点：合并的镜像无法共享镜像层，会导致空间的低效利用，且 push 和 pull 的镜像体积更大。

## Docker Hub

使用 `$ docker image push` 时，会将 Docker Hub 作为默认推送地址，但推送前还需要：

```bash
# 登录
$ docker login

# 为镜像添加一个额外的标签，不会覆盖已经存在的标签
# 格式为 docker image tab current_tag new_tag
$ docker image tag web:latest account/web:latest

# 推送到 Docker Hub
$ docker image push account/web:latest
```

# Docker Compose

> 部署和管理繁多的服务是困难的，这一点也正式 Docker Compose 要解决的问题。

Docker Compose 能够在 Docker 节点上，以单引擎模式（Simgle-Engine Mode）进行多容器的部署和管理。

Docker Compose 不会通过脚本和冗长的命令来组织应用组建，而是通过声明式的配置文件描述整个应用，从而使用一条命令完成部署。

部署成功后，还可以通过一系列简单的命令实现对其完整声明周期的管理，甚至配置文件还可以置于版本控制系统中进行存储和管理。

## 安装

```bash
$ yay -S docker-compose
```

## Compose 文件

Docker Compose 使用 YAML 文件来定义多服务的应用，也可以使用 JSON。

默认使用 *docker-compose.yml* 作为文件名，也可以使用 `-f` 参数指定具体文件。









# Docker Swarm

Docker Swarm 能够以 Swarm 模式对 Docker 节点上的多容器应用进行部署和管理









# 卷与持久化数据

数据主要分两类，持久化和非持久化，Docker 对这两种数据都有对应的支持方式。

**非持久化数据**自动创建，从属于容器，生命周期同容器，**会**随容器的删除而删除。

**持久化数据**是需要保存的数据，**不会**随容器的删除而删除，如果需要保存容器数据，就需要将数据存储到卷上。

卷与容器时完全解耦的，可以独立地创建及管理，删除关联了卷的容器并**不会**删除卷。













