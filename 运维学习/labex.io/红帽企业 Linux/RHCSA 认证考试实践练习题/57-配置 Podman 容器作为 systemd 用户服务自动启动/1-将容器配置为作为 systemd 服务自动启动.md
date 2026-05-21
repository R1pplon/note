# 将容器配置为作为 systemd 服务自动启动

在本挑战中，你将配置一个容器，使其作为 systemd 服务自动启动。这对于确保容器化应用始终保持运行并可访问非常有用。

## 任务

你的目标是在 Podman 容器中运行一个 Nginx Web 服务器，并将其配置为在系统启动时自动启动。

- 基于 `nginx:latest` 镜像创建并运行一个 `podman` 容器。
- 为该容器生成一个 `systemd` 用户服务文件。
- 为 `labex` 用户启用该服务，使其自动启动。
- 为 `labex` 用户启用驻留（Lingering）功能，以确保即使在没有活动登录会话的情况下，服务也能在开机时启动。
- 验证容器是否正在运行且 Nginx 服务可以访问。

## 要求

- 必须使用 `podman` 执行所有容器管理任务。
- 容器镜像必须使用 `docker.io/library/nginx:latest`。
- 容器名称必须为 `my-nginx`。
- 容器必须将主机的 `8080` 端口映射到容器的 `80` 端口。
- 必须使用 `podman generate systemd` 生成 `systemd` 服务文件。
- 生成的服务文件必须放置在 `~/.config/systemd/user/container-my-nginx.service`。
- 必须使用 `loginctl` 为 `labex` 用户启用驻留功能。

## 示例

完成挑战后，你应该能够通过运行 `curl http://localhost:8080` 访问 Nginx 欢迎页面。输出结果应如下所示：

```plaintext
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
