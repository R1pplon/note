# 在 Dockerfile 中使用 ENTRYPOINT

在这一步中，我们将学习如何在 Dockerfile 中使用 `ENTRYPOINT` 指令，并使用一个不同的端口（9100）。

1. 在 WebIDE 中再次打开 `Dockerfile`。

2. 将 `Dockerfile` 的内容修改为：

```Dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/
COPY start.sh /start.sh
RUN chmod +x /start.sh
ENTRYPOINT ["/start.sh"]
```

这个 Dockerfile 移除了环境变量和 `sed` 命令。取而代之的是，我们将使用 `ENTRYPOINT` 脚本在运行时处理配置。这使得我们的镜像更加灵活。

3. 在同一目录下创建一个名为 `start.sh` 的新文件，内容如下：

```bash
#!/bin/bash
# 如果未设置 NGINX_PORT，则设置默认端口
export NGINX_PORT=${NGINX_PORT:-9100}
# 替换 nginx 配置中的端口
sed -i "s/listen[[:space:]]*80;/listen $NGINX_PORT;/g" /etc/nginx/conf.d/default.conf
echo "Starting Nginx on port $NGINX_PORT"
nginx -g 'daemon off;'
```

此脚本设置了一个默认端口，在容器启动时修改 Nginx 配置，打印一条显示 Nginx 运行端口的消息，然后启动 Nginx。

4. 在 WebIDE 中保存这两个文件。

5. 在 WebIDE 终端中，使用新标签重新构建 Docker 镜像：

```bash
docker build -t my-nginx-entrypoint .
```

6. 基于新镜像运行容器。我们将通过环境变量将端口设置为 9100。

```bash
docker run -d -p 9100:9100 -e NGINX_PORT=9100 --name entrypoint-container my-nginx-entrypoint
```

7. 检查容器日志以查看启动消息：

```bash
docker logs entrypoint-container
```

你应该在输出中看到消息「Starting Nginx on port 9100」。

8. 验证 Web 服务器是否在新端口上正常运行：

```bash
curl http://localhost:9100
```

你应该会在终端中看到 `index.html` 文件的 HTML 内容。
