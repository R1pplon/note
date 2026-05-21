# 手动发送自定义 Syslog 消息

在这一步，你将学习如何使用 `logger` 命令手动发送自定义 syslog 消息。这是一种有用的技术，用于测试 `rsyslog` 服务的配置，或者将自定义消息注入系统日志中，以用于调试或监控目的。

`logger` 命令将消息发送到 `rsyslog` 服务。默认情况下，它使用 `user` facility 和 `notice` priority（`user.notice`）发送消息，除非使用 `-p` 选项另行指定。

让我们从向默认日志位置（通常是 `/var/log/messages`）发送一个简单的测试消息开始。

```bash
logger "This is a test message from labex."
```

执行该命令后，你可以通过检查 `/var/log/messages` 文件来验证消息是否已记录。使用 `tail` 查看文件的最后几行。请记住使用 `sudo` 访问日志文件。

```bash
sudo tail /var/log/messages
```

你应该在输出的末尾看到你的自定义消息，类似于这样：

```plaintext
...
Dec 16 10:30:00 host labex: This is a test message from labex.
```

现在，让我们尝试发送一条具有特定 facility 和 priority 的消息。回想一下上一步，syslog 消息按 facility 和 priority 进行分类。例如，`local7.notice` 意味着该消息将使用 `local7` facility 和 `notice` priority 进行记录。`local7` facility 经常用于自定义应用程序或启动消息，并且通常通过 `rsyslog` 配置定向到 `/var/log/boot.log`。

要将消息发送到 `rsyslog` 服务，以便将其记录在 `/var/log/boot.log` 日志文件中，请执行以下 `logger` 命令：

```bash
logger -p local7.notice "Log entry created by labex for boot.log"
```

现在，验证此消息是否已写入 `/var/log/boot.log`。使用 `sudo` 访问日志文件。

```bash
sudo tail /var/log/boot.log
```

你应该在输出中看到你的自定义消息：

```plaintext
...
Dec 16 10:31:00 host labex: Log entry created by labex for boot.log
```

这演示了如何通过指定 facility 和 priority 来控制自定义消息的记录位置。此功能对于测试 `rsyslog` 配置以及将特定事件注入系统日志非常有用。
