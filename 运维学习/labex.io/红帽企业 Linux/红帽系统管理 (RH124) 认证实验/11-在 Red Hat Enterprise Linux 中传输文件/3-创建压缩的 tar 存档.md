# 创建压缩的 tar 存档

本步骤将介绍如何创建压缩的 `tar` 归档文件。虽然 `tar` 本身只是打包文件，但它可以与压缩工具（如 `gzip`、`bzip2` 和 `xz`）集成，创建更小的归档文件。这对于节省磁盘空间和减少传输时间至关重要。

`tar` 命令为不同的压缩算法提供了特定的选项：

- `-z` 或 `--gzip`：使用 `gzip` 压缩，生成 `.tar.gz` 或 `.tgz` 后缀。这是最常见且最快的压缩方法。
- `-j` 或 `--bzip2`：使用 `bzip2` 压缩，生成 `.tar.bz2` 或 `.tbz` 后缀。它通常比 `gzip` 提供更好的压缩效果，但速度较慢。
- `-J` 或 `--xz`：使用 `xz` 压缩，生成 `.tar.xz` 或 `.txz` 后缀。它在三种方法中提供最佳的压缩比，但速度最慢。
- `-a` 或 `--auto-compress`：允许 `tar` 根据归档文件的后缀自动确定压缩算法（例如，`.tar.gz` 暗示使用 `gzip`）。这是一个方便的选项。

让我们首先确保你位于 `~/project` 目录。

```bash
cd ~/project
```

首先，我们将创建一个 `gzip` 压缩的 `my_files` 目录归档文件。我们将将其命名为 `my_archive.tar.gz`。

```bash
tar -czvf my_archive.tar.gz my_files
```

输出将显示正在添加和压缩的文件：

```plaintext
my_files/
my_files/file1.txt
my_files/file2.txt
my_files/file3.txt
```

你可以验证压缩归档文件的创建：

```bash
ls -lh my_archive.tar.gz
```

`ls` 的 `-lh` 选项提供人类可读的大小。你将看到类似于此的输出，显示文件大小：

```plaintext
-rw-r--r-- 1 labex labex 180 Oct 27 10:00 my_archive.tar.gz
```

(注意：实际大小可能略有不同，具体取决于系统和内容，但对于这些小型文本文件来说，它会是一个较小的尺寸。)

现在，让我们尝试创建一个 `xz` 压缩的归档文件，它通常提供更好的压缩效果。我们将将其命名为 `my_archive.tar.xz`。

```bash
tar -cJvf my_archive.tar.xz my_files
```

同样，输出将显示正在处理的文件：

```plaintext
my_files/
my_files/file1.txt
my_files/file2.txt
my_files/file3.txt
```

检查 `xz` 归档文件的大小：

```bash
ls -lh my_archive.tar.xz
```

你可能会注意到 `my_archive.tar.xz` 比 `my_archive.tar.gz` 略小，这表明 `xz` 具有更好的压缩比。

```plaintext
-rw-r--r-- 1 labex labex 168 Oct 27 10:00 my_archive.tar.xz
```

要提取压缩的 `tar` 归档文件，`tar` 在使用 `-x` 选项时足够智能，通常可以自动检测压缩类型。但是，最好显式使用相应的解压缩选项 (`-z`、`-j` 或 `-J`) 或 `-a` (自动压缩) 选项。

让我们尝试将 `my_archive.tar.gz` 提取到名为 `extracted_gz` 的新目录中。

```bash
mkdir extracted_gz
tar -xzvf my_archive.tar.gz -C extracted_gz
```

`-C` 选项 (更改目录) 指示 `tar` 将文件提取到指定的目录。这是一个非常有用的选项，以避免弄乱你的当前目录。

验证 `extracted_gz` 的内容：

```bash
ls extracted_gz/my_files
```

你应该看到：

```plaintext
file1.txt  file2.txt  file3.txt
```
