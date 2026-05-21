# 从 tar 存档中提取文件

本步骤将介绍如何从 `tar` 归档文件中提取文件。提取文件是将归档内容取出并放回文件系统中的过程。

提取文件的首要选项是 `-x` 或 `--extract`。你通常还会使用 `-f` 来指定归档文件，以及 `-v` 来显示提取文件的详细信息。

在提取之前，最好将归档文件提取到一个空目录，以避免覆盖现有文件或将它们与其他内容混合。让我们在你的 `~/project` 目录中创建一个名为 `extracted_files` 的新目录。

```bash
cd ~/project
mkdir extracted_files
```

现在，导航到 `extracted_files` 目录。这确保了归档内容将被提取到此处。

```bash
cd extracted_files
```

现在，让我们将 `my_archive.tar` (位于父目录 `~/project` 中) 的内容提取到当前的 `extracted_files` 目录中。

```bash
tar -xvf ../my_archive.tar
```

输出将显示正在提取的文件：

```plaintext
my_files/
my_files/file1.txt
my_files/file2.txt
my_files/file3.txt
```

提取后，你可以列出当前目录 (`~/project/extracted_files`) 的内容，以验证 `my_files` 目录及其内容已成功提取。

```bash
ls
```

你应该看到 `my_files` 目录：

```plaintext
my_files
```

现在，让我们检查 `extracted_files` 中 `my_files` 目录的内容：

```bash
ls my_files
```

你应该看到原始文件：

```plaintext
file1.txt  file2.txt  file3.txt
```

你还可以查看其中一个提取文件的內容以确认其完整性：

```bash
cat my_files/file1.txt
```

输出应为：

```plaintext
This is file1 content.
```

在提取文件时，`tar` 命令使用当前的 `umask` 来设置提取文件的权限。但是，如果你想保留归档文件中文件的原始权限，可以使用 `-p` 或 `--preserve-permissions` 选项。这在处理需要特定权限的可执行脚本或配置文件时特别有用。对于 `root` 用户，此选项通常默认启用。对于普通用户，如果权限保留很重要，最好包含它。

在本实验中，我们不会明确演示 `-p` 选项，因为默认行为足以满足我们的文本文件需求。但是，请记住此选项，以便在未来的用例中使用。
