# 使用 semanage 理解和管理 SELinux 端口标签

在这一步，你将学习如何使用 `semanage` 命令管理 SELinux 端口标签。即使 `httpd` 服务当前正在端口 8081 上运行，理解如何正确配置 SELinux 端口策略以确保安全性和合规性也很重要。你将探索当前的端口配置，并学习如何显式地将正确的 SELinux 类型分配给自定义端口。

首先，你需要找到 Web 服务器端口的正确 SELinux 类型。`semanage port -l` 命令列出 SELinux 知道的所有端口定义。我们可以将此输出通过管道传递给 `grep` 以查找与 `http` 相关的类型。

```bash
sudo semanage port -l | grep http
```

输出显示了几个端口类型。与标准 Web 服务器最相关的是 `http_port_t`。

```plaintext
http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
http_cache_port_t              udp      3130
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988
pegasus_https_port_t           tcp      5989
```

如你所见，`http_port_t` 被分配给标准 HTTP/HTTPS 端口，如 `80` 和 `443`。SELinux 策略允许具有 `httpd_t` 类型（我们的 Web 服务器）的进程绑定到任何标记为 `http_port_t` 的端口。让我们检查端口 `8081` 是否已在此列表中。

请注意，端口 `8081` 当前未在 `http_port_t` 下列出。但是，在某些 RHEL 配置中，此端口可能已在 SELinux 策略中定义。让我们尝试使用 `semanage port -a` 命令显式添加它以实现正确的 SELinux 合规性。

- `-a` 选项表示“添加（add）”。
- `-t http_port_t` 选项指定要分配的类型。
- `-p tcp` 选项指定协议。

```bash
sudo semanage port -a -t http_port_t -p tcp 8081
```

你可能会看到一条消息，指示“Port tcp/8081 already defined, modifying instead”，这意味着该端口已经配置。这解释了为什么 httpd 在上一步中成功启动。要验证当前配置，请再次列出 `http_port_t` 定义。

```bash
sudo semanage port -l | grep '^http_port_t'
```

你现在应该看到端口 `8081` 包含在列表中。

```plaintext
http_port_t                    tcp      8081, 80, 81, 443, 488, 8008, 8009, 8443, 9000
```

通过显式更新 SELinux 策略，端口 `8081` 现在被正式确认为 HTTP 端口。`httpd` 服务应该继续运行，没有任何问题，并且你已经确保了正确的 SELinux 合规性。

让我们验证该进程是否仍在运行：

```bash
ps aux | grep httpd
```

你应该继续看到多个 `httpd` 进程，这表明 Web 服务器正在使用正确的 SELinux 端口标签成功运行。

```plaintext
root        4813  0.0  0.2  23364  7736 ?        Ss   09:32   0:00 /usr/sbin/httpd
apache      4814  0.0  0.1  23020  5092 ?        S    09:32   0:00 /usr/sbin/httpd
apache      4815  0.0  0.4 1441064 14620 ?       Sl   09:32   0:00 /usr/sbin/httpd
apache      4816  0.0  0.5 1441064 18736 ?       Sl   09:32   0:00 /usr/sbin/httpd
apache      4837  0.0  0.4 1572200 16872 ?       Sl   09:32   0:00 /usr/sbin/httpd
labex       5215  0.0  0.0   6408  2176 pts/3    S+   09:33   0:00 grep --color=auto httpd
```

你已成功配置 SELinux 策略，以显式允许 `httpd` 服务在端口 8081 上运行，从而确保了正确的安全合规性。
