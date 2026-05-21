# 管理用户 'crontab' 条目

在这一步中，你将学习更高级的管理用户 `crontab` 条目的技术，包括编辑现有作业、添加多个作业以及理解特殊的 `cron` 字符串。有效的 `crontab` 管理对于自动化例行任务至关重要。

我们将继续在本地系统上工作，以探索高级 crontab 管理技术。

让我们从添加一个新的 `cron` 作业开始。此作业将每两分钟将 "Hello from cron!" 追加到 `~/cron_messages.txt`。

打开你的 `crontab` 进行编辑：

```bash
crontab -e
```

在 vim 中：

1. 按 `i` 进入插入模式
2. 将以下行添加到 `crontab` 文件中：

```
*/2 * * * * echo "Hello from cron!" >> ~/cron_messages.txt
```

3. 按 `Esc` 退出插入模式
4. 键入 `:wq` 并按 `Enter` 保存并退出

验证条目是否已添加：

```bash
crontab -l
```

你应该看到新添加的行。

现在，让我们添加另一个 `cron` 作业，该作业每天早上 08:00 运行。此作业将把你的主目录的磁盘使用情况记录到 `~/disk_usage.log`。

再次打开你的 `crontab` 进行编辑：

```bash
crontab -e
```

在 vim 中：

1. 按 `i` 进入插入模式
2. 在上一行下方添加以下行：

```
0 8 * * * du -sh ~ >> ~/disk_usage.log
```

3. 按 `Esc` 退出插入模式
4. 键入 `:wq` 并按 `Enter` 保存并退出

验证两个条目都存在：

```bash
crontab -l
```

你现在应该看到列出的两个 `cron` 作业。

`cron` 还支持可以简化常见调度的特殊字符串。这些包括 `@reboot`、`@yearly`、`@annually`、`@monthly`、`@weekly`、`@daily`、`@midnight` 和 `@hourly`。例如，`@hourly` 等同于 `0 * * * *`。

让我们添加一个每小时运行并将系统运行时间记录到 `~/uptime_log.txt` 的作业。

打开你的 `crontab` 进行编辑：

```bash
crontab -e
```

在 vim 中：

1. 按 `i` 进入插入模式
2. 添加以下行：

```
@hourly uptime >> ~/uptime_log.txt
```

3. 按 `Esc` 退出插入模式
4. 键入 `:wq` 并按 `Enter` 保存并退出

验证所有三个条目：

```bash
crontab -l
```

你现在应该看到所有三个 `cron` 作业。

为了演示这些作业的效果，我们将等待一小段时间。由于作业以不同的间隔调度，我们不会立即看到它们全部执行，但我们可以验证设置。

等待至少 3 分钟，以允许 `*/2` 作业至少运行一次。

检查 `~/cron_messages.txt` 文件：

```bash
cat ~/cron_messages.txt
```

你应该看到至少一条 "Hello from cron!" 消息。

```plaintext
Hello from cron!
```

`~/disk_usage.log` 和 `~/uptime_log.txt` 文件可能尚未创建，具体取决于当前时间，因为它们分别被调度为每日和每小时执行。重要的是，它们的条目已在你的 `crontab` 中正确配置。

这完成了用户 crontab 管理步骤。所有 cron 作业将继续运行，直到容器被销毁。
