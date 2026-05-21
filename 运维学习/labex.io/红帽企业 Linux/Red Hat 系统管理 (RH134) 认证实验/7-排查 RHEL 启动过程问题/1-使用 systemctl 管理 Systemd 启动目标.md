# 使用 systemctl 管理 Systemd 启动目标

在这一步中，你将学习如何管理 `systemd` 启动目标。在 `systemd` 中，"目标"（target）是一个同步点，它将各种服务和其他单元组合在一起，以使系统达到特定状态。这相当于旧的 SysV init 系统中的 "运行级别"（runlevels）。我们将探讨如何查看当前的默认目标，更改未来启动的默认目标，以及临时切换到不同的目标。

首先，让我们检查你的系统默认启动到哪个目标。`graphical.target` 用于具有桌面环境的系统，提供图形用户界面（GUI）。`multi-user.target` 用于仅限命令行的界面。

要查看当前的默认目标，请运行以下命令：

```bash
systemctl get-default
```

你应该看到默认目标是图形目标。

```plaintext
graphical.target
```

现在，让我们将默认启动目标更改为 `multi-user.target`。这对于服务器环境或在不需要图形界面或图形界面导致问题的故障排除情况下非常有用。`systemctl set-default` 命令通过更改 `/etc/systemd/system/default.target` 符号链接来实现这一点。

使用 `sudo` 以管理员权限执行此命令。

```bash
sudo systemctl set-default multi-user.target
```

输出结果确认符号链接已更新。

```plaintext
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target -> /usr/lib/systemd/system/multi-user.target.
```

你可以通过再次运行 `get-default` 命令来验证默认值是否已更改。

```bash
systemctl get-default
```

输出现在显示了新的默认目标。

```plaintext
multi-user.target
```

有了这个设置，系统将在重启后启动到基于文本的控制台。对于这个实验，我们希望保持一致的图形环境。让我们将默认目标设置回 `graphical.target`。

```bash
sudo systemctl set-default graphical.target
```

你将看到与之前类似的输出，表明符号链接已改回。

```plaintext
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target -> /usr/lib/systemd/system/graphical.target.
```

运行最终检查以确认默认目标已恢复为 `graphical.target`。

```bash
systemctl get-default
```

```plaintext
graphical.target
```

除了更改重启的默认目标之外，你还可以使用 `systemctl isolate` 在当前会话中切换目标。此命令会停止与新目标无关的服务，并启动与新目标相关的服务。例如，运行 `sudo systemctl isolate multi-user.target` 将终止你的图形会话并切换到仅文本控制台。这是一个强大但可能具有破坏性的命令，因此我们不会在此处执行它。

你现在已经成功地使用 `systemctl` 管理了 systemd 目标。
