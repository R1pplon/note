# 构建带有环境变量的 Docker 镜像

配置管理是容器化应用程序的一个关键方面。在这一步中，你将创建一个 Docker 镜像，该镜像使用环境变量来设置 Flask 应用程序的端口号。这将演示如何让你的 Docker 镜像更具动态性，并能适应不同的环境。

## 任务

1. 进入 `/home/labex/project` 下的 `flask-app-env` 目录。
2. 打开该目录中的 `Dockerfile` 并添加设置环境变量的指令。
3. 修改 `app.py` 文件，使其使用环境变量作为端口号。
4. 使用 `docker build` 命令构建一个名为 `flask-app-env` 的 Docker 镜像。

## 要求

- 在 `/home/labex/project/flask-app-env` 目录中执行所有操作。
- 在 Dockerfile 中使用 `python:3.7-alpine` 作为基础镜像。
- 在 Dockerfile 中设置一个环境变量 `PORT`，默认值为 5000。
- 修改 Flask 应用程序，使其使用 `PORT` 环境变量来设置监听端口。
- 确保安装了 `requirements.txt` 中列出的所有必要 Python 软件包。

## 示例

完成此步骤并运行容器后，你应该能够访问该应用程序：

```
$ docker run -d -p 5000:5000 flask-app-env
$ curl http://127.0.0.1:5000
Hello, World!
```

做得好！你现在创建了一个更灵活的 Docker 镜像，它可以通过环境变量适应不同的环境。这是创建可重用和可配置容器镜像的一种强大技术。在最后一步中，我们将探索一项高级 Docker 特性：多阶段构建。
