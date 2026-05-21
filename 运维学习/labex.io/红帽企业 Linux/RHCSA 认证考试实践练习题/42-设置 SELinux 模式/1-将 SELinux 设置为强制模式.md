# 将 SELinux 设置为强制模式

你的第一个任务是将 SELinux 从当前模式切换到强制（`Enforcing`）模式。在此模式下，SELinux 会主动拦截任何违反安全策略的操作。

## 任务

- 将 SELinux 运行时模式更改为 `Enforcing`。
- 验证当前模式是否为 `Enforcing`。

## 要求

- 本挑战的默认用户账号为 `labex`，密码为 `labex`。
- 所有命令必须在 `/home/labex` 目录下运行。
- 使用 `setenforce` 命令更改 SELinux 模式。
- 使用 `getenforce` 命令检查当前模式。

## 示例

成功更改模式后，`getenforce` 的输出应为：

```plaintext
Enforcing
```
