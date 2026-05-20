# 使用 Docker Compose 进行容器编排

在这一步中，你将学习如何使用 Docker Compose 在不同网络上编排多个容器。

## 目标

完成此步骤后，你应该能够使用 Docker Compose 启动多个可以相互通信的容器。

## 结果示例

1. 下载 `docker-compose`。

```bash
docker-compose version
```

```plaintext
Docker Compose version v2.6.1
```

2. 在 `~/project` 中创建一个名为 `myapp` 的新目录并进入该目录。
3. 创建一个包含以下内容的 `docker-compose.yml` 文件：

- 文件的第一行指定所使用的 Docker Compose 版本，格式为 `version: 'x'`。
- 在 `services` 部分下，定义每个服务。在此示例中，此 Compose 文件定义了三个服务：`web`、`app` 和 `db`。
  - `web` 服务使用 `nginx` 镜像，并将容器内部的 80 端口映射到主机上的 8000 端口。
  - `app` 服务使用其自己的 `tiangolo/uwsgi-nginx-flask:python3.8` 镜像，并连接到 `front_net` 和 `back_net` 自定义网络。
  - `db` 服务使用 `postgres` 镜像，并将 `POSTGRES_PASSWORD` 环境变量设置为 `mypassword`。
- 最后一节定义了 `front_net` 和 `back_net` 自定义网络。

4. 启动容器。

![Starting Docker containers](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-single-host-container-interconnection/zh/../assets/challenge-single-host-container-interconnection-3-4.png)

5. 使用 `curl` 命令验证 `web` 容器是否可访问。

![Web container access verification](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-single-host-container-interconnection/zh/../assets/challenge-single-host-container-interconnection-3-5.png)
