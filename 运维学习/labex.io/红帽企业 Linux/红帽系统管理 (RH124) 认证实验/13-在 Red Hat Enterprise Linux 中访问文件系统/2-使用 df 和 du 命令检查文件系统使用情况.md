# 使用 df 和 du 命令检查文件系统使用情况

在这一步中，你将深入研究使用 `df` 和 `du` 命令检查文件系统使用情况。虽然 `df` 提供了已挂载文件系统的磁盘空间使用情况的概览，但 `du`（磁盘使用情况）用于估算特定文件或目录的文件空间使用情况。了解两者之间的区别以及何时使用每个命令对于有效的磁盘空间管理至关重要。

让我们从重新审视带有其人类可读选项的 `df` 命令开始。此命令非常适合快速总结所有已挂载文件系统上已使用和可用的空间量。

```bash
df -h
```

输出将以易于阅读的格式显示磁盘使用情况（例如，G 代表千兆字节，M 代表兆字节）：

```plaintext
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        892M     0  892M   0% /dev
tmpfs           915M     0  915M   0% /dev/shm
tmpfs           915M   17M  899M   2% /run
tmpfs           915M     0  915M   0% /sys/fs/cgroup
/dev/vda4       8.0G  1.4G  6.7G  17% /
/dev/vda3      1014M  166M  849M  17% /boot
tmpfs           183M     0  183M   0% /run/user/1000
```

现在，让我们探索 `du` 命令。与 `df` 不同，`du` 计算指定路径内文件和目录使用的磁盘空间。当你想要找出哪些目录或文件占用了最多的空间时，这特别有用。

要查看当前目录 (`~/project`) 的磁盘使用情况，请使用不带任何参数的 `du`。这将列出 `~/project` 内每个文件和子目录的大小。

```bash
du
```

输出可能很长，默认情况下以千字节为单位显示大小：

```plaintext
4       ./.config/xfce4/xfconf/xfce-perchannel-xml
8       ./.config/xfce4/xfconf
12      ./.config/xfce4
16      ./.config
4       ./.local/share/nano
8       ./.local/share
12      ./.local
28      .
```

为了使输出更具可读性，类似于 `df -h`，你可以将 `-h` 选项与 `du` 一起使用。

```bash
du -h
```

这将以人类可读的单位显示大小：

```plaintext
4.0K    ./.config/xfce4/xfconf/xfce-perchannel-xml
8.0K    ./.config/xfce4/xfconf
12K     ./.config/xfce4
16K     ./.config
4.0K    ./.local/share/nano
8.0K    ./.local/share
12K     ./.local
28K     .
```

通常，你对目录的总大小感兴趣，而不是每个单独文件和子目录的大小。为此，你可以将 `-s`（summary，摘要）选项与 `-h` 一起使用。让我们检查你的主目录 (`~`) 的总大小。

```bash
du -sh ~
```

此命令将输出你的主目录的总大小：

```plaintext
48K     /home/labex
```

让我们创建一些文件来查看 `du` 如何报告它们的大小。我们将创建一个名为 `test_data` 的目录，然后在其中创建几个文件。

首先，创建目录：

```bash
mkdir ~/project/test_data
```

现在，进入 `test_data` 目录：

```bash
cd ~/project/test_data
```

接下来，创建一些包含内容的文件。我们将使用 `head` 命令生成特定大小的文件。

```bash
head -c 1K < /dev/urandom > file1.txt
head -c 5K < /dev/urandom > file2.txt
head -c 10K < /dev/urandom > file3.txt
```

现在，使用 `du -h` 查看这些新文件和 `test_data` 目录的大小。

```bash
du -h
```

你应该看到类似这样的输出：

```plaintext
1.0K    ./file1.txt
5.0K    ./file2.txt
10K     ./file3.txt
24K     .
```

最后一行 (`24K .`) 显示当前目录 (`.`，即 `~/project/test_data`) 的总大小。

最后，让我们回到你的 `~/project` 目录，并使用 `du -sh` 检查 `test_data` 的总大小。

```bash
cd ~/project
du -sh test_data
```

这将显示 `test_data` 目录的摘要大小：

```plaintext
24K     test_data
```

这演示了如何使用 `du` 来查明特定目录内的磁盘空间消耗，从而帮助你有效地管理存储。
