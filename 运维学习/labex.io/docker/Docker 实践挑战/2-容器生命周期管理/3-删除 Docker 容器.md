# 删除 Docker 容器

移除或删除容器意味着销毁容器内部运行的所有进程，然后删除该容器本身。

## 目标

你的目标是停止正在运行的容器并将其删除。

## 结果示例

以下是完成本挑战后你应该能够实现的效果示例：

1. 使用 `nginx:1.23.1` 镜像启动一个 `web4` 容器。

   ![Starting web4 container](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-3-1.png)

2. 尝试删除正在运行的 `web4` 容器。但 Docker 守护进程会抛出错误。我们不能删除一个正在运行的 Docker 容器。

   ![Error deleting running container](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-3-2.png)

3. 停止 `web4` 容器。

   ![Stopping the web4 container](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-3-3.png)

4. 删除 `web4` 容器。

   ![Deleted web4 container confirmation](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-container-lifecycle-management/zh/../assets/challenge-container-lifecycle-management-3-4.png)

## 要求

要完成本实验，你需要：

- 确保 Docker 已安装并正常运行。
- 有一个正在运行的容器。
