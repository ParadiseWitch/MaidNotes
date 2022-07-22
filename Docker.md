---
date updated: 2022-07-21 18:19
---

# Docker
Tags: #Docker 

> 本文有关Docker的基础知识和概念请到👇下面的链接或者搜索获得，此处不会详细记录。本文记录的基础知识只为防止忘记。
- Links:
  - [前言 - Docker — 从入门到实践](https://yeasy.gitbook.io/docker_practice/)
  - [Docker Dockerfile | 菜鸟教程](https://www.runoob.com/docker/docker-dockerfile.html)

# [Image 镜像](https://yeasy.gitbook.io/docker_practice/basic_concept/image)
- !。除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像 不包含 任何动态数据，其内容在构建之后也不会被改变。

## [分层存储](https://yeasy.gitbook.io/docker_practice/basic_concept/image)

镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。
比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。

# [Container 容器](https://yeasy.gitbook.io/docker_practice/basic_concept/container)

<u>容器的实质是进程</u>，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。

每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为 **==容器存储层==**。

按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 [数据卷(Volume)](https://yeasy.gitbook.io/docker_practice/data_management/volume)、或者 [绑定宿主目录](https://yeasy.gitbook.io/docker_practice/data_management/bind-mounts)，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。

数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。

# [利用 commit 理解镜像构成](https://yeasy.gitbook.io/docker_practice/image/commit)

## commit命令

```bash
docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]

docker commit \
    --author "Tao Wang <twang2218@gmail.com>" \
    --message "修改了默认网页" \
    webserver \
    nginx:v2
sha256:07e33465974800ce65751acc279adc6ed2dc5ed4e0838f8b86f0c87aa1795214
```

其中 `--author` 是指定修改的作者，而 `--message` 则是记录本次修改的内容。这点和 git 版本控制相似，不过这里这些信息可以省略留空。

## 谨慎使用docker commit

> 使用 `docker commit` 命令虽然可以比较直观的帮助理解镜像分层存储的概念，但是实际环境中并不会这样使用。
>
> 1. 除了自己真正想要修改的东西以外，你可能还会commit其他你不想提交的文件
> 2. 使用 `docker commit` 意味着所有对镜像的操作都是黑箱操作，生成的镜像也被称为 **黑箱镜像**，换句话说，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。而且，即使是这个制作镜像的人，过一段时间后也无法记清具体的操作。这种黑箱镜像的维护工作是非常痛苦的。

# [使用 Dockerfile 定制镜像](https://yeasy.gitbook.io/docker_practice/image/build)

[Docker Dockerfile | 菜鸟教程](https://www.runoob.com/docker/docker-dockerfile.html)

## build命令
```sh
docker build [选项] <上下文路径/URL/->

# example
docker build -t nginx:v3 .
# 直接用 Git repo 进行构建
docker build -t hello-world https://github.com/docker-library/hello-world.git#master:amd64/hello-world
# 用给定的 tar 压缩包构建
docker build http://server/context.tar.gz
# 从标准输入中读取 Dockerfile 进行构建
docker build - < Dockerfile
# 或者
cat Dockerfile | docker build -
# 从标准输入中读取上下文压缩包进行构建
docker build - < context.tar.gz
```

## [多阶段构建](https://yeasy.gitbook.io/docker_practice/image/multistage-builds)
