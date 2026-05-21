# 使用 ln 命令（硬链接和符号链接）创建文件之间的链接

在此步骤中，你将学习如何使用 `ln` 命令创建文件之间的链接。Linux 文件系统支持两种类型的链接：硬链接和符号链接（或软链接）。理解它们之间的区别对于有效的文件管理至关重要。

首先，确保你位于 `~/project` 目录中。

```bash
cd ~/project
```

验证你的当前位置：

```bash
pwd
```

输出应该是 `/home/labex/project`。

让我们创建一个名为 `original_file.txt` 的新文件，并包含一些内容供我们使用。

```bash
echo "This is the content of the original file." > original_file.txt
```

验证文件内容：

```bash
cat original_file.txt
```

## 硬链接 (Hard Links)

硬链接本质上是现有文件的另一个名称。它直接指向存储设备上与原始文件相同的数据（inode）。创建硬链接后，你无法区分新的硬链接和文件的原始名称；它们是访问相同数据的同等有效的方式。

你可以使用 `ls -l` 命令来确定一个文件是否具有多个硬链接。`ls -l` 输出中的第二列显示了文件的硬链接数量。

最初，`original_file.txt` 有一个硬链接（它本身）：

```bash
ls -l original_file.txt
```

你应该会看到类似以下的输出，其中数字 `1` 表示一个硬链接：

```plaintext
-rw-rw-r--. 1 labex labex 35 Mar  7 HH:MM original_file.txt
```

现在，让我们使用 `ln` 命令创建一个名为 `hard_link.txt` 的硬链接到 `original_file.txt`。

```bash
ln original_file.txt hard_link.txt
```

检查两个文件的硬链接计数：

```bash
ls -l original_file.txt hard_link.txt
```

你现在应该看到两个文件的硬链接计数都为 `2`，这表明它们指向相同的数据：

```plaintext
-rw-rw-r--. 2 labex labex 35 Mar  7 HH:MM hard_link.txt
-rw-rw-r--. 2 labex labex 35 Mar  7 HH:MM original_file.txt
```

为了确认它们指向相同的数据，你可以使用 `ls -i` 选项来列出每个文件的 inode 号。如果文件位于同一文件系统上且它们的 inode 号相同，那么这些文件就是指向相同数据文件内容的硬链接。

```bash
ls -i original_file.txt hard_link.txt
```

inode 号应该是相同的：

```plaintext
1234567 original_file.txt  1234567 hard_link.txt
```

如果你修改其中一个文件的内容，更改将反映在另一个文件中，因为它们是相同的基础数据。

```bash
echo "Adding new line." >> hard_link.txt
```

```bash
cat original_file.txt
```

你将看到：

```plaintext
This is the content of the original file.
Adding new line.
```

即使原始文件被删除，只要至少存在一个其他硬链接，你仍然可以访问该文件的内容。数据仅在最后一个硬链接被删除时才从存储中删除，使得文件内容不再被任何硬链接引用。

让我们删除 `original_file.txt`：

```bash
rm original_file.txt
```

现在，尝试访问 `hard_link.txt`：

```bash
cat hard_link.txt
```

你仍然会看到内容：

```plaintext
This is the content of the original file.
Adding new line.
```

`hard_link.txt` 的硬链接计数现在应该为 `1`：

```bash
ls -l hard_link.txt
```

**硬链接的限制：**

- 你只能对常规文件使用硬链接。你不能使用 `ln` 命令创建指向目录或特殊文件的硬链接。
- 你只能在两个文件位于同一文件系统上时使用硬链接。你可以使用 `df` 命令列出文件系统。

## 符号链接 (Symbolic Links)

符号链接（也称为“软链接”或“symlink”）是一种特殊类型的文件，它通过其路径指向另一个文件或目录。它类似于 Windows 中的快捷方式。与硬链接不同，符号链接不直接指向数据；它们指向目标文件或目录的名称。

让我们创建一个名为 `target_file.txt` 的新文件作为我们的符号链接。

```bash
echo "This is the target file for the symbolic link." > target_file.txt
```

现在，使用 `ln -s` 命令创建一个名为 `sym_link.txt` 的符号链接，指向 `target_file.txt`。

```bash
ln -s target_file.txt sym_link.txt
```

使用 `ls -l` 检查符号链接的详细信息：

```bash
ls -l target_file.txt sym_link.txt
```

你会注意到几点不同：

- `sym_link.txt` 的长列表的第一个字符是 `l`（字母 `l`），表示它是一个符号链接。
- 输出显示 `sym_link.txt -> target_file.txt`，明确显示了它指向的内容。
- 符号链接的大小非常小（只是目标路径的长度），而不是目标文件的大小。
- `sym_link.txt` 的硬链接计数为 `1`。

```plaintext
-rw-rw-r--. 1 labex labex 45 Mar  7 HH:MM target_file.txt
lrwxrwxrwx. 1 labex labex 14 Mar  7 HH:MM sym_link.txt -> target_file.txt
```

你可以通过 `sym_link.txt` 访问 `target_file.txt` 的内容：

```bash
cat sym_link.txt
```

你将看到：

```plaintext
This is the target file for the symbolic link.
```

**符号链接的关键区别和行为：**

- **跨文件系统链接：** 符号链接可以链接不同文件系统上的两个文件。
- **链接到目录：** 符号链接可以指向目录，而不仅仅是常规文件。让我们创建一个指向你的 `documents` 目录的符号链接。

  ```bash
  ln -s documents doc_shortcut
  ```

  现在，你可以像 `documents` 目录本身一样 `cd` 进入 `doc_shortcut`：

  ```bash
  cd doc_shortcut
  ```

  验证你的位置。请注意，`pwd` 默认显示符号链接的路径：

  ```bash
  pwd
  ```

  输出：`/home/labex/project/doc_shortcut`

  如果你希望 `pwd` 显示符号链接指向的目录的实际路径，请使用 `-P` 选项：

  ```bash
  pwd -P
  ```

  输出：`/home/labex/project/documents`

  现在，返回到 `~/project`：

  ```bash
  cd ~/project
  ```

- **悬空符号链接 (Dangling symbolic links)：** 当原始常规文件（目标）被删除时，符号链接仍然存在，但指向一个丢失的文件。这被称为“悬空符号链接”。

  让我们删除 `target_file.txt`：

  ```bash
  rm target_file.txt
  ```

  现在，使用 `ls -l` 检查 `sym_link.txt`：

  ```bash
  ls -l sym_link.txt
  ```

  你会看到 `sym_link.txt` 仍然存在，但其目标 `target_file.txt` 以红色或其他颜色显示（取决于你的终端配置），以表明它已损坏：

  ```plaintext
  lrwxrwxrwx. 1 labex labex 14 Mar  7 HH:MM sym_link.txt -> target_file.txt
  ```

  如果你尝试 `cat` 悬空符号链接，它将失败：

  ```bash
  cat sym_link.txt
  ```

  输出：

  ```plaintext
  cat: sym_link.txt: No such file or directory
  ```

最后，让我们清理此步骤中创建的文件和链接。

```bash
rm hard_link.txt sym_link.txt doc_shortcut
```

```bash
ls
```

从之前的步骤来看，你应该只剩下 `documents` 和 `reports` 目录。
