# 配置防火墙规则以允许 SSH 访问

你的第一个任务是确保允许通过系统防火墙进行 SSH 远程管理。你将为 SSH 服务添加一条永久规则。

在开始之前，检查防火墙状态是一个好习惯。你可以通过运行 `sudo firewall-cmd --state` 来确认。如果防火墙未运行，可以通过运行 `sudo systemctl start firewalld` 来启动它。

## 任务

- 创建一条永久防火墙规则以允许传入的 SSH 流量。
- 将更改应用到当前的防火墙配置中。
- 验证规则是否已成功添加。

## 要求

- 所有防火墙修改必须使用 `firewall-cmd` 命令执行。
- 该规则必须明确启用 `ssh` 服务。
- 该规则必须是永久性的（permanent），以便在系统重启后依然生效。

## 示例

在正确添加永久规则并重载防火墙后，列出永久服务列表时应包含 `ssh`。

```plaintext
$ sudo firewall-cmd --list-services --permanent
cockpit dhcpv6-client ssh
```
