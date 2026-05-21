# 理解 Docker 容器执行

在这一步中，你将学习 Docker 容器执行的基础知识以及如何停止正在运行的容器。

1. 首先，让我们通过在你的终端中执行以下命令，使用 `nginx` 镜像运行一个简单的 Docker 容器：

   ```bash
   docker run -d --name my-nginx nginx
   ```

2. 现在，要停止正在运行的容器，请使用以下命令：

   ```bash
   docker stop my-nginx
   ```

3. 执行 `docker stop` 命令后，容器将被停止，你可以通过运行以下命令来验证这一点：

   ```bash
   docker ps -a
   ```

   你应该会看到停止的容器被列出，状态为 "Exited"。
