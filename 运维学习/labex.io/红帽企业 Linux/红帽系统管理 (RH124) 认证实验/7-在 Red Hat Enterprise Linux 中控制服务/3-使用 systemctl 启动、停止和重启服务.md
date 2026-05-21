# 使用 systemctl 启动、停止和重启服务

在此步骤中，你将学习如何使用 `systemctl` 命令管理系统服务的生命周期。你将练习启动、停止和重启服务。在本实验中，我们将使用一个我们将创建的虚拟服务。这种方法确保我们可以安全地操作服务而不会影响关键的系统功能。

首先，让我们创建一个简单的服务单元文件。此文件将定义一个每隔几秒钟将时间戳写入日志文件的服务。

使用 `nano` 在 systemd 系统目录中直接创建一个名为 `mytest.service` 的新服务单元文件：

```bash
sudo nano /etc/systemd/system/mytest.service
```

将以下内容粘贴到 `nano` 编辑器中：

```ini
[Unit]
Description=My Test Service
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -c 'while true; do echo "$(date): My Test Service is running." >> /tmp/mytest.log; sleep 5; done'
ExecStop=/bin/bash -c 'echo "$(date): My Test Service stopped." >> /tmp/mytest.log'
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

- **`[Unit]`**: 包含关于单元的通用信息。`Description` 提供了一个人类可读的名称，而 `After=network.target` 指定此服务应在网络启动后启动。
- **`[Service]`**: 定义了服务的行为。
  - `Type=simple`: 指示一种简单的服务类型，其中 `ExecStart` 命令是主进程。
  - `ExecStart`: 启动服务时要执行的命令。在这里，它是一个 bash 循环，每 5 秒将一个带时间戳的消息写入 `/tmp/mytest.log`。
  - `ExecStop`: 停止服务时要执行的命令。它将停止消息写入日志。
  - `Restart=on-failure`: 配置服务在以非零状态退出时重新启动。
- **`[Install]`**: 包含有关服务应如何安装的信息。`WantedBy=multi-user.target` 意味着此服务应在系统达到多用户运行级别时启动。

通过按 `Ctrl+X`，然后按 `Y` 确认，最后按 `Enter` 保存文件来保存文件。

现在，重新加载 systemd 守护进程以识别新的服务文件：

```bash
sudo systemctl daemon-reload
```

## 启动服务

要启动服务，请使用 `systemctl start` 命令。

执行以下命令启动 `mytest.service`。请注意，我们需要使用 `sudo`，因为 `systemctl` 操作通常需要 root 权限。

```bash
sudo systemctl start mytest.service
```

如果命令成功，将不会有立即的输出。

现在，通过检查其状态来验证服务是否正在运行：

```bash
systemctl status mytest.service
```

你应该看到指示服务为 `active (running)` 的输出：

```plaintext
● mytest.service - My Test Service
     Loaded: loaded (/etc/systemd/system/mytest.service; disabled; preset: disabled)
     Active: active (running) since ...
   Main PID: ... (bash)
      Tasks: 2 (limit: ...)
     Memory: ...
        CPU: ...
     CGroup: /system.slice/mytest.service
             ├─... /bin/bash -c "while true; do echo \"\$(date): My Test Service is running.\" >> /tmp/mytest.log; sleep 5; done"
             └─... sleep 5

...output omitted...
```

你还可以检查日志文件以查看服务是否正在写入消息：

```bash
tail -f /tmp/mytest.log
```

你应该看到每 5 秒出现新行，类似这样：

```plaintext
Tue Jul 22 09:15:09 AM CST 2025: My Test Service is running.
Tue Jul 22 09:15:14 AM CST 2025: My Test Service is running.
```

按 `Ctrl+C` 退出 `tail`。

## 停止服务

要停止正在运行的服务，请使用 `systemctl stop` 命令。

执行以下命令停止 `mytest.service`：

```bash
sudo systemctl stop mytest.service
```

同样，不会有立即的输出。

验证服务是否已停止：

```bash
systemctl status mytest.service
```

输出现在应显示 `Active: inactive (dead)`：

```plaintext
○ mytest.service - My Test Service
     Loaded: loaded (/etc/systemd/system/mytest.service; disabled; preset: disabled)
     Active: inactive (dead) since ...
...output omitted...
```

再次检查日志文件 `/tmp/mytest.log`。你应该看到“My Test Service stopped.”消息，并且没有新的“running”消息出现。

```bash
tail /tmp/mytest.log
```

输出将类似这样：

```plaintext
Tue Jul 22 09:15:24 AM CST 2025: My Test Service is running.
Tue Jul 22 09:15:28 AM CST 2025: My Test Service stopped.
```

## 重启服务

要重启服务，请使用 `systemctl restart` 命令。此命令首先停止服务，然后再次启动它。当您对服务的配置进行了更改并需要这些更改生效时，此命令很有用。

执行以下命令重启 `mytest.service`：

```bash
sudo systemctl restart mytest.service
```

验证服务是否再次运行：

```bash
systemctl status mytest.service
```

你应该再次看到 `Active: active (running)`，并且 `Main PID` 可能会是一个新的数字，表明已启动一个新进程。

```plaintext
● mytest.service - My Test Service
     Loaded: loaded (/etc/systemd/system/mytest.service; disabled; preset: disabled)
     Active: active (running) since ...
   Main PID: ... (bash)
      Tasks: 2 (limit: ...)
     Memory: ...
        CPU: ...
     CGroup: /system.slice/mytest.service
             ├─... /bin/bash -c "while true; do echo \"\$(date): My Test Service is running.\" >> /tmp/mytest.log; sleep 5; done"
             └─... sleep 5
...output omitted...
```

检查日志文件 `/tmp/mytest.log` 以确认服务已恢复写入“running”消息。

```bash
tail -f /tmp/mytest.log
```

你应该看到一条“stopped”消息，后跟新的“running”消息：

```plaintext
Tue Jul 22 09:15:28 AM CST 2025: My Test Service stopped.
Tue Jul 22 09:15:40 AM CST 2025: My Test Service is running.
```

按 `Ctrl+C` 退出 `tail`。
