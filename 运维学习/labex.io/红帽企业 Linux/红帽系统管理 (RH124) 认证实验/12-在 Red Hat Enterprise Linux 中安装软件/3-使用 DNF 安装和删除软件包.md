# 使用 DNF 安装和删除软件包

在本步骤中，你将学习如何使用 `dnf` (Dandified YUM) 来管理软件包。`dnf` 是 Red Hat Enterprise Linux 9 中默认的软件包管理器，用于安装、更新和删除软件包，以及管理软件仓库。它会自动处理依赖关系，使得软件管理比仅使用 `rpm` 更容易。

首先，让我们列出所有名称中包含 "http" 的可用和已安装软件包。这将让你了解哪些软件包与 HTTP 服务相关。

```bash
sudo dnf list 'http*'
```

你将看到一个软件包列表，指示它们是否已安装或可供安装。

```plaintext
(此处省略输出)
```

现在，让我们搜索名称、摘要或描述中包含 "web server" 的软件包。`search all` 选项用于更广泛的搜索。

```bash
sudo dnf search all 'web server'
```

此命令将返回与搜索条件匹配的软件包列表。

```plaintext
(此处省略输出)
```

让我们获取 `httpd` 软件包（Apache HTTP 服务器）的详细信息。

```bash
sudo dnf info httpd
```

这将显示该软件包的全面详细信息，包括其大小、许可证和描述。

```plaintext
(此处省略输出)
```

现在，让我们安装 `httpd` 软件包。你需要 `sudo` 权限才能执行此操作。

```bash
sudo dnf install httpd -y
```

`-y` 标志会自动回答“是”以响应任何提示，这对于脚本很有用，但在生产环境中请谨慎使用。

```plaintext
(此处省略输出)
```

你可以通过查询 `rpm` 来验证 `httpd` 是否已安装：

```bash
rpm -q httpd
```

```plaintext
(此处省略输出)
```

现在，让我们删除 `httpd` 软件包。

```bash
sudo dnf remove httpd -y
```

这将删除 `httpd` 软件包以及任何不再被其他已安装软件包需要的依赖项。

```plaintext
(此处省略输出)
```

你可以确认其已删除：

```bash
rpm -q httpd
```

```plaintext
(此处省略输出)
```

这演示了 `dnf` 命令在安装和删除软件包方面的基本用法。
