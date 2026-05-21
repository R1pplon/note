# 使用 Skopeo 复制容器镜像

`skopeo` 是一个功能强大的工具，用于在不同类型的存储之间移动容器镜像，例如公共仓库、本地存储和私有仓库。在此步骤中，你将把 `nginx` 镜像复制到运行在本地机器上的本地容器仓库中。

默认情况下，Podman 和 Skopeo 不会将镜像推送到不安全（HTTP）的仓库。你必须先使用现代的 v2 仓库配置格式，将系统配置为信任该本地仓库。

## 任务

- 配置你的系统，允许将镜像推送到位于 `localhost:5000` 的不安全本地仓库。
- 使用 `skopeo` 将 `nginx:latest` 镜像从 `docker.io` 复制到你的本地仓库。
- 从本地仓库将镜像拉取到 Podman 的本地存储中，以验证复制是否成功。

## 要求

- 编辑 `/etc/containers/registries.conf` 文件，使用 v2 格式将 `localhost:5000` 配置为不安全仓库。你需要 `sudo` 权限来执行此操作。
- 使用 `skopeo copy` 复制 `docker.io/library/nginx:latest` 镜像。
- 镜像在本地仓库中的目标位置应为 `localhost:5000/my-local-nginx:latest`。
- 复制完成后，使用 `podman pull` 获取 `localhost:5000/my-local-nginx:latest`。
