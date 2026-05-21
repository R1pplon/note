# 管理 Systemd 日志

在本挑战中，你将学习如何在运行中的 Linux 系统上管理和维护系统日志，这是 RHCSA 考试所需的一项关键技能。

## 任务

- 任务 1：使用 `journalctl` 命令查看当前系统日志。
- 任务 2：配置系统日志，将其最大磁盘占用限制为 `100M`。
- 任务 3：手动触发日志轮转并应用新的大小限制。

## 要求

- 以具有 `sudo` 权限的 `labex` 用户身份执行所有操作。
- 默认工作目录为 `/home/labex`。
- 修改位于 `/etc/systemd/journald.conf` 的系统日志配置文件。
- 将 `SystemMaxUse` 参数设置为 `100M`。
- 使用 `journalctl` 命令轮转日志并强制执行大小限制。

## 示例

完成任务后，检查日志的磁盘占用情况应显示其未超过配置的限制。具体大小会有所不同，但应保持在 100M 以下。

```plaintext
$ sudo du -sh /var/log/journal/
XXM /var/log/journal/
```
