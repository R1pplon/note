# 暴露 Web 服务器端口并测试连接

在这一步中，你将学习如何将容器的端口暴露给宿主机，从而使服务可以从容器的隔离网络外部访问。我们的 Apache Web 服务器正在运行，但我们还不能从宿主机的浏览器或命令行访问它。我们将通过发布容器的端口来解决这个问题。

端口映射是在创建容器时定义的。因此，我们必须首先停止并移除在上一步中创建的 `web_server` 容器。不用担心网站内容，它在宿主机上的 `~/project/webapp_content` 目录中是安全的，因为我们使用了绑定挂载。

首先，停止容器：

```bash
podman stop web_server
```

```plaintext
web_server
```

接下来，移除已停止的容器：

```bash
podman rm web_server
```

```plaintext
web_server
```

现在，我们将再次运行 `web_server` 容器，但这次我们将添加 `-p`（或 `--publish`）标志，以将宿主机上的一个端口映射到容器中的一个端口。我们将宿主机上的端口 `8080` 映射到容器内的端口 `80`（默认的 HTTP 端口）。

```bash
podman run -d \
  --name web_server \
  --network webapp-network \
  -v $(pwd)/webapp_content:/usr/local/apache2/htdocs/:Z \
  -p 8080:80 \
  httpd:2.4
```

新的标志 `-p 8080:80` 告诉 Podman 将来自宿主机上端口 `8080` 的所有流量转发到 `web_server` 容器内的端口 `80`。

让我们使用 `podman ps` 验证容器是否正在运行以及端口是否已正确映射。

```bash
podman ps
```

注意 `web_server` 容器的 `PORTS` 列。它现在显示了从 `0.0.0.0:8080` 到 `80/tcp` 的映射，表明端口已成功暴露。

```plaintext
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                  NAMES
c5d4e3f2a1b6   httpd:2.4      "httpd-foreground"       10 seconds ago   Up 9 seconds    0.0.0.0:8080->80/tcp   web_server
a1b2c3d4e5f6   mariadb:10.6   "docker-entrypoint.s…"   25 minutes ago   Up 25 minutes   3306/tcp               mariadb_server
```

最后，让我们使用 `curl` 命令从我们的宿主机测试连接。这会将一个 HTTP 请求发送到 `localhost` 的端口 `8080`。

```bash
curl http://localhost:8080
```

你应该看到来自你的 `index.html` 文件的 HTML 内容作为输出，确认你的 Web 服务器现在可以从宿主机访问。

```plaintext
<h1>Welcome to My Web App</h1>
```

你已经成功地将你的容器化 Web 服务器暴露给宿主机，这是使应用程序可供用户使用的关键一步。
