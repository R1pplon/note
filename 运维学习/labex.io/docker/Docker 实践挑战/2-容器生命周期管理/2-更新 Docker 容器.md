# 更新 Docker 容器

假设你正在一个容器中运行某个服务，现在该服务的 Docker 镜像有了新版本。在这种情况下，你需要更新你的 Docker 容器。

## 目标

你的目标是运行一个容器，然后升级该容器所使用的镜像。

## 结果示例

以下是完成本挑战后你应该能够实现的效果示例：

1. 使用 `nginx:1.22.1` 镜像启动一个名为 `web3` 的容器。

   ![Starting web3 container](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-2-1.png)

2. 将 `nginx:1.23.1` 镜像拉取到本地宿主机。

   ![Pulling new Docker image](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-2-2.png)

3. 停止 `web3` 容器。

   ![Stopping the web3 container](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-2-3.png)

4. 删除 `web3` 容器。

   ![Deleting Docker container web3](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-2-4.png)

5. 使用 `nginx:1.23.1` 镜像启动一个名为 `web3` 的容器（即完成更新）。

   ![Container started with updated image](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-2-5.png)

## 要求

要完成本实验，你需要：

- 确保 Docker 已安装并正常运行。
- 有一个可用的 Docker 镜像用于容器更新。
