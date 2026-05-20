# 搭建 Docker 镜像仓库

本次挑战的第一步是搭建本地 Docker 镜像仓库。为此，我们将利用 Docker 官方提供的 `registry` 镜像。

## 目标

本步骤的目标是成功搭建一个 Docker 镜像仓库。

## 结果示例

以下是搭建 Docker 镜像仓库的步骤：

1. 拉取 `registry` 镜像。

![拉取 Docker registry 镜像](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-1-1.png)

2. 在 `/home/labex` 路径下创建一个名为 `dockerregistry` 的目录，以便于容器镜像的数据持久化。

![创建 dockerregistry 目录](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-1-2.png)

3. 使用 `registry` 镜像运行一个名为 `my-registry` 的容器，映射端口，并将你创建的 `dockerregistry` 目录挂载为数据卷以实现数据持久化。

![运行 Docker registry 容器](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-1-3.png)

4. 使用 `curl` 命令验证镜像仓库是否可用。

![验证 Docker registry 可用性](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-1-4.png)

完成此步骤后，你的本地机器上将运行一个 Docker 镜像仓库实例。
