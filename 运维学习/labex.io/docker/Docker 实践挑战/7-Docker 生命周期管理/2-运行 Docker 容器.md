# 运行 Docker 容器

在这一步骤中，我们将基于上一步创建的镜像运行一个 Docker 容器。

## 目标

你的目标是以分离模式（detached mode）启动一个新容器，并将宿主机的 8080 端口转发到容器的 80 端口。该容器将运行我们在上一步中创建的 `my-web-server` 镜像。然后使用 Web 浏览器访问它。

## 结果示例

以下是你在本步骤结束时应能完成的效果示例：

1. 基于 `my-web-server` 镜像启动一个名为 `my-web` 的新容器。容器将宿主机端口 `8080` 转发到容器端口 `80`。

```
labex:hello-docker/ $ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS                                   NAMES
c33f6a28b5ac   my-web-server   "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   my-web
```

2. 使用 `curl` 命令查看容器提供的网页。

```
<!doctype html>
<html>
  <head>
    <title>Hello Docker!</title>
  </head>
  <body>
    <h1>Hello Docker!</h1>
  </body>
</html>
```

3. 查看网页中的内容。

![Docker 容器网页](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-docker-lifecycle-management/zh/../assets/challenge-docker-lifecycle-management-2-3.png)

## 要求

- 你的机器上必须已安装 Docker。
