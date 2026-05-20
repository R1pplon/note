# 安装和运行 Docker 镜像

在这一步中，你将学习如何安装和运行一个 Docker 镜像。Docker 镜像是一个轻量级、独立且可执行的包，它包含了运行应用程序所需的一切，包括代码、运行时、库、环境变量和配置文件。

## 目标

本步骤的目标是安装并运行一个简单 Web 应用程序的 Docker 镜像。

## 要求

- 你的系统上需要安装 Docker。
- 稳定的互联网连接以下载 Docker 镜像。

## 结果示例

1. 使用 `docker run` 命令下载并运行一个 Web 应用程序的 `tutum/hello-world` 镜像。并将容器内的 `80` 端口映射到宿主机的 `8080` 端口。

在另一个终端中，你可以使用 `docker ps` 命令查看正在运行的容器。

```
labex:~/ $ docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS         PORTS                                   NAMES
3ca0e7c373a9   tutum/hello-world   "/bin/sh -c 'php-fpm…"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   bold_volhard
```

2. 等待 Docker 下载镜像并启动容器。

3. 在你的 Web 浏览器中打开 `http://localhost:8080` 访问 Web 应用程序。

![Docker 容器运行示例](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-package-and-run-applications/zh/../assets/challenge-package-and-run-applications-1-3.png)
