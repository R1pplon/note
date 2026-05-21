# 使用 rsync 安全同步文件

本实验将介绍如何使用 `rsync` 命令在系统之间同步文件。`rsync` 是一款功能强大且多用途的工具，用于本地和远程复制和同步文件和目录。其主要优势在于它能够只传输文件之间的差异，使其在更新时非常高效。与 `sftp` 一样，`rsync` 可以使用 SSH 进行安全加密传输。

`rsync` 最常用的选项包括：

- `-a` 或 `--archive`：这是几个选项 (`-rlptgoD`) 的组合，保留大多数文件属性（递归、链接、权限、时间、组、所有者、设备文件）。它通常被称为“归档模式”，对于大多数同步任务强烈推荐。
- `-v` 或 `--verbose`：提高详细程度，显示更多关于传输的详细信息。
- `-z` 或 `--compress`：在传输过程中压缩文件数据，这可以加快通过慢速链接的传输速度。
- `-h` 或 `--human-readable`：以人类可读的格式输出数字。
- `-n` 或 `--dry-run`：执行试运行，不做任何更改。在实际执行命令之前，这对于验证 `rsync` 将执行的操作非常有用。

让我们首先确保你位于 `~/project` 目录。

```bash
cd ~/project
```

我们将通过创建源目录和目标目录来模拟同步场景。

创建一个包含一些文件的源目录 `source_dir`：

```bash
mkdir source_dir
echo "Content of fileA" > source_dir/fileA.txt
echo "Content of fileB" > source_dir/fileB.txt
mkdir source_dir/subdir
echo "Content of subfile1" > source_dir/subdir/subfile1.txt
```

创建一个空的目的地目录 `dest_dir`：

```bash
mkdir dest_dir
```

现在，让我们执行一次试运行，看看 `rsync` 在将 `source_dir` 同步到 `dest_dir` 时会做什么。我们将使用 `-avh` 选项，用于归档模式、详细输出和人类可读的大小，以及 `-n` 用于试运行。

```bash
rsync -avhn source_dir/ dest_dir/
```

**关于尾部斜杠的重要说明：**

- `source_dir/`：尾部斜杠表示“复制 `source_dir` 的内容”。
- `source_dir`：没有尾部斜杠表示“将 `source_dir` 本身复制到目标”。

试运行的输出将显示 *将要* 传输的文件：

```plaintext
sending incremental file list
./
fileA.txt
fileB.txt
subdir/
subdir/subfile1.txt

sent 186 bytes  received 12 bytes  396.00 bytes/sec
total size is 66  speedup is 0.33 (DRY RUN)
```

注意结尾的 `(DRY RUN)`，表示没有实际更改。

现在，让我们执行实际的同步。移除 `-n` 选项。

```bash
rsync -avh source_dir/ dest_dir/
```

输出将类似于试运行，但没有 `(DRY RUN)` 标签：

```plaintext
sending incremental file list
./
fileA.txt
fileB.txt
subdir/
subdir/subfile1.txt

sent 186 bytes  received 12 bytes  396.00 bytes/sec
total size is 66  speedup is 0.33
```

验证文件已复制到 `dest_dir`：

```bash
ls -R dest_dir
```

你应该看到：

```plaintext
dest_dir:
fileA.txt  fileB.txt  subdir

dest_dir/subdir:
subfile1.txt
```

现在，让我们修改 `source_dir` 中的文件并添加新文件，看看 `rsync` 的效率。

```bash
echo "Updated content for fileA" > source_dir/fileA.txt
echo "New file content" > source_dir/new_file.txt
```

再次执行试运行，看看这次 `rsync` 将传输哪些内容：

```bash
rsync -avhn source_dir/ dest_dir/
```

输出将仅显示更改和新文件：

```plaintext
sending incremental file list
./
fileA.txt
new_file.txt

sent 128 bytes  received 12 bytes  280.00 bytes/sec
total size is 100  speedup is 0.71 (DRY RUN)
```

这演示了 `rsync` 仅传输差异的功能。

现在，再次执行实际的同步：

```bash
rsync -avh source_dir/ dest_dir/
```

再次验证 `dest_dir` 的内容：

```bash
ls -R dest_dir
cat dest_dir/fileA.txt
cat dest_dir/new_file.txt
```

你应该在 `dest_dir` 中看到 `new_file.txt`，并且 `fileA.txt` 应该包含 "Updated content for fileA"。
