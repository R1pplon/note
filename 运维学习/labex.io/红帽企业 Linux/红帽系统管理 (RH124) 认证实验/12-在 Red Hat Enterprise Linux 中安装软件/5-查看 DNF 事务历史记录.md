# 查看 DNF 事务历史记录

在本步骤中，你将学习如何查看 DNF 操作的事务历史记录。`dnf` 会详细记录所有软件包的安装、删除和更新操作。此历史记录对于故障排除、审计，甚至在必要时恢复更改至关重要。

首先，让我们查看系统上发生的所有 DNF 事务的摘要。

```bash
sudo dnf history
```

你将看到一个表格，列出每个事务的 ID、使用的命令行、日期和时间、执行的操作以及更改的软件包数量。

```plaintext
(此处省略输出)
```

“ID”列尤其重要，因为它允许你参考特定事务。例如，如果要查看特定事务的详细信息，可以使用 `dnf history info <ID>`。让我们查看最后一次事务的详细信息（这应该是在上一步中删除的 `httpd`）。你可以从 `dnf history` 输出中找到 ID。在上面的示例中，它是 `2`。

```bash
sudo dnf history info 2
```

此命令提供了所选事务的全面分解，包括已删除的软件包、它们的版本以及执行操作的原因。

```plaintext
(此处省略输出)
```

DNF 历史记录的一个强大功能是能够撤销或重做事务。例如，要撤销 `httpd` 的删除操作，可以使用 `dnf history undo <ID>`。让我们撤销 `httpd` 的删除事务（使用你 `dnf history` 输出中的 ID，例如 `2`）。

```bash
sudo dnf history undo 2 -y
```

此命令将重新安装 `httpd` 软件包及其在该特定事务中已删除的依赖项。

```plaintext
(此处省略输出)
```

你可以再次验证 `httpd` 是否已安装：

```bash
rpm -q httpd
```

```plaintext
(此处省略输出)
```

最后，让我们再次删除 `httpd`，以便为未来的实验保留一个干净的系统状态。

```bash
sudo dnf remove httpd -y
```

```plaintext
(此处省略输出)
```

本步骤演示了如何使用 `dnf history` 查看、检查甚至恢复 DNF 事务，为系统管理提供了强大的功能。
