# 使用 cron 目录调度系统定期作业

在这一步中，你将学习如何使用 `cron` 目录调度重复的系统范围任务。与特定于用户的用户 `crontab` 条目不同，系统 `cron` 作业由 root 用户管理，并影响整个系统。这些通常用于系统维护、日志轮换和其他管理任务。

我们将继续在本地系统上工作，以探索系统范围的 cron 作业配置。

系统范围的 `cron` 作业在 `/etc/crontab` 中定义，或者通过将脚本放置在特定目录中：

- `/etc/cron.hourly/`：此目录中的脚本每小时运行一次。
- `/etc/cron.daily/`：此目录中的脚本每天运行一次。
- `/etc/cron.weekly/`：此目录中的脚本每周运行一次。
- `/etc/cron.monthly/`：此目录中的脚本每月运行一次。

这些目录由 `run-parts` 实用程序处理，该实用程序执行它们中的所有可执行文件。

要管理系统 `cron` 作业，你需要 root 权限。由于 labex 用户具有 sudo 访问权限，我们可以使用 `sudo` 来执行所需的命令。

让我们创建一个简单的脚本，将消息记录到系统日志。我们将把这个脚本放在 `/etc/cron.hourly/` 中，使其每小时运行一次。

首先，创建脚本文件 `/etc/cron.hourly/my_hourly_script`：

```bash
sudo nano /etc/cron.hourly/my_hourly_script
```

将以下内容添加到文件中：

```bash
#!/bin/bash
logger "Hourly cron job executed at $(date)"
```

保存并退出编辑器（在 `nano` 中使用 `Ctrl+o`、`Enter`、`Ctrl+x`）。

接下来，你需要使脚本可执行。如果没有执行权限，`run-parts` 将忽略它。

```bash
sudo chmod +x /etc/cron.hourly/my_hourly_script
```

现在，让我们验证脚本是否可执行：

```bash
ls -l /etc/cron.hourly/my_hourly_script
```

你应该在权限中看到 `x`，例如：`-rwxr-xr-x`。

由于 `cron.hourly` 作业每小时运行一次，我们无法在这个实验中等待一整小时来验证它的执行。但是，我们可以手动触发 hourly 目录的 `run-parts` 命令来模拟它的执行。

```bash
sudo run-parts /etc/cron.hourly/
```

此命令将执行 `/etc/cron.hourly/` 中的所有可执行脚本。我们创建的脚本使用 `logger` 命令将消息写入系统日志。虽然我们无法在此容器环境中轻松验证日志输出，但重要的学习目标是了解如何在 cron 目录中创建和管理脚本。

在真实的 RHEL 系统中，你将能够使用 `journalctl` 或 `/var/log/messages` 检查系统日志，以验证脚本是否成功执行。

这完成了系统 cron 作业管理步骤。该脚本将保留在原位，并在真实系统环境中每小时执行一次。
