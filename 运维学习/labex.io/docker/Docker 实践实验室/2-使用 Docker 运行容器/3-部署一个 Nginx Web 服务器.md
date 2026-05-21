# 部署一个 Nginx Web 服务器

在这一步中，你将使用 Docker 容器部署一个 Nginx Web 服务器。在我们的维多利亚时代场景中，这相当于为向访客展示你最珍贵的古董（Web 内容）搭建一个精致的展示柜（Nginx）。

1. 从 Docker Hub 拉取 Nginx 镜像：

   ```bash
   sudo docker pull nginx
   ```

   此命令从 Docker Hub 下载官方的 Nginx 镜像。Nginx 是一个流行的 Web 服务器，可以提供 HTML 内容。这就相当于为你的古董藏品购置了一个精美的展示柜。

2. 以分离模式运行一个 Nginx 容器，将主机的 80 端口映射到容器的 80 端口：

   ```bash
   sudo docker run -d -p 80:80 nginx
   ```

   让我们来拆解一下这个命令：
   - `run`：创建并启动一个新容器
   - `-d`：以分离模式（在后台）运行容器
   - `-p 80:80`：将你主机的 80 端口映射到容器内的 80 端口，使 Web 流量能够访问 Nginx 服务器
   - `nginx`：指定要使用的镜像

   这就好比把你的展示柜放置在展厅里，并确保访客能够看到你的古董。

3. 验证你的 Nginx 容器是否正在运行：

   ```bash
   sudo docker ps
   ```

   此命令会显示所有正在运行的容器。你应该能在列表中看到你的 Nginx 容器，这表明它正在活跃运行。

4. 从实验顶部菜单添加一个 Web 服务映射，并将端口映射设置为 80。如果 Nginx Web 服务器成功运行，实验环境将自动导航到服务器实验中的一个随机 URL，你应该会看到默认的 Nginx 欢迎页面。

5. 可选操作：如果你想手动在浏览器中访问 Nginx 服务器，可以导航到 `http://localhost` 或你机器的 IP 地址。

6. 如果你遇到任何问题，可以查看容器日志：

   ```bash
   sudo docker logs <container_id>
   ```

   将 `<container_id>` 替换为你的 Nginx 容器的 ID，你可以从 `docker ps` 命令的输出中找到该 ID。
