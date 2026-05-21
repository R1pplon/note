# 在自定义端口上配置 httpd 并理解 SELinux 上下文

在这一步，你将学习如何配置 Apache Web 服务器 (`httpd`) 在一个非标准端口上运行，并理解 SELinux 如何管理端口访问。我们将使用端口 8081，并探索 SELinux 端口管理，即使服务在某些配置中成功启动。

必要的软件包 (`httpd`, `policycoreutils-python-utils` 和 `firewalld`) 已经在设置阶段安装完毕。`policycoreutils-python-utils` 软件包提供了 `semanage` 命令，你将在后面的步骤中使用它。

让我们从修改默认的 `httpd` 配置开始，使其监听一个非标准端口 `8081`。`httpd` 的主配置文件位于 `/etc/httpd/conf/httpd.conf`。我们将使用 `nano` 编辑器来更改监听端口。

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

在 `nano` 编辑器中，使用箭头键向下滚动并找到显示 `Listen 80` 的行。将此行更改为：

```
Listen 8081
```

要保存文件并退出 `nano`，请按 `Ctrl+X`，然后按 `Y` 确认更改，最后按 `Enter` 写入文件。

现在，配置已更改，让我们尝试启动 `httpd` 服务。在这个容器化环境中，`systemctl` 不可用。我们将直接启动 `httpd` 守护进程。

```bash
sudo /usr/sbin/httpd
```

你可能会看到关于服务器完全限定域名（fully qualified domain name）的警告消息，但这很正常，可以忽略。

```plaintext
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using fe80::216:3eff:fe02:1a1e%eth0. Set the 'ServerName' directive globally to suppress this message
```

让我们通过检查 `httpd` 进程来验证服务是否正在运行。

```bash
ps aux | grep httpd
```

你应该看到多个 `httpd` 进程正在运行，这表明 Web 服务器已成功启动。

```plaintext
root        4813  0.0  0.2  23364  7736 ?        Ss   09:32   0:00 /usr/sbin/httpd
apache      4814  0.0  0.1  23020  5092 ?        S    09:32   0:00 /usr/sbin/httpd
apache      4815  0.0  0.4 1441064 14620 ?       Sl   09:32   0:00 /usr/sbin/httpd
apache      4816  0.0  0.5 1441064 18736 ?       Sl   09:32   0:00 /usr/sbin/httpd
apache      4837  0.0  0.4 1572200 16872 ?       Sl   09:32   0:00 /usr/sbin/httpd
labex       4996  0.0  0.0   6408  2176 pts/3    S+   09:32   0:00 grep --color=auto httpd
```

我们还检查 `httpd` 错误日志，看看启动期间发生了什么。

```bash
sudo tail /var/log/httpd/error_log
```

你应该看到正常的启动消息，表明服务器正在正常运行。

```plaintext
[Tue Jun 17 09:32:46.374275 2025] [core:notice] [pid 4812:tid 4812] SELinux policy enabled; httpd running as context system_u:system_r:unconfined_service_t:s0
[Tue Jun 17 09:32:46.377265 2025] [suexec:notice] [pid 4812:tid 4812] AH01232: suEXEC mechanism enabled (wrapper: /usr/sbin/suexec)
[Tue Jun 17 09:32:46.394284 2025] [lbmethod_heartbeat:notice] [pid 4813:tid 4813] AH02282: No slotmem from mod_heartmonitor
[Tue Jun 17 09:32:46.399433 2025] [mpm_event:notice] [pid 4813:tid 4813] AH00489: Apache/2.4.62 (Red Hat Enterprise Linux) configured -- resuming normal operations
[Tue Jun 17 09:32:46.399458 2025] [core:notice] [pid 4813:tid 4813] AH00094: Command line: '/usr/sbin/httpd'
```

有趣的是，`httpd` 服务在没有任何 SELinux 问题的的情况下启动了。让我们检查审计日志中是否有任何 SELinux 拒绝。

```bash
sudo grep AVC /var/log/audit/audit.log | grep httpd
```

如果没有结果，这意味着 SELinux 没有阻止 httpd 服务绑定到端口 8081。这可能是因为：

1. 在某些配置中，端口 8081 默认可能已经允许用于 HTTP 服务
2. httpd 进程可能在未受限的上下文中运行
3. 端口 8081 可能已经在 SELinux 策略中定义

让我们检查当前的 SELinux 模式：

```bash
getenforce
```

你应该看到 SELinux 处于 "Enforcing" 模式，这意味着它正在积极地强制执行策略。httpd 成功启动的事实表明，端口 8081 可能已经具有适当的 SELinux 标签，或者服务正在未受限的上下文中运行，如日志消息所示。为了本次学习练习的目的，让我们继续下一步，在那里我们将探索 SELinux 端口管理并确保正确的配置。
