# 启动 Nginx 容器

在这一步中，你将部署一个 Nginx 容器，为你的舰队提供一个安全可靠的通信平台。

## 任务

- 从 Docker Hub 拉取 Nginx Docker 镜像。
- 从 Nginx 镜像运行一个 Docker 容器。
- 验证 Nginx 容器是否正在运行且可访问。

## 要求

- 使用 Docker Hub 上可用的 Nginx Docker 镜像。
- 使用命令行执行 Docker 命令。

## 示例

成功完成此步骤后，Nginx 容器应该正在运行，并监听在 80 端口，以处理传入的通信。你可以使用以下命令进行检查：

```bash
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
3cb4e460696f nginx "/docker-entrypoint.…" 11 seconds ago Up 10 seconds 0.0.0.0:80- :::80- pensive_wiles > 80/tcp, > 80/tcp
```
