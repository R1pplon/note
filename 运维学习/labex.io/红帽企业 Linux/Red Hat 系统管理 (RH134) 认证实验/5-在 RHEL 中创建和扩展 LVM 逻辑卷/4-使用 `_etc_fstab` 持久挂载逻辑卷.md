# 使用 `/etc/fstab` 持久挂载逻辑卷

在这一步中，你将使你创建和格式化的逻辑卷对系统可用。这涉及两个操作：创建一个目录作为“挂载点”，然后配置系统，以便在每次启动时自动将逻辑卷附加到此目录。

**了解挂载和 `/etc/fstab`**

一个已格式化的块设备，例如你的 `my_lv` 逻辑卷，在它被**挂载**之前不能直接使用。挂载是将文件系统附加到主文件系统树中的特定目录的过程。挂载后，你可以进入该目录以读取和写入设备上的文件。

为了确保在重新启动后自动挂载文件系统，你必须在 `/etc/fstab`（文件系统表）文件中为其添加一个条目。此文件包含系统应在启动时挂载的所有磁盘和分区的列表。

### 1. 创建挂载点

挂载点只是一个空目录，它将充当你的新文件系统的根目录。我们将为此目的创建一个名为 `/data` 的目录。

```bash
sudo mkdir /data
```

### 2. 在 `/etc/fstab` 中添加一个条目以进行持久挂载

现在，你需要告诉系统自动将你的 LV 挂载到 `/data` 目录。你将向 `/etc/fstab` 文件添加一个新行。此文件中的每一行都有六个字段：

1. **设备（Device）**：要挂载的设备。在我们的例子中，是 `/dev/my_vg/my_lv`。
2. **挂载点（Mount Point）**：要挂载到的目录。这里是 `/data`。
3. **文件系统类型（Filesystem Type）**：文件系统的类型，即 `xfs`。
4. **挂载选项（Mount Options）**：用于挂载的选项。 `defaults` 是一组适用于大多数情况的标准选项。
5. **转储（Dump）**：由 `dump` 实用程序使用，以决定是否备份文件系统。 `0` 表示禁用。
6. **传递（Pass）**：由 `fsck` 使用，以确定在启动时检查文件系统的顺序。 `0` 表示不检查。

我们将使用 `echo` 命令与 `sudo tee -a` 结合使用，将正确的行追加到 `/etc/fstab`，而无需文本编辑器。

```bash
echo '/dev/my_vg/my_lv /data xfs defaults 0 0' | sudo tee -a /etc/fstab
```

你可以通过查看文件的内容来验证该行是否已正确添加：

```bash
cat /etc/fstab
```

### 3. 挂载文件系统

现在 `/etc/fstab` 中存在该条目，你可以使用 `mount` 命令立即挂载它。因为挂载点 `/data` 在 `/etc/fstab` 中列出，所以你只需要提供目录名称。

```bash
sudo mount /data
```

系统将读取 `/etc/fstab`，找到 `/data` 的条目，并挂载相应的设备。

### 4. 验证挂载

要确认文件系统已成功挂载，你可以使用带有 `-h`（人类可读）选项的 `df`（磁盘空闲）命令。

```bash
df -h /data
```

输出应显示你的逻辑卷挂载在 `/data` 上，并显示其总大小和可用空间。

```plaintext
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/my_vg-my_lv     251M   28M  224M  11% /data
```

你还可以创建一个测试文件以确保你具有写入权限：

```bash
echo "My LVM is working!" | sudo tee /data/test.txt
```

然后，读回该文件以确认它已被写入：

```bash
cat /data/test.txt
```

```plaintext
My LVM is working!
```

你的逻辑卷现在已挂载，并且将在每次系统启动时自动可用。
