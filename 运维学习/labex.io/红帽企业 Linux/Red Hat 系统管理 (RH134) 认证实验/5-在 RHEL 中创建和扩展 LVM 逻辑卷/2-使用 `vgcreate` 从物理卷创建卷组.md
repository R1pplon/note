# 使用 `vgcreate` 从物理卷创建卷组

在这一步中，你将使用你在上一步中准备的物理卷（PV）来创建卷组（VG）。卷组是 LVM 的一个核心组件，它充当一个单一的、可管理的磁盘空间池，你可以从中创建逻辑卷。

**了解卷组和物理扩展**

**卷组（VG）** 将一个或多个物理卷聚合到一个存储池中。可以将其想象成将几个较小的水容器组合成一个大水箱。这种抽象是 LVM 具有灵活性的原因。

此存储池被划分为称为**物理扩展（PE）** 的小的、固定大小的块。默认情况下，PE 为 4 MiB。稍后创建逻辑卷时，你本质上是从卷组中分配一定数量的这些 PE。

### 1. 创建卷组

现在，你将使用 `/dev/vdb1` 物理卷创建一个名为 `my_vg` 的卷组。相应的命令是 `vgcreate`。

```bash
sudo vgcreate my_vg /dev/vdb1
```

如果命令成功，你将看到一条确认消息：

```plaintext
  Volume group "my_vg" successfully created
```

此命令创建了一个名为 `my_vg` 的新存储池，其中包含来自 `/dev/vdb1` 的所有可用空间。

### 2. 显示卷组信息

就像你对物理卷所做的那样，你可以显示有关新卷组的信息。 `vgs` 命令提供了一个摘要，而 `vgdisplay` 提供了详细的视图。

要获取系统中所有卷组的简洁摘要，请运行：

```bash
sudo vgs
```

输出将显示你的新 VG、其大小以及可用空间的数量。

```plaintext
  VG    #PV #LV #SN Attr   VSize   VFree
  my_vg   1   0   0 wz--n- 508.00m 508.00m
```

要获取有关你的特定卷组的更详细报告，请使用 `vgdisplay`：

```bash
sudo vgdisplay my_vg
```

此输出提供了全面的详细信息，包括 PE 大小、PE 总数以及仍有多少 PE 剩余。

```plaintext
  --- Volume group ---
  VG Name               my_vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               508.00 MiB
  PE Size               4.00 MiB
  Total PE              127
  Alloc PE / Size       0 / 0
  Free  PE / Size       127 / 508.00 MiB
  VG UUID               xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

请注意，`PE Size` 为 4.00 MiB，并且你有 127 个 `Free PE`，这对应于卷组中的总可用空间。你现在已经成功创建了一个卷组，并且已准备好从中创建逻辑卷。
