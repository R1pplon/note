# 探索 RPM 包信息

在本步骤中，你将学习如何使用 `rpm` 命令来调查软件包。RPM (Red Hat Package Manager) 是 Red Hat Enterprise Linux 使用的核心软件包管理系统。虽然 `dnf` (你将在后续步骤中探索) 是一个用于从仓库管理软件包的高级工具，`rpm` 允许你查询、验证、安装和卸载单个 `.rpm` 文件。

首先，让我们列出系统上所有已安装的 RPM 软件包。这可能会生成一个很长的列表，因此我们将输出管道化到 `head` 命令，只查看开头部分。

```bash
rpm -qa | head
```

你将看到软件包名称、版本和架构的列表。例如：

```plaintext
libgcc-11.4.1-3.el9.x86_64
crypto-policies-20240202-1.git283706d.el9.noarch
tzdata-2024a-1.el9.noarch
subscription-manager-rhsm-certificates-20220623-1.el9.noarch
redhat-release-9.4-0.4.el9.x86_64
setup-2.13.7-10.el9.noarch
filesystem-3.16-2.el9.x86_64
basesystem-11-13.el9.noarch
pcre2-syntax-10.40-5.el9.noarch
ncurses-base-6.2-10.20210508.el9.noarch
```

接下来，让我们找出哪个软件包提供了特定文件。我们将使用 `/etc/yum.repos.d` 作为示例，这是一个包含 DNF 仓库配置文件的目录。

```bash
rpm -qf /etc/yum.repos.d
```

输出将显示拥有该目录的软件包。

```plaintext
redhat-release-9.4-0.4.el9.x86_64
```

现在，让我们获取已安装软件包的详细信息。我们将使用 `dnf` 软件包本身作为示例。

```bash
rpm -qi dnf
```

此命令提供大量信息，包括软件包名称、版本、发行版、架构、大小、摘要、URL、许可证和详细说明。

```plaintext
(此处省略输出)
```

你还可以列出软件包安装的所有文件。这对于了解软件包在系统上放置了什么内容很有用。

```bash
rpm -ql dnf | head -n 10
```

这将显示 `dnf` 软件包安装的前 10 个文件。

```plaintext
(此处省略输出)
```

要查看软件包安装的配置文件，请使用 `-qc` 选项。让我们检查 `openssh-clients` 软件包。

```bash
rpm -qc openssh-clients
```

这将列出与 SSH 客户端相关的配置文件。

```plaintext
(此处省略输出)
```

最后，要查看软件包的更改日志信息，请使用 `--changelog`。这可以提供有关软件包更新和修复历史的见解。让我们查看 `audit` 软件包。

```bash
rpm -q --changelog audit | head -n 5
```

如果软件包未安装，你将看到一条错误消息：

```plaintext
package audit is not installed
```

你可以尝试使用已安装的软件包。例如，使用 `setup` 软件包：

```bash
rpm -q --changelog setup | head -n 5
```

这些 `rpm` 命令是了解系统上安装的软件包及其包含的文件的强大工具。
