# 向 Docker 镜像仓库推送镜像

现在我们的 Docker 镜像仓库已经启动并运行，是时候看看它的实际效果了！在这一步中，我们将获取一个示例 Docker 镜像并将其推送到本地 Docker 镜像仓库。

## 目标

本步骤的目标是将一个镜像推送到本地 Docker 镜像仓库。

## 结果示例

以下是将镜像推送到本地 Docker 镜像仓库的步骤：

1. 从 Docker Hub 拉取 `hello-world` 镜像。

![拉取 hello-world 镜像](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-2-1.png)

2. 使用我们的镜像仓库地址为 `hello-world` 镜像创建一个新标签（Tag）。

![标记 hello-world 镜像](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-2-2.png)

3. 将 `hello-world` 镜像推送到我们的镜像仓库。

![向本地镜像仓库推送镜像](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-local-docker-registry/zh/../assets/challenge-deploying-local-docker-registry-2-3.png)

完成此步骤后，你将成功把一个 Docker 镜像推送到本地 Docker 镜像仓库中。
