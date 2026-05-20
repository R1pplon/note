# 从 Docker 镜像仓库拉取镜像

Docker 镜像仓库目前正在运行，并且已经存放了我们的示例镜像。在这一步中，我们将演示如何从本地 Docker 镜像仓库中拉取镜像。

## 目标

本步骤的目标是从镜像仓库中拉取镜像。

## 结果示例

以下是从本地 Docker 镜像仓库拉取镜像的步骤：

1. 使用 `docker rmi` 命令删除本地已标记为本地仓库地址的 `hello-world` 镜像副本。

![删除 hello-world Docker 镜像](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-3-1.png)

2. 从你的本地 Docker 镜像仓库拉取镜像。

![从本地拉取 Docker 镜像](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-3-2.png)

3. 为你刚刚拉取的镜像运行一个容器。

![从镜像运行 Docker 容器](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-3-3.png)

完成此步骤后，你将完成从本地镜像仓库拉取 Docker 镜像的操作。
