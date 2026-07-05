# 检查 Jenkins 镜像

在此步骤中，你将识别正在运行的 Jenkins 容器所使用的 Docker 镜像。

运行以下命令：

```bash
docker inspect jenkins --format '{{.Config.Image}}'
```

这将打印出用于创建该容器的镜像名称。在此环境中，它应该显示一个 Jenkins 镜像：

```plaintext
jenkins/jenkins:latest
```

你也可以列出本地镜像：

```bash
docker images jenkins/jenkins
```

具体的镜像 ID 和创建时间可能会有所不同，但仓库名称应为 `jenkins/jenkins`：

```plaintext
REPOSITORY        TAG       IMAGE ID       CREATED       SIZE
jenkins/jenkins   latest    ...            ...           ...
```

这有助于你将浏览器中运行的服务与提供 Jenkins 的容器镜像关联起来。
