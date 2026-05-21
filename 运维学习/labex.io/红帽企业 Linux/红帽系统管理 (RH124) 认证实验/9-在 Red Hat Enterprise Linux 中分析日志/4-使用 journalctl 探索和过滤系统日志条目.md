# 使用 journalctl 探索和过滤系统日志条目

在这一步，你将学习如何使用 `journalctl` 命令探索和过滤系统日志条目。正如你所了解的，`systemd-journald` 服务将日志数据存储在一个结构化的、索引的二进制文件中，称为 `journal`。`journalctl` 命令是你与此 journal 交互的主要工具。

让我们从查看 journal 中的所有消息开始。当你运行 `journalctl` 没有任何选项时，它会显示所有可用的日志条目，从最旧的开始。由于你以 `labex` 的身份登录并具有 `sudo` 权限，你将完全访问 journal。

```bash
journalctl
```

你将看到大量的输出。按 `q` 退出 `journalctl` 查看器。请注意，`journalctl` 突出显示重要的日志消息：`notice` 或 `warning` priority 的消息以粗体文本显示，而 `error` priority 或更高的消息以红色文本显示。

现在，让我们探索一些常见的 `journalctl` 选项，用于过滤和查看特定条目。

**1. 查看最后 N 个日志条目（`-n` 选项）：**
默认情况下，`journalctl -n` 显示最后 10 个日志条目。你可以指定一个不同的数字，例如，最后 5 个条目：

```bash
journalctl -n 5
```

你应该看到最近的 5 个日志条目。

**2. 跟踪新的 journal 条目（`-f` 选项）：**
类似于 `tail -f` 命令，`journalctl -f` 选项输出系统 journal 的最后 10 行，并继续输出新添加的 journal 条目。这对于实时监控非常有用。

```bash
journalctl -f
```

要退出此连续输出，请按 **Ctrl+C**。

**3. 按 priority 过滤（`-p` 选项）：**
你可以按 journal 条目的 priority 级别过滤输出。`journalctl -p` 选项显示指定 priority 级别（按名称或数字）或更高的条目。priority 级别，按升序排列，分别是 `debug`、`info`、`notice`、`warning`、`err`、`crit`、`alert` 和 `emerg`。

让我们列出 `err` priority 或更高的 journal 条目：

```bash
journalctl -p err
```

你可能会看到与各种系统组件相关的错误消息。

**4. 按 systemd unit 过滤（`-u` 选项）：**
你可以通过使用 `journalctl -u` 选项和 unit 名称来显示指定 `systemd` unit 的消息。例如，要专门查看 `sshd` 服务的日志：

```bash
journalctl -u sshd.service
```

这将显示与 SSH 守护进程相关的所有日志条目。

**5. 按时间范围过滤（`--since` 和 `--until` 选项）：**
当查找特定事件时，你可以将输出限制在特定的时间范围内。`--since` 和 `--until` 选项都接受 "YYYY-MM-DD hh:mm:ss" 格式的时间参数。如果参数中有空格，则需要使用双引号。你还可以使用相对术语，如 `yesterday`、`today`、`tomorrow`，或时间段，如 `"-1 hour"`。

让我们查看从今天开始的所有 journal 条目：

```bash
journalctl --since today
```

现在，让我们查看过去一小时的条目：

```bash
journalctl --since "-1 hour"
```

**6. 查看详细输出（`-o verbose` 选项）：**
要查看有关每个日志条目的其他详细信息，包括内部 journal 字段，你可以使用 `-o verbose` 选项。这对于高级故障排除可能很有帮助。

```bash
journalctl -n 1 -o verbose
```

这将显示带有所有详细信息的最后一个日志条目。注意诸如 `_COMM`（命令名称）、`_EXE`（可执行文件的路径）、`_PID`（进程 ID）、`_UID`（用户 ID）和 `_SYSTEMD_UNIT`（systemd unit）之类的字段。这些字段可用于更精确的过滤。

例如，要从具有已知 PID 的特定 `sshd` 进程中查找条目（你可以从之前的 `journalctl -u sshd.service` 输出中获取 PID）：

```bash
journalctl _SYSTEMD_UNIT=sshd.service _PID=<PID_NUMBER>
```

将 `<PID_NUMBER>` 替换为你从 `sshd` 条目中观察到的实际 PID。例如，如果你看到 `sshd[1433]`，你将使用 `_PID=1433`。

```bash
journalctl _SYSTEMD_UNIT=sshd.service _PID=1433
```

此命令演示了如何组合多个过滤器以缩小你在 journal 中的搜索范围。
