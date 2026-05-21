# 使用 systemctl 设置服务开机自启和禁用

在此步骤中，你将学习如何配置服务以便在启动时自动启动（启用）或阻止它们在启动时启动（禁用）。这对于管理系统资源和确保系统启动时必需的服务可用至关重要。

在典型的 `systemd` 环境中，启用服务会在适当的 `systemd` 配置目录（例如 `/etc/systemd/system/multi-user.target.wants/`）中创建指向服务单元文件的符号链接。禁用服务会移除这些链接。

由于我们处于容器化环境中，`systemd` 在传统意义上可能无法完全运行，因此 `enable` 和 `disable` 命令可能不会在 `/etc/systemd/system` 目录中创建实际的符号链接，这些链接在容器重启后会持续存在。但是，`systemctl` 仍然会处理这些命令并更新其内部状态，这就是我们将要观察到的。

我们将继续使用我们的 `mytest.service` 进行此演示。

首先，确保 `mytest.service` 已从上一步停止：

```bash
sudo systemctl stop mytest.service
```

## 启用服务

要启用服务，请使用 `systemctl enable` 命令。此命令配置服务以便在系统启动时自动启动。

执行以下命令启用 `mytest.service`：

```bash
sudo systemctl enable mytest.service
```

你可能会看到类似以下的输出，表明在完整的 `systemd` 环境中会创建一个符号链接：

```plaintext
Created symlink /etc/systemd/system/multi-user.target.wants/mytest.service → /etc/systemd/system/mytest.service.
```

现在，使用 `systemctl is-enabled` 验证服务是否已启用：

```bash
systemctl is-enabled mytest.service
```

预期输出：

```plaintext
enabled
```

这证实了 `systemctl` 现在认为 `mytest.service` 已被启用以在启动时运行。

你还可以使用 `--now` 选项将启用和启动服务合并为一个命令。这是一种方便的方式，可以确保服务立即运行并配置为在将来的启动时启动。

首先，让我们禁用它以准备 `--now` 演示：

```bash
sudo systemctl disable mytest.service
```

现在，同时启用并启动它：

```bash
sudo systemctl enable --now mytest.service
```

验证其状态和启用情况：

```bash
systemctl status mytest.service
systemctl is-enabled mytest.service
```

你应该从 `status` 命令看到 `Active: active (running)`，从 `is-enabled` 命令看到 `enabled`。

## 禁用服务

要禁用服务，请使用 `systemctl disable` 命令。此命令会移除导致服务在启动时启动的配置。

执行以下命令禁用 `mytest.service`：

```bash
sudo systemctl disable mytest.service
```

你可能会看到指示移除符号链接的输出：

```plaintext
Removed /etc/systemd/system/multi-user.target.wants/mytest.service.
```

现在，验证服务是否已禁用：

```bash
systemctl is-enabled mytest.service
```

预期输出：

```plaintext
disabled
```

与启用类似，你可以使用 `--now` 选项将禁用和停止服务合并。这将立即停止服务，并阻止它在将来的启动时启动。

```bash
sudo systemctl disable --now mytest.service
```

验证其状态和启用情况：

```bash
systemctl status mytest.service
systemctl is-enabled mytest.service
```

你应该从 `status` 命令看到 `Active: inactive (dead)`，从 `is-enabled` 命令看到 `disabled`。

这结束了启用和禁用服务的演示。请记住，在真实的 `systemd` 环境中，这些命令会直接操作启动配置。
