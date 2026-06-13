# 配置 Nginx 身份验证

现在我们需要告诉「门卫」（Nginx）在允许任何人进入 `/secure` 目录之前，先检查「访客名单」（密码文件）。

打开默认配置文件：

```bash
sudo nano /etc/nginx/sites-enabled/default
```

我们需要为我们的受保护文件夹专门添加一个新的 `location` 块。在 `server { ... }` 块内向下滚动，并添加以下行：

```nginx
    location /secure {
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
```

**这些代码的作用是什么？**

1. `location /secure { ... }`：仅当用户访问 `http://your-site/secure` 时应用这些规则。
2. `auth_basic "Restricted Content";`：开启身份验证。引号内的文本是登录弹窗中显示的消息。
3. `auth_basic_user_file ...`：告诉 Nginx 在哪里查找我们在步骤 2 中创建的文件。

这是 Nginx 基于路径行为的另一个示例。不同的 URL 位置可以有不同的安全规则，这让你能够仅保护网站中需要限制的部分。

保存文件（`Ctrl+O`，`Enter`）并退出（`Ctrl+X`）。

在重新加载之前，请务必检查是否有拼写错误：

```bash
sudo nginx -t
```

如果测试成功，重新加载 Nginx 以激活安全设置：

```bash
sudo service nginx reload
```
