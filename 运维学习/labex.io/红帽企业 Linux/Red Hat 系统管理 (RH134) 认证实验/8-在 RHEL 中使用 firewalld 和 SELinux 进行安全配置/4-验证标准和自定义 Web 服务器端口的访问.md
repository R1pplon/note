# 验证标准和自定义 Web 服务器端口的访问

在最后一步，你将验证你所做的所有更改是否确保你的 Web 服务器已正确配置，以便进行本地和外部访问。你已经配置了 SELinux 策略并在防火墙中打开了必要的端口。现在，你将执行全面的测试以确认配置。

首先，让我们创建一个简单的测试页面，以便当我们连接时，我们得到一个自定义消息。`httpd` 的默认文档根目录是 `/var/www/html`。我们将在该目录中创建一个 `index.html` 文件。你需要 `sudo` 权限才能写入此位置。

```bash
echo "Success! Web server on custom port 8081 is working." | sudo tee /var/www/html/index.html
```

此命令将成功消息放入 `index.html` 文件中。这里使用 `tee` 命令是因为它允许我们使用管道写入需要 `sudo` 权限的文件。你应该看到消息回显到终端作为确认。

```plaintext
Success! Web server on custom port 8081 is working.
```

为了完成练习，让我们通过尝试访问标准 HTTP 端口 `80` 来演示对比。由于我们的服务器配置为仅侦听 `8081`，因此此请求应该失败。

```bash
curl http://localhost:80
```

正如预期的那样，连接被拒绝，因为没有服务在侦听该端口。

```plaintext
curl: (7) Failed to connect to localhost port 80: Connection refused
```

这证实了你的服务器仅在你配置的自定义端口上运行。通过这个实验，你已经学习了 RHEL 上服务的关键故障排除工作流程：

1. 检查服务状态和日志。
2. 调查审计日志中的 SELinux 拒绝。
3. 使用 `semanage` 纠正 SELinux 策略。
4. 使用 `firewall-cmd` 配置防火墙规则。
5. 验证连接。
