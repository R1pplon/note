# 使用 Podman 管理容器

本次挑战将引导你体验容器的生命周期。你将使用 `podman` 运行一个新容器，检查其状态，然后将其停止。

## 任务

- 使用 `nginx` 镜像运行一个容器。
- 列出正在运行的容器以确认其已启动。
- 停止该运行中的容器。
- 再次列出正在运行的容器以确认其已停止。

## 要求

- 所有容器操作必须使用 `podman` 命令。
- 使用 `docker.io/library/nginx` 镜像运行容器。
- 容器必须命名为 `my-nginx`。
- 容器必须在后台运行（分离模式）。
- 使用 `podman ps` 验证容器状态。

## 示例

运行容器后，`podman ps` 的输出应类似于：

```plaintext
CONTAINER ID  IMAGE                           COMMAND               CREATED         STATUS             PORTS       NAMES
d1234567890a  docker.io/library/nginx:latest  nginx -g daemon o...  10 seconds ago  Up 8 seconds ago               my-nginx
```

停止容器后，执行 `podman ps` 命令应无任何输出，表示当前没有正在运行的容器。
