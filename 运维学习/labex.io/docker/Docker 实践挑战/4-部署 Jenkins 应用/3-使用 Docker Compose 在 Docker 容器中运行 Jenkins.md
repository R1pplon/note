# 使用 Docker Compose 在 Docker 容器中运行 Jenkins

构建好 Jenkins 的 Docker 镜像后，现在可以使用 Docker Compose 在 Docker 容器中运行 Jenkins。

## 目标

使用 Docker Compose 在 Docker 容器中运行 Jenkins。

## 结果示例

1. 创建一个名为 `docker-compose.yml` 的文件，定义一个名为 `jenkins` 的服务。该服务基于 `jenkins-docker` 镜像。服务挂载了一个名为 `jenkins-data` 的卷，并将其映射到 `/var/jenkins_home` 目录。此外，它暴露了 `8080` 和 `50000` 端口用于通信。最后，该服务被限制在 `jenkins` 网络中。

2. 使用 Docker Compose 在 Docker 容器中运行 Jenkins。

```
labex:project/ $ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                                                                      NAMES
b56bee607cec   jenkins-docker   "/usr/bin/tini -- /u…"   7 seconds ago   Up 6 seconds   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 0.0.0.0:50000->50000/tcp, :::50000->50000/tcp   jenkins-docker
```

3. 打开 Web 浏览器并访问 `http://localhost:8080` 以进入 Jenkins Web 界面。

![Jenkins Web 界面访问](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-deploying-jenkins-application/zh/../assets/challenge-deploying-jenkins-application-3-3.png)
