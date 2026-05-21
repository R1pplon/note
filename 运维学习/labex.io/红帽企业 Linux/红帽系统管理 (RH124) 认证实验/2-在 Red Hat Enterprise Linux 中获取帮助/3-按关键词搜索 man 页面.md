# 按关键词搜索 man 页面

在这一步，你将学习如何按关键词搜索 `man` 页面。当你清楚你想要做什么（例如，“更改密码”）但不知道确切的命令名称时，这非常有用。

`man -k` 选项（等同于 `apropos` 命令）允许你在 `man` 页面的标题和简短描述中搜索关键词。这将列出所有在其单行描述中包含指定关键词的 `man` 页面。

**注意：** 在 LabEx 精简的 UBI9 环境中，你将看到比完整的 RHEL 安装更少的结果。这演示了在可用 `man` 页面中工作时的概念。

假设你想查找与 "curl" 相关的命令。你可以使用 `man -k curl`。

```bash
man -k curl
```

你将看到一个命令列表及其节号，以及一个简短的描述。例如：

```plaintext
curl (1)             - transfer a URL
```

从这个输出中，你可以看到 `curl (1)` 是“transfer a URL”的命令，这是用于数据传输的主要 curl 命令。括号中的数字，如 `(1)`，表示 `man` 页面的部分。

让我们再试一个例子。假设你想查找与 "memory" 相关的命令。你可以搜索 "memory"。

```bash
man -k memory
```

你将获得一个与内存功能相关的 `man` 页面列表。此输出在 UBI9 中可能非常短，但它可以帮助你发现相关的命令。

```plaintext
free (1)             - Display amount of free and used memory in the system
pmap (1)             - report memory map of a process
vmstat (8)           - Report virtual memory statistics
```

当你对确切的命令名称不确定时，这种方法是探索系统功能的绝佳方式。
