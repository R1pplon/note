# 使用 systemd-tmpfiles 管理临时文件

在这一步中，你将学习如何使用 `systemd-tmpfiles` 管理临时文件和目录。此实用程序是 `systemd` 的一部分，负责创建、删除和清理易失性和临时文件和目录。它通常用于管理 `/tmp`、`/var/tmp` 和其他临时存储位置，确保定期删除旧文件。

我们将继续在本地系统上工作，以探索 systemd-tmpfiles 配置。

你需要 root 权限才能配置 `systemd-tmpfiles`。由于 labex 用户具有 sudo 访问权限，我们可以使用 `sudo` 来执行所需的命令。

`systemd-tmpfiles` 从 `/etc/tmpfiles.d/` 和 `/usr/lib/tmpfiles.d/` 读取配置文件。这些文件定义了创建、删除和管理文件和目录的规则。

让我们创建一个自定义配置文件来管理一个新的临时目录。我们将创建一个目录 `/run/my_temp_dir`，并配置 `systemd-tmpfiles` 从其中清理超过 1 分钟的文件。

创建配置文件 `/etc/tmpfiles.d/my_temp_dir.conf`：

```bash
sudo nano /etc/tmpfiles.d/my_temp_dir.conf
```

将以下内容添加到文件中：

```
d /run/my_temp_dir 0755 labex labex 1m
```

**对该行的解释：**

- `d`：指定此条目定义一个目录。
- `/run/my_temp_dir`：目录的路径。
- `0755`：目录的权限。
- `labex labex`：目录的所有者和组。
- `1m`：此目录中的文件应该被删除的时间（1 分钟）。

保存并退出编辑器（在 `nano` 中使用 `Ctrl+o`、`Enter`、`Ctrl+x`）。

现在，让我们告诉 `systemd-tmpfiles` 应用此配置。`--create` 选项将在目录不存在时创建它。

```bash
sudo systemd-tmpfiles --create /etc/tmpfiles.d/my_temp_dir.conf
```

验证目录是否已使用正确的权限和所有权创建：

```bash
ls -ld /run/my_temp_dir
```

你应该看到类似以下的输出：

```plaintext
drwxr-xr-x 2 labex labex 6 Jun 10 06:55 /run/my_temp_dir
```

接下来，让我们在这个新的临时目录中创建一个测试文件：

```bash
sudo touch /run/my_temp_dir/test_file.txt
```

验证文件是否存在：

```bash
ls -l /run/my_temp_dir/test_file.txt
```

现在，我们需要等待超过 1 分钟，让文件根据我们的配置“变旧”。等待至少 70 秒（1 分钟 10 秒）。

等待超过 1 分钟后，我们将手动运行 `systemd-tmpfiles`，并使用 `--clean` 选项来触发基于我们配置的清理过程。

```bash
sudo systemd-tmpfiles --clean /etc/tmpfiles.d/my_temp_dir.conf
```

最后，检查 `test_file.txt` 是否已被删除：

```bash
ls -l /run/my_temp_dir/test_file.txt
```

你应该得到一个“没有这样的文件或目录”错误，这表明 `systemd-tmpfiles` 成功地清理了旧文件。

这完成了 systemd-tmpfiles 配置步骤。配置文件和临时目录将保留在原位以供参考。
