# 启动 Docker 容器

本节将通过两种方式带你启动一个容器：改变容器的状态使其进入运行（Running）状态。

## 目标

你的目标是使用两种不同的方式启动容器：第一种是先创建容器，然后再启动它；第二种是直接使用 `docker run` 命令启动容器。

## 结果示例

以下是完成本挑战后你应该能够实现的效果示例：

1. 首先，将镜像 `nginx:1.22.1` 拉取到宿主机。

   ![Pulling nginx image](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-1-1.png)

2. 使用 `nginx:1.22.1` 镜像创建一个名为 `web1` 的新容器。

   ![Creating container web1](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-1-2.png)

3. 启动该容器，使其状态变为 Running。

   ![Starting Docker container web1](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-1-3.png)

4. 使用 `docker inspect web1` 命令查看具体的运行状态详情。

   ![docker inspect web1 status](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-1-4.png)

5. 使用 `docker run` 命令，基于 `nginx:1.22.1` 镜像直接创建一个名为 `web2` 的新容器。

   ![Creating container with docker run](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-1-5.png)

## 要求

要完成本实验，你需要：

- 一个可用的 Docker 安装环境。
- 了解 Docker 的相关命令。
