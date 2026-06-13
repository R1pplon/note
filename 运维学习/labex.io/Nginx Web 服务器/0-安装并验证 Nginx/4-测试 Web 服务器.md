# 测试 Web 服务器

现在服务器正在运行且端口已打开，让我们通过发送请求来验证它是否正常工作。

由于我们使用的是没有 Chrome 或 Firefox 等图形化浏览器的命令行界面，我们将使用名为 `curl` 的工具。`curl` 允许你直接在终端中从 URL 获取数据。

运行以下命令来访问你自己的服务器（`localhost`）：

```bash
curl localhost
```

你应该会看到默认 Nginx 欢迎页面的原始 HTML 代码。

**输出示例：**

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
...
```

如果你看到了这条「Welcome to nginx!」消息，恭喜你！你的 Web 服务器已完全正常运行。

至此，你已经验证了整个链路：软件包已安装、服务正在运行、端口正在监听，并且 HTTP 响应已正确返回。
