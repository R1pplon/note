# 构建带有依赖项的 Docker 镜像

既然你已经熟悉了基础知识，让我们来处理一个更真实的场景。在这一步中，你将为一个使用 Flask Web 框架的 Python 应用程序创建一个 Docker 镜像。这将向你介绍在 Docker 镜像中管理依赖项的概念。

## 任务

1. 进入 `/home/labex/project` 下的 `flask-app` 目录。
2. 打开该目录中的 `Dockerfile` 并添加安装依赖项所需的指令。
3. 检查 `requirements.txt` 文件以了解依赖项。
4. 使用 `docker build` 命令构建一个名为 `flask-app` 的 Docker 镜像。

## 要求

- 在 `/home/labex/project/flask-app` 目录中执行所有操作。
- 在 Dockerfile 中使用 `python:3.7-alpine` 作为基础镜像。
- 确保镜像中安装了 `requirements.txt` 中列出的依赖项。
- Dockerfile 应复制 `requirements.txt` 文件并安装依赖项。

## 示例

完成此步骤后，你在列出 Docker 镜像时应该能看到 `flask-app` 镜像：

```
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
flask-app    latest    abcdef123456   2 minutes ago   55.8MB
```

干得漂亮！你现在已经创建了一个包含外部依赖项的 Docker 镜像。这是现实应用中的常见场景。在下一步中，我们将探索如何在 Docker 镜像中使用环境变量，使其更加灵活和可配置。
