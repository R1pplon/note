# 配置 Nginx 反向代理

在这一步中，你将修改 Nginx 配置文件，告知它关于我们 Python 应用的信息。

我们需要使用 `sudo` 和 `nano` 文本编辑器来编辑默认配置文件。

```bash
sudo nano /etc/nginx/sites-enabled/default
```

在 `server { ... }` 块中向下滚动。你会看到一个现有的 `location / { ... }` 块。我们需要在它下方添加一个新的 `location /app` 块。

将以下代码添加到文件中（注意花括号 `{ }` 和分号 `;`）：

```nginx
    location /app {
        proxy_pass http://127.0.0.1:8000;
    }
```

**这段代码的作用是什么？**

- `location /app`：告诉 Nginx 监听以 `/app` 开头的请求。
- `proxy_pass ...`：充当呼叫转接按钮。它告诉 Nginx：「当你收到对 `/app` 的请求时，将其转发到端口 8000 上的 `localhost` (127.0.0.1)。」

这种转发正是反向代理的精髓。客户端与 Nginx 对话，但 Nginx 代表用户成为了后端服务器的客户端。

你的文件结构应该类似于这样：

```nginx
server {
    listen 80 default_server;
    ...
    location / {
        try_files $uri $uri/ =404;
    }

    location /app {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

按 `Ctrl+O` 然后按 `Enter` 保存文件，按 `Ctrl+X` 退出 `nano`。
