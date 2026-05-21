# 探索 Red Hat Universal Base Images (UBI)

在这一步，你将探索 Red Hat 的通用基础镜像 (UBI)，它们是基于 RHEL 的企业级容器镜像。与需要完整虚拟机的传统 RHEL 安装不同，UBI 镜像在轻量级、可移植的容器中提供 RHEL 环境。这些镜像可以自由重新分发，并专为现代云原生应用程序设计。

Red Hat 提供了几个针对不同用例优化的 UBI 变体。 `redhat/ubi9` 镜像提供了一个完整的基于 RHEL 的容器环境，带有 `dnf` 包管理器，使其适用于需要软件安装和系统自动化的应用程序。

首先，让我们检查为这个实验准备的 Red Hat 容器配置模板。这个文件演示了传统的 Kickstart 概念如何转化为容器环境。

```bash
sudo cat /etc/labex/rhel-container-config.cfg
```

你将看到一个 Dockerfile 风格的配置输出，它反映了传统的安装概念：

```plaintext
# RHEL Container Configuration Template
# Based on traditional Kickstart concepts adapted for containers

# Base image specification
FROM redhat/ubi9

# System locale and timezone
ENV LANG=en_US.UTF-8
ENV TZ=America/New_York

# User configuration
ENV CONTAINER_USER=labex
ENV ROOT_PASSWORD=redhat

# Package installation
# Packages: httpd, curl (container-appropriate equivalents)
RUN dnf install -y --allowerasing httpd curl && \
    dnf clean all

# Service configuration
EXPOSE 80

# Startup command
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
```

现在，让我们探索可用的 Red Hat UBI 镜像。首先，检查 Docker 是否正在运行并且可访问：

```bash
docker --version
```

拉取 Red Hat UBI 9 镜像，它提供了一个完整的基于 RHEL 的容器环境：

```bash
docker pull redhat/ubi9
```

你应该看到类似这样的输出：

```plaintext
Using default tag: latest
latest: Pulling from redhat/ubi9
Digest: sha256:...
Status: Downloaded newer image for redhat/ubi9:latest
docker.io/redhat/ubi9:latest
```

列出已下载的镜像以确认拉取成功：

```bash
docker images redhat/ubi9
```

输出将显示有关镜像的详细信息：

```plaintext
REPOSITORY      TAG       IMAGE ID       CREATED      SIZE
redhat/ubi9     latest    b1c2d3e4f5g6   5 days ago   216MB
```

现在，让我们运行一个基本的容器来探索 RHEL 环境：

```bash
docker run -it --rm redhat/ubi9 /bin/bash
```

在容器内部，通过检查操作系统版本来探索 RHEL 环境：

```bash
cat /etc/redhat-release
```

你应该看到类似的内容：

```plaintext
Red Hat Enterprise Linux release 9.6 (Plow)
```

检查可用的包管理器：

```bash
dnf --version
```

通过键入以下内容退出容器：

```bash
exit
```

将模板配置复制到你的项目目录中以进行自定义：

```bash
sudo cp /etc/labex/rhel-container-config.cfg ~/project/rhel-container.dockerfile
sudo chown labex:labex ~/project/rhel-container.dockerfile
```

验证文件是否已成功复制：

```bash
ls -l ~/project/rhel-container.dockerfile
```

你现在已经成功地探索了 Red Hat UBI 镜像，并准备好在下一步中创建自定义容器配置。
