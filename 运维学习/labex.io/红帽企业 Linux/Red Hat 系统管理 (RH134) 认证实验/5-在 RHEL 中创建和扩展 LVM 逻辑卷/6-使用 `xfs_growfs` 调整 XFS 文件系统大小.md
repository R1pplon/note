# 使用 `xfs_growfs` 调整 XFS 文件系统大小

在最后一步中，你将通过调整 XFS 文件系统的大小来完成存储扩展过程，使其能够识别你为其底层逻辑卷分配的额外空间。这是使新空间可用于存储文件的关键最后一步。

**了解文件系统调整大小**

当你扩展逻辑卷时，你只是在增加容器（块设备）的大小。该容器*内*的文件系统仍保持其原始大小，并且不知道设备末尾的新未使用空间。

对于 XFS 文件系统，`xfs_growfs` 命令用于扩展文件系统以填充底层设备。XFS 的一个主要优点是，此操作可以在线执行，同时文件系统已挂载并正在使用，无需停机时间。

### 1. 扩展 XFS 文件系统

`xfs_growfs` 命令以文件系统的挂载点作为其参数。在你的例子中，挂载点是 `/data`。

运行以下命令以扩展文件系统：

```bash
sudo xfs_growfs /data
```

该命令将输出有关更改的信息，指示数据块的旧数量和新数量。

```plaintext
meta-data=/dev/mapper/my_vg-my_lv isize=512    agcount=4, agsize=16384 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=65536, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 65536 to 102400
```

关键行是 `data blocks changed from 65536 to 102400`，它确认文件系统已增长。

### 2. 验证新的文件系统大小

现在，再次运行 `df -h` 命令以验证文件系统大小是否反映了更改。

```bash
df -h /data
```

输出现在应显示新的、更大的大小，大约为 400 MiB。

```plaintext
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/my_vg-my_lv     395M   29M  367M   8% /data
```

你还可以检查你之前创建的测试文件是否仍然完好无损：

```bash
cat /data/test.txt
```

```plaintext
My LVM is working!
```

恭喜你！你已成功完成了整个 LVM 工作流程：从对物理磁盘进行分区和创建物理卷，到构建卷组、创建逻辑卷、格式化和挂载它，最后，在没有任何数据丢失的情况下动态地在线扩展它。
