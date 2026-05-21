# 构建 Docker 镜像

在此步骤中，你将使用 Dockerfile 为航天器的软件系统构建自定义 Docker 镜像。

1. 打开终端并导航到 `~/project` 目录。
2. 运行以下命令构建 Docker 镜像：

```bash
docker build -t spaceship-software .
```

3. 验证 Docker 镜像是否已成功构建并标记为 `spaceship-software`。
