# 使用 firewall-cmd 在防火墙中开放自定义端口

在这一步，你将配置系统的防火墙，以允许外部连接到你的 Web 服务器上的自定义端口 `8081`。尽管由于 SELinux 策略的更改，`httpd` 服务现在运行正常，但管理网络流量规则的 `firewalld` 服务默认情况下很可能阻止在此非标准端口上的传入请求。

`firewalld` 软件包已在设置阶段安装完毕。但是，我们需要首先启动 firewalld 服务。让我们检查当前状态，并在需要时启动它。

```bash
sudo firewall-cmd --list-all
```

如果你看到“FirewallD is not running”，我们需要启动 firewalld 守护进程。在这个容器环境中，我们直接启动 `firewalld` 守护进程。末尾的 `&` 在后台运行该进程。

```bash
sudo /usr/sbin/firewalld &
```

稍等片刻，让服务初始化，然后验证它是否正在运行：

```bash
sudo firewall-cmd --list-all
```

现在你应该看到默认区域（`public`）的当前防火墙配置。

让我们使用 `curl` 从命令行测试对 Web 服务器的访问。此命令尝试连接到端口 `8081` 上的 `localhost`。

```bash
curl http://localhost:8081
```

你应该看到你的测试页面的 HTML 内容，这意味着 Web 服务器可以在本地访问。这是预期的，因为 firewalld 默认情况下通常允许 localhost 流量。

但是，对于外部访问和正确的安全配置，我们仍然需要为我们的自定义端口正确配置防火墙。虽然 localhost 连接通常无论防火墙规则如何都有效，但来自其他机器的外部连接如果没有正确的防火墙配置将被阻止。

首先，让我们检查默认区域（`public`）的当前规则：

```plaintext
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0 eth1
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

现在，添加一个新规则以允许端口 `8081` 上的 TCP 流量。在执行此命令之前，请确保 firewalld 正在运行。

- `--add-port=8081/tcp` 指定要打开的端口和协议。
- `--permanent` 确保规则在重启或防火墙重新加载后仍然存在。

```bash
sudo firewall-cmd --permanent --add-port=8081/tcp
```

如果你看到“FirewallD is not running”，请确保你在上一步中启动了 firewalld 守护进程，并稍等片刻让其初始化。

当 firewalld 正常运行时，该命令应返回 `success`。

```plaintext
success
```

永久规则在重新加载之前不会应用于活动的防火墙配置。让我们重新加载防火墙以应用我们的新规则。

```bash
sudo firewall-cmd --reload
```

此命令也应返回 `success`。

```plaintext
success
```

让我们通过再次列出规则来验证端口是否已打开。

```bash
sudo firewall-cmd --list-all
```

你现在应该在 `ports:` 部分看到 `8081/tcp`。

```plaintext
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0 eth1
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 8081/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

你已成功配置防火墙。最后一步是测试你是否可以访问 Web 服务器。
