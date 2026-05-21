# 使用 systemctl 屏蔽和解除屏蔽服务

在此最终步骤中，你将学习关于屏蔽 (masking) 和取消屏蔽 (unmasking) 服务。屏蔽服务是一种强大的方式，可以阻止它被手动启动或在启动时自动启动。

当你屏蔽服务时，`systemd` 会在 `/etc/systemd/system/<service-name>.service` 创建一个指向 `/dev/null` 的符号链接，从而有效地使服务单元文件对 `systemd` 不可用。这是比 `disable` 更强大的替代方案。

此机制最适用于定义在 `/usr/lib/systemd/system` 中的服务，这是软件包安装其服务文件的位置。`mask` 命令会在 `/etc/systemd/system` 中创建一个覆盖性的“空”文件。但是，正如你所发现的，如果你尝试屏蔽一个直接在 `/etc/systemd/system` 中创建的服务（如我们的 `mytest.service`），该命令可能会失败，因为它设计为不覆盖现有配置文件，否则会导致数据丢失。

为了正确演示屏蔽，我们将使用一个预先存在的服务 `atd.service`。

首先，让我们检查 `atd.service` 的当前状态。它应该是活动的并且已启用。

```bash
systemctl status atd.service
```

输出将与此类似，显示服务是活动的并且正在运行：

```plaintext
● atd.service - Deferred execution scheduler
     Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-07-22 09:13:06 CST; 22min ago
       Docs: man:atd(8)
   Main PID: 1222 (atd)
      Tasks: 1 (limit: 22509)
     Memory: 900.0K
        CPU: 5ms
     CGroup: /system.slice/atd.service
             └─1222 /usr/sbin/atd -f
```

## 屏蔽服务

在屏蔽服务之前停止它是一个好习惯。

```bash
sudo systemctl stop atd.service
```

现在，执行以下命令屏蔽 `atd.service`：

```bash
sudo systemctl mask atd.service
```

你将看到指示创建指向 `/dev/null` 的符号链接的输出：

```plaintext
Created symlink /etc/systemd/system/atd.service → /dev/null.
```

现在，尝试启动被屏蔽的服务：

```bash
sudo systemctl start atd.service
```

你将收到一条错误消息，表明该服务已被屏蔽：

```plaintext
Failed to start atd.service: Unit atd.service is masked.
```

你还可以使用 `systemctl list-unit-files` 检查服务的状态：

```bash
systemctl list-unit-files --type=service | grep atd.service
```

输出应显示 `atd.service` 的状态为 `masked`：

```plaintext
atd.service                            masked      enabled
```

这证实了该服务现在已被屏蔽，无法启动。

## 取消屏蔽服务

要取消屏蔽服务，请使用 `systemctl unmask` 命令。此命令会移除指向 `/dev/null` 的符号链接，从而从 `/usr/lib/systemd/system` 恢复原始服务文件。

执行以下命令取消屏蔽 `atd.service`：

```bash
sudo systemctl unmask atd.service
```

你将看到指示移除符号链接的输出：

```plaintext
Removed "/etc/systemd/system/atd.service".
```

现在，再次使用 `systemctl status atd.service` 检查服务的状态：

```bash
systemctl status atd.service
```

你应该看到 `Active: active (running)`，类似如下：

```plaintext
● atd.service - Deferred execution scheduler
     Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-07-22 09:36:10 CST; 2s ago
       Docs: man:atd(8)
   Main PID: 7372 (atd)
      Tasks: 1 (limit: 22509)
     Memory: 868.0K
        CPU: 6ms
     CGroup: /system.slice/atd.service
             └─7372 /usr/sbin/atd -f
```

这标志着关于控制服务和守护进程的实验结束了。你已经学会了如何使用 `systemctl` 查看、启动、停止、重启、重新加载、启用、禁用、屏蔽和取消屏蔽服务。
