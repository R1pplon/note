# 准备 Docker 环境

在此步骤中，我们将通过确保 Docker 已安装并正在运行来准备我们的 Docker 环境。此外，我们将拉取 `hello-world` Docker 镜像，并基于该镜像运行一个容器。

#### 任务 1：安装 Docker

首先，让我们确保 Docker 已安装。如果尚未安装，请按照官方文档为你的特定操作系统安装 Docker。

#### 任务 2：启动 Docker 服务

通过在终端中执行以下命令，确保 Docker 服务已启动并正在运行：

```bash
sudo systemctl start docker
```

#### 任务 3：拉取并运行 `hello-world` 镜像

通过运行以下命令拉取 `hello-world` Docker 镜像：

```bash
docker pull hello-world
```

接下来，使用以下命令基于 `hello-world` 镜像运行一个容器：

```bash
docker run hello-world
```
