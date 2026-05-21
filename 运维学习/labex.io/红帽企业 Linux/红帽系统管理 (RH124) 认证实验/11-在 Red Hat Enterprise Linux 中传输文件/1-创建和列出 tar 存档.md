# 创建和列出 tar 存档

本步骤将介绍如何创建和列出 `tar` 归档文件。`tar` 工具是一个强大的命令行工具，用于归档文件和目录。它常用于备份和传输文件。

`tar` 命令需要一个操作选项来指定它应该执行的操作。常见的操作选项包括：

- `-c` 或 `--create`：创建新的归档文件。
- `-t` 或 `--list`：列出归档文件的内容。
- `-x` 或 `--extract`：从归档文件中提取文件。

此外，`tar` 通常使用通用选项来修改其行为：

- `-v` 或 `--verbose`：在归档或提取过程中显示正在处理的文件。
- `-f` 或 `--file`：指定归档文件名。此选项必须后跟归档文件名。

让我们首先创建一些我们将要归档的示例文件。导航到你的 `~/project` 目录，这是你的默认工作目录。

```bash
cd ~/project
```

现在，创建一个名为 `my_files` 的新目录，并在其中创建一些示例文本文件。

```bash
mkdir my_files
echo "这是文件1的内容。" > my_files/file1.txt
echo "这是文件2的内容。" > my_files/file2.txt
echo "这是文件3的内容。" > my_files/file3.txt
ls my_files
```

你应该看到三个文件列出：

```plaintext
file1.txt  file2.txt  file3.txt
```

现在，让我们创建一个 `my_files` 目录的 `tar` 归档文件。我们将把归档文件命名为 `my_archive.tar`。

```bash
tar -cvf my_archive.tar my_files
```

输出将显示正在添加到归档文件中的文件：

```plaintext
my_files/
my_files/file1.txt
my_files/file2.txt
my_files/file3.txt
```

你可以验证归档文件 `my_archive.tar` 已在当前目录中创建：

```bash
ls
```

你应该看到 `my_archive.tar` 和 `my_files` 一起列出：

```plaintext
my_archive.tar  my_files
```

接下来，让我们使用 `-t` (列出) 和 `-f` (文件) 选项列出 `my_archive.tar` 文件的内容。

```bash
tar -tf my_archive.tar
```

输出将显示归档文件的内容：

```plaintext
my_files/
my_files/file1.txt
my_files/file2.txt
my_files/file3.txt
```

如果你想查看更详细的信息，例如文件权限、所有权和大小，你可以添加 `-v` (详细) 选项：

```bash
tar -tvf my_archive.tar
```

输出将类似于此，提供有关每个归档项的更多详细信息：

```plaintext
drwxr-xr-x labex/labex        0 2023-10-27 10:00 my_files/
-rw-r--r-- labex/labex       22 2023-10-27 10:00 my_files/file1.txt
-rw-r--r-- labex/labex       22 2023-10-27 10:00 my_files/file2.txt
-rw-r--r-- labex/labex       22 2023-10-27 10:00 my_files/file3.txt
```

请注意，`tar` 默认情况下会从绝对路径中删除前导 `/`，并在归档时。这是为了防止在提取归档文件时意外覆盖系统文件。例如，如果你要归档 `/etc/hosts`，它将在 `tar` 文件中存储为 `etc/hosts`。这允许你将其提取到新位置，而不会影响原始的 `/etc/hosts` 文件。
