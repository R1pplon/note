# 管理 DNF 软件仓库

在本步骤中，你将学习如何管理 DNF 软件仓库。仓库是存储软件包的位置，`dnf` 从中检索软件包。了解如何列出、启用、禁用和添加仓库对于控制系统上可用的软件至关重要。

首先，让我们列出所有已配置的 DNF 仓库及其状态（启用或禁用）。

```bash
sudo dnf repolist all
```

你将看到一个仓库 ID、名称及其当前状态的列表。

```plaintext
(此处省略输出)
```

`dnf config-manager` 命令是管理仓库配置的强大工具。你可以使用它来启用或禁用仓库。例如，让我们尝试启用一个假设的调试仓库。虽然这个特定的仓库可能在这个实验环境中不存在或不可访问，但该命令演示了语法。

```bash
sudo dnf config-manager --enable rhel-9-server-debug-rpms
```

你将看到关于订阅管理的消息和一个指示该仓库未找到的错误，这在当前环境中是预期的。

```plaintext
(此处省略输出)
```

现在，让我们尝试禁用一个仓库。我们将使用 `rhel-9-for-x86_64-appstream-rpms` 作为示例。**注意：这个特定的仓库名称在这个 UBI 环境中不存在，但该命令演示了语法。**

```bash
sudo dnf config-manager --disable rhel-9-for-x86_64-appstream-rpms
```

你将看到关于订阅管理的消息和一个指示该仓库在这个环境中不存在的错误。

```plaintext
(此处省略输出)
```

让我们验证这个仓库名称在当前系统中是否存在：

```bash
sudo dnf repolist all | grep rhel-9-for-x86_64-appstream-rpms
```

正如预期的那样，由于这个仓库在这个 UBI 环境中不存在，因此不会有任何输出。

```plaintext
(无输出)
```

让我们尝试相同的启用命令来确认仓库不存在：

```bash
sudo dnf config-manager --enable rhel-9-for-x86_64-appstream-rpms
```

再次，你将看到相同的错误消息：

```plaintext
(此处省略输出)
```

`dnf config-manager --add-repo` 命令也可以用于通过指定 URL 添加新的仓库。为了演示，我们将尝试添加一个通用的 EPEL (Extra Packages for Enterprise Linux) 仓库 URL。虽然这可能无法完全配置仓库（因为它通常需要 GPG 密钥和特定的 `.repo` 文件），但它展示了该命令的功能。

```bash
sudo dnf config-manager --add-repo="https://dl.fedoraproject.org/pub/epel/9/Everything/x86_64/"
```

你将看到输出，指示已创建新的 `.repo` 文件。

```plaintext
(此处省略输出)
```

你可以检查 `/etc/yum.repos.d/` 中新创建的 `.repo` 文件。该文件名称将从 URL 派生。

```bash
ls /etc/yum.repos.d/
```

你应该看到一个类似于 `dl.fedoraproject.org_pub_epel_9_Everything_x86_64_.repo` 的文件，以及现有的仓库文件。

```plaintext
(此处省略输出)
```

最后，让我们删除我们刚刚添加的仓库配置文件以进行清理。

```bash
sudo rm /etc/yum.repos.d/dl.fedoraproject.org_pub_epel_9_Everything_x86_64_.repo
```

本步骤向你展示了如何列出、启用、禁用和添加 DNF 仓库，这些技能对于管理 RHEL 上的软件源至关重要。
