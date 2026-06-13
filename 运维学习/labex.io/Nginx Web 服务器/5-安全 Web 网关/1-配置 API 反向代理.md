# 配置 API 反向代理

开发团队有一个运行在 `7000` 端口的内部服务，它充当后端 API。目前，该服务无法从外部访问。

你的第一个任务是配置 Nginx 作为 **反向代理**。当用户请求 `/api` 时，Nginx 应将该请求转发到 `http://127.0.0.1:7000`。

## 任务

1. 编辑默认的 Nginx 配置文件 (`/etc/nginx/sites-enabled/default`)。
2. 在 `server` 块内，为路径 `/api` 创建一个新的 `location` 块。
3. 使用 `proxy_pass` 指令将请求转发到 7000 端口的本地后端服务。
4. 重新加载 Nginx 以应用更改。
5. 验证 `curl http://localhost/api` 是否返回了后端响应。

## 要求

- 编辑 `/etc/nginx/sites-enabled/default`。
- 创建一个 `location /api { ... }` 块。
- 将流量转发到 `http://127.0.0.1:7000`。

## 示例

完成任务后，你可以运行以下命令来验证反向代理是否正常工作：

```bash
curl http://localhost/api
```

你应该看到以下输出：

```plain
Backend API Response: Success
```

## 提示

<details>
<summary>提示：Proxy Pass 语法</summary>

回顾一下反向代理实验的内容：

```nginx
location /path {
    proxy_pass http://127.0.0.1:PORT;
}
```

别忘了末尾的分号 `;`！

</details>
