# 在 /etc/fstab 中配置持久化 NFS 挂载

在上一步中，你学习了如何使用 `mount` 命令手动挂载 NFS 共享。然而，这种挂载是临时的，在系统重启后会失效。要使挂载永久生效，你需要向 `/etc/fstab` 文件（“文件系统表”的缩写）添加一个条目。此文件包含一个列表，列出了系统启动时自动挂载的文件系统和设备。

在本步骤中，你将通过向 `/etc/fstab` 添加条目来配置相同的 NFS 共享以实现持久化挂载。

### 步骤 2.1：准备环境

首先，确保上一步中的挂载点 `~/project/nfs_mount` 存在且为空。如果你是直接从上一步继续，它应该已经存在。

如果目录不存在，请立即创建：

```bash
mkdir -p ~/project/nfs_mount
```

另外，请确保当前没有内容挂载到此目录。你可以运行 `umount` 命令，如果未挂载，它将报告错误，这完全没问题。

```bash
sudo umount ~/project/nfs_mount
```

### 步骤 2.2：编辑 `/etc/fstab` 文件

现在，你将向 `/etc/fstab` 文件添加一个新行来定义持久化 NFS 挂载。你必须使用 `sudo` 来编辑此系统配置文件。我们将使用 `nano` 编辑器。

使用以下命令打开文件：

```bash
sudo nano /etc/fstab
```

导航到文件底部并添加以下行。请非常小心语法，因为此文件中的错误可能导致系统启动问题。

```plaintext
localhost:/srv/nfs/shared_data /home/labex/project/nfs_mount nfs defaults,_netdev 0 0
```

让我们分解这一行：

- **`localhost:/srv/nfs/shared_data`**: 这是要挂载的设备。它指定了 NFS 服务器 (`localhost`) 和导出的目录 (`/srv/nfs/shared_data`)。
- **`/home/labex/project/nfs_mount`**: 这是本地挂载点，共享将在此处可访问。
- **`nfs`**: 这指定了文件系统类型。
- **`defaults,_netdev`**: 这些是挂载选项。`defaults` 包含一组标准选项（如 `rw` 表示读写）。`_netdev` 对网络文件系统至关重要；它告诉系统在尝试挂载此共享之前等待网络激活。
- **`0`**: 这是 `dump` 字段，由 `dump` 备份实用程序使用。值为 `0` 表示禁用它。
- **`0`**: 这是 `pass` 字段，由 `fsck` 实用程序用于确定启动时文件系统检查的顺序。值为 `0` 表示文件系统不会被检查。

添加行后，按 `Ctrl+X`，然后按 `Y`，最后按 `Enter` 保存文件并退出 `nano`。

### 步骤 2.3：测试 `/etc/fstab` 条目

你无需重启即可测试新的 `/etc/fstab` 条目。`mount` 命令足够智能，可以读取 `/etc/fstab`。如果你只提供挂载点，`mount` 将查找 `/etc/fstab` 中相应的条目并使用它找到的信息。

仅使用挂载点挂载共享：

```bash
sudo mount ~/project/nfs_mount
```

如果命令成功完成而没有错误，则你的 `/etc/fstab` 条目是正确的。

### 步骤 2.4：验证挂载

通过检查 `mount` 命令的输出并列出目录内容来验证共享是否已挂载。

```bash
mount | grep nfs_mount
```

```plaintext
localhost:/srv/nfs/shared_data on /home/labex/project/nfs_mount type nfs4 (rw,relatime,vers=4.2,rsize=...,wsize=...,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=...,local_lock=none,addr=...,_netdev)
```

现在，检查内容。你应该能看到共享中的文件。

```bash
ls -l ~/project/nfs_mount
```

```plaintext
total 4
-rw-r--r--. 1 root root 32 Nov 10 14:30 welcome.txt
```

此挂载现在是持久化的，并且将在重启后自动重新建立。

### 步骤 2.5：清理环境

为避免与后续实验冲突，你应该撤销更改。首先，卸载共享，然后从 `/etc/fstab` 中删除你添加的行。

卸载目录：

```bash
sudo umount ~/project/nfs_mount
```

再次打开 `/etc/fstab` 以删除条目：

```bash
sudo nano /etc/fstab
```

使用箭头键导航到你添加的行（`localhost:/srv/nfs/shared_data ...`），然后按 `Ctrl+K` 删除整行。然后，按 `Ctrl+X`，`Y`，然后 `Enter` 保存并退出。

这样可以使系统为实验的下一部分保持干净的状态。
