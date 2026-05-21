# 使用 umask 配置默认权限

在最后一步，你将学习 `umask`，它控制分配给新创建的文件和目录的默认权限。`umask`（用户文件创建模式掩码）是一个位掩码，它从最大可能的权限中*移除*权限。

新文件的最大权限是 `666` (rw-rw-rw-)，表示每个人都可以读写。出于安全原因，新文件通常默认不会获得执行权限。新目录的最大权限是 `777` (rwxrwxrwx)，表示每个人都可以读、写和执行。

`umask` 值从这些最大权限中减去，以确定实际的默认权限。

要查看你当前的 `umask` 值，只需键入 `umask`：

```bash
umask
```

你可能会看到 `0002` 或 `0022`。在这个环境中，对于 `labex` 用户来说，它是 `0022`。

`umask` 为 `0022` 意味着：

- 第一个 `0` 用于特殊权限（SUID、SGID、Sticky Bit），默认情况下不受 `umask` 的影响。
- 第二个 `0` 意味着没有从所有者那里移除权限。
- 第三个 `2` 意味着从组中移除写权限（值 2）。
- 第四个 `2` 意味着从其他人中移除写权限（值 2）。

让我们使用 `umask` 为 `0022` 来计算默认权限：

- **对于文件（最大 666）：**

  - 所有者：6 - 0 = 6 (rw-)
  - 组：6 - 2 = 4 (r--)
  - 其他人：6 - 2 = 4 (r--)
  - 结果文件权限：`644` (rw-r--r--)

- **对于目录（最大 777）：**
  - 所有者：7 - 0 = 7 (rwx)
  - 组：7 - 2 = 5 (r-x)
  - 其他人：7 - 2 = 5 (r-x)
  - 结果目录权限：`755` (rwxr-xr-x)

让我们测试一下。创建一个新文件和目录：

```bash
touch ~/project/new_file_umask.txt
mkdir ~/project/new_dir_umask
```

检查它们的权限：

```bash
ls -l ~/project/new_file_umask.txt
ls -ld ~/project/new_dir_umask
```

你应该会看到类似 `-rw-r--r--` 的文件权限和 `drwxr-xr-x` 的目录权限，这确认了 `0022` `umask` 的效果。

现在，让我们将 `umask` 更改为 `0077`。这个 `umask` 将移除所有组和其他人的权限。

```bash
umask 0077
```

验证 `umask` 是否已更改：

```bash
umask
```

输出：

```plaintext
0077
```

现在，让我们使用 `umask` 为 `0077` 来计算默认权限：

- **对于文件（最大 666）：**

  - 所有者：6 - 0 = 6 (rw-)
  - 组：6 - 7 = -1 (实际上是 0, ---)
  - 其他人：6 - 7 = -1 (实际上是 0, ---)
  - 结果文件权限：`600` (rw-------)

- **对于目录（最大 777）：**
  - 所有者：7 - 0 = 7 (rwx)
  - 组：7 - 7 = 0 (---)
  - 其他人：7 - 7 = 0 (---)
  - 结果目录权限：`700` (rwx------)

让我们测试一下这个新的 `umask`。创建另一个新文件和目录：

```bash
touch ~/project/restricted_file.txt
mkdir ~/project/restricted_dir
```

检查它们的权限：

```bash
ls -l ~/project/restricted_file.txt
ls -ld ~/project/restricted_dir
```

你现在应该会看到类似 `-rw-------` 的文件权限和 `drwx------` 的目录权限。

`umask` 设置通常在 shell 初始化文件（如 `~/.bashrc` 或 `/etc/profile`）中配置，以便在用户登录时自动应用。对于这个实验，`umask` 的更改是临时的，仅适用于当前的终端会话。

要将 `umask` 恢复为其 `labex` 用户的默认值，你可以简单地将其设置回 `0022`：

```bash
umask 0022
```

最后，清理在此步骤中创建的文件和目录：

```bash
rm ~/project/new_file_umask.txt ~/project/restricted_file.txt
rmdir ~/project/new_dir_umask ~/project/restricted_dir
```
