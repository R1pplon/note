# 诊断并解决 SELinux 策略冲突

## 场景

你系统上的 Web 服务器本应提供用户主目录中的内容（例如来自 `~/public_html` 目录）。然而，用户报告称他们的网页无法访问，并显示「Forbidden」错误。你怀疑这是一个 SELinux 问题。你的任务是调查 SELinux 日志，识别策略冲突，并应用正确的修复方案，使 Web 服务器能够按预期运行。

## 任务

- 调查系统的审计日志，查找最近的 SELinux AVC 拒绝记录。
- 分析拒绝消息，了解哪些操作被阻止，以及哪个 SELinux 布尔值（boolean）可以解决该问题。
- 修改相应的 SELinux 布尔值，允许 Web 服务器访问用户主目录。
- 确保策略更改是持久化的，以便在系统重启后依然有效。

## 要求

- 所有命令必须以 `labex` 用户身份运行。在需要管理权限的地方使用 `sudo`。
- 你需要修改的 SELinux 布尔值是 `httpd_enable_homedirs`。
- 更改必须持久化。

## 提示

- 使用 `ausearch` 命令查询审计日志。`-m avc` 选项用于过滤 AVC 消息。
- 密切关注 `ausearch` 的输出。它通常包含来自 `setroubleshoot` 的关于如何解决拒绝问题的建议。
- `setsebool` 命令用于更改 SELinux 布尔值。使用 `-P` 标志使更改持久化。
- 你可以使用 `getsebool` 命令检查布尔值的当前值。
