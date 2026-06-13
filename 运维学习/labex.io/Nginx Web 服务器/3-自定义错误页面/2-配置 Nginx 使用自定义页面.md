# 配置 Nginx 使用自定义页面

现在我们已经有了「指示牌」（HTML 文件），需要告诉 Nginx 何时使用它。我们通过编辑主服务器配置文件来实现这一点。

使用 `nano` 打开默认配置文件：

```bash
sudo nano /etc/nginx/sites-enabled/default
```

我们需要在 `server { ... }` 块内添加两段代码。插入它们的最佳位置是在 `location / { ... }` 块之前。

添加以下内容：

```nginx
    error_page 404 /custom_404.html;

    location = /custom_404.html {
        root /var/www/html;
        internal;
    }
```

**配置解析：**

1. `error_page 404 /custom_404.html;`：这告诉 Nginx：「如果发生 404 错误，请在内部将请求重定向到 `/custom_404.html`。」
2. `location = /custom_404.html { ... }`：这告诉 Nginx 在哪里可以找到该特定文件。
3. `internal;`：这是一种安全措施。它防止用户直接访问错误页面（例如，通过输入 `http://yoursite.com/custom_404.html`）。只有在确实发生错误时，他们才能看到该页面。

`internal` 这个词非常重要，因为它表明 Nginx 可以做出不同于用户直接请求的内部路由决策。

保存文件（`Ctrl + O`，`Enter`）并退出（`Ctrl + X`）。

在重启服务器之前，先检查是否有拼写错误：

```bash
sudo nginx -t
```

如果测试成功，重新加载 Nginx 以应用新规则：

```bash
sudo service nginx reload
```
