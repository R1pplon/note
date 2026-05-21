# 管理 SELinux 布尔值设置

SELinux 布尔值是允许你在运行时更改部分 SELinux 策略的开关，无需重新加载或重新编译策略。这为调整 Apache Web 服务器（`httpd`）等服务的安全设置提供了一种灵活的方式。在本步骤中，你将检查并修改一个与 `httpd` 网络连接相关的 SELinux 布尔值。

## 任务

- **任务 1：** 列出所有可用的 SELinux 布尔值及其当前状态。
- **任务 2：** 临时启用 `httpd_can_network_connect` 布尔值。
- **任务 3：** 永久禁用 `httpd_can_network_connect` 布尔值以还原更改。

## 要求

- 所有命令必须以 `labex` 用户身份执行。在需要管理权限的地方使用 `sudo`。
- 在 `/home/labex` 目录下进行所有操作。
- 使用 `getsebool` 和 `setsebool` 命令。

## 示例

在临时启用 `httpd_can_network_connect` 布尔值后，执行 `getsebool httpd_can_network_connect` 的输出应为：

```plaintext
httpd_can_network_connect --> on
```

在永久禁用它之后，输出应为：

```plaintext
httpd_can_network_connect --> off
```

## 提示

- 使用 `getsebool` 的 `-a` 选项来列出所有布尔值。
- `setsebool` 命令接受布尔值名称和状态（`on` 或 `off`）作为参数。
- 若要使 `setsebool` 的更改在系统重启后依然有效，请使用 `-P` 选项。
