# 配置 systemd 定时器以执行重复任务

在这一步中，你将了解 `systemd` 定时器，它是 `cron` 在 Linux 系统上调度任务的现代替代方案。`systemd` 定时器提供了更大的灵活性，并与 `systemd` 生态系统更好地集成。虽然 `systemctl` 命令通常用于管理 `systemd` 单元，但由于 Docker 容器环境的限制，我们将专注于直接创建和验证定时器和服务单元文件。

`systemd` 定时器与 `systemd` 服务单元结合使用。定时器单元（`.timer` 文件）定义了何时应该运行任务，而服务单元（`.service` 文件）定义了应该执行什么任务。

我们将继续在本地系统上工作，以探索 systemd 定时器配置。

你需要 root 权限才能在系统目录中创建 `systemd` 单元文件。由于 labex 用户具有 sudo 访问权限，我们可以使用 `sudo` 来执行所需的命令。

让我们创建一个简单的服务，将消息记录到文件中。我们将把这个服务单元文件放在 `/etc/systemd/system/` 中，这是自定义服务单元的典型存储位置。

创建服务单元文件 `/etc/systemd/system/my-custom-task.service`：

```bash
sudo nano /etc/systemd/system/my-custom-task.service
```

将以下内容添加到文件中：

```ini
[Unit]
Description=My Custom Scheduled Task

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo "My custom task executed at $(date)" >> /var/log/my-custom-task.log'
```

保存并退出编辑器（在 `nano` 中使用 `Ctrl+o`、`Enter`、`Ctrl+x`）。

接下来，创建定时器单元文件 `/etc/systemd/system/my-custom-task.timer`。此定时器将每 5 分钟激活我们的服务。

```bash
sudo nano /etc/systemd/system/my-custom-task.timer
```

将以下内容添加到文件中：

```ini
[Unit]
Description=Run My Custom Scheduled Task every 5 minutes

[Timer]
OnCalendar=*:0/5
Persistent=true

[Install]
WantedBy=timers.target
```

保存并退出编辑器。

**`OnCalendar` 的解释：**

- `*:0/5` 意思是“每 5 分钟”。
  - `*` 代表年、月、日、小时（任何值）。
  - `0/5` 代表分钟，意思是从第 0 分钟开始，每 5 分钟（0、5、10、...、55）。

在典型的 `systemd` 环境中，你现在将运行 `systemctl daemon-reload` 以使 `systemd` 知道新的单元文件，然后运行 `systemctl enable --now my-custom-task.timer` 以启动定时器。但是，由于 Docker 容器的限制，`systemctl` 无法完全运行。

相反，我们将手动验证文件的创建。容器内的 `systemd` 守护程序最终可能会拾取这些文件，但我们无法在此实验设置中直接控制或观察其定时器执行。这里的主要目标是了解如何*配置*这些文件。

让我们验证已创建文件的存在：

```bash
ls -l /etc/systemd/system/my-custom-task.service
ls -l /etc/systemd/system/my-custom-task.timer
```

你应该看到输出，表明这两个文件都存在。

要模拟服务的执行，你可以手动运行在 `ExecStart` 中定义的命令：

```bash
sudo /bin/bash -c 'echo "My custom task executed at $(date)" >> /var/log/my-custom-task.log'
```

现在，检查日志文件以查看输出：

```bash
sudo cat /var/log/my-custom-task.log
```

你应该看到你刚刚记录的消息：

```plaintext
My custom task executed at Tue Jun 10 06:54:40 UTC 2025
```

这完成了 systemd 定时器配置步骤。服务和定时器单元文件将保留在原位以供参考。
