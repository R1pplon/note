# 将 SELinux 设置为宽容模式

现在，你将把 SELinux 切换回宽容（`Permissive`）模式。在此模式下，SELinux 会记录违反策略的行为但不会进行拦截，这对于排查安全策略故障非常有用。

## 任务

- 将 SELinux 运行时模式更改为 `Permissive`。
- 验证当前模式是否为 `Permissive`。

## 要求

- 本挑战的默认用户账号为 `labex`，密码为 `labex`。
- 所有命令必须在 `/home/labex` 目录下运行。
- 使用 `setenforce` 命令更改 SELinux 模式。

## 示例

将 SELinux 设置为宽容模式后，`getenforce` 命令的输出应为：

```plaintext
Permissive
```
