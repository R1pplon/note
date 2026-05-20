# 将 Docker 镜像存储到 Docker Hub

在这一步中，你将学习如何将 Docker 镜像存储到 Docker Hub，这是一个用于存储和共享 Docker 镜像的云端仓库。Docker Hub 允许开发者共享、分发和协作 Docker 镜像，并自动化构建和部署过程。

## 目标

本步骤的目标是将一个 Docker 镜像存储到 Docker Hub，并使用它来部署一个 Web 应用程序。

## 要求

- 一个 Docker Hub 账户（`https://hub.docker.com`）。
- 上一步构建好的 Docker 镜像。

## 结果示例

1. 使用 `docker login` 命令登录到 Docker Hub。

你需要有一个 Docker Hub 账户。如果没有，可以在 `<https://hub.docker>` 创建一个。

```
labex:~/ $ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: yaww
Password:
WARNING! Your password will be stored unencrypted in /home/labex/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

2. 使用 `docker tag` 命令为 Docker 镜像添加你的 Docker Hub 账户标签。

```
labex:~/ $ docker images | grep /my-app
yaww/my-app                   v1         e310a0cb9319   22 minutes ago   136MB
```

3. 使用 `docker push` 命令将 Docker 镜像推送到 Docker Hub。

![docker push 命令示例](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-package-and-run-applications/zh/../assets/challenge-package-and-run-applications-3-3.png)

4. 使用 `docker rmi` 命令删除 `<your_username>/my-app` 镜像。

```
Untagged: yaww/my-app:v1
Untagged: yaww/my-app@sha256:887b6e11c284951c68c5081eb7a4ec5d37ddcf693381e0bc0de9965076fa12f5
labex:~/ $ docker images | grep my-app
my-app                        latest     e310a0cb9319   28 minutes ago   136MB
labex:~/ $ docker images | grep /my-app
```

5. 使用 `docker pull` 命令从 Docker Hub 拉取 `<your_username>/my-app` 镜像。

```
v1: Pulling from yaww/my-app
Digest: sha256:887b6e11c284951c68c5081eb7a4ec5d37ddcf693381e0bc0de9965076fa12f5
Status: Downloaded newer image for yaww/my-app:v1
docker.io/yaww/my-app:v1
labex:~/ $ docker images | grep /my-app
yaww/my-app                   v1         e310a0cb9319   29 minutes ago   136MB
```

6. 使用 `docker run` 命令使用拉取的镜像启动一个 Docker 容器。并将容器端口映射到宿主机相同的端口。

7. 在你的 Web 浏览器中打开 `http://localhost:80` 访问 Web 应用程序。

![在浏览器中运行的 Web 应用程序](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/docker/challenge-package-and-run-applications/zh/../assets/challenge-package-and-run-applications-3-7.png)
