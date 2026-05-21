# 设置 Docker 日志记录

在这一步中，你将为一个 Docker 容器设置日志记录并查看日志。

1. 拉取官方的 `nginx` Docker 镜像：

   ```bash
   docker pull nginx
   ```

2. 使用 `nginx` 镜像创建一个名为 `magical-nginx` 的 Docker 容器：

   ```bash
   docker run -d -p 80:80 --name magical-nginx nginx
   ```

3. 查看 `magical-nginx` 容器的日志：

   ```bash
   docker logs magical-nginx
   ```
