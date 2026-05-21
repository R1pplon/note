# 使用 `lvcreate` 和 `mkfs.xfs` 创建并格式化逻辑卷

在这一步中，你将从你创建的卷组中划分出一个可用的块设备，称为逻辑卷（LV）。一旦创建了 LV，它仍然只是原始存储空间。要在其上存储文件，你必须使用文件系统对其进行格式化。

**了解逻辑卷和文件系统**

**逻辑卷（LV）** 相当于传统的磁盘分区。它从卷组中的可用空间创建，并作为标准块设备呈现给操作系统。你可以在 LV 上创建文件系统，挂载它们，并使用它们来存储数据。关键优势在于可以轻松调整 LV 的大小，这对于物理分区来说要困难得多。

**文件系统** 是操作系统用来控制数据存储和检索方式的数据结构。它将设备（如 LV）的原始空间组织成文件和目录。对于本实验，我们将使用 **XFS**，它是一个高性能的、日志式文件系统，是 Red Hat Enterprise Linux 的默认文件系统。

### 1. 创建逻辑卷

你现在将从 `my_vg` 卷组创建一个名为 `my_lv` 的 256 MiB 逻辑卷。相应的命令是 `lvcreate`。

- `-n my_lv`：指定新 LV 的名称。
- `-L 256M`：将 LV 的大小设置为 256 兆字节。
- `my_vg`：要从中创建 LV 的卷组的名称。

执行以下命令：

```bash
sudo lvcreate -n my_lv -L 256M my_vg
```

成功的命令将产生此输出：

```plaintext
  Logical volume "my_lv" created.
```

### 2. 验证逻辑卷的创建

你可以使用 `lvs` 和 `lvdisplay` 命令检查你的新 LV。

要查看所有 LV 的摘要，请运行：

```bash
sudo lvs
```

输出将显示你的新 LV `my_lv`，位于 `my_vg` 组中。

```plaintext
  LV    VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  my_lv my_vg -wi-a----- 256.00m
```

对于详细视图，你可以指定 LV 的完整路径。LV 的路径通常是 `/dev/VG_NAME/LV_NAME`。

```bash
sudo lvdisplay /dev/my_vg/my_lv
```

这提供了详细信息，包括 LV 路径，你将在下一步中需要它。

```plaintext
  --- Logical volume ---
  LV Path                /dev/my_vg/my_lv
  LV Name                my_lv
  VG Name                my_vg
  LV UUID                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  LV Write Access        read/write
  LV Creation host, time host, 2023-10-27 10:30:00 +0000
  LV Status              available
  # open                 0
  LV Size                256.00 MiB
  Current LE             64
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2
```

### 3. 使用 XFS 文件系统格式化逻辑卷

现在，你将使用 `mkfs.xfs` 命令使用 XFS 文件系统格式化 `my_lv` 逻辑卷。这将准备卷以存储文件。

```bash
sudo mkfs.xfs /dev/my_vg/my_lv
```

该命令将输出有关它正在创建的文件系统的详细信息。

```plaintext
meta-data=/dev/my_vg/my_lv       isize=512    agcount=4, agsize=16384 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=65536, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

你的逻辑卷现在已格式化，可以挂载和使用。你将在下一步中执行此操作。
