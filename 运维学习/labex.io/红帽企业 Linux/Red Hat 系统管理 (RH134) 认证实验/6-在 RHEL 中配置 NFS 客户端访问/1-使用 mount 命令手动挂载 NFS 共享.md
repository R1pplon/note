# 使用 mount 命令手动挂载 NFS 共享

在此步骤中，你将学习如何使用网络文件系统 (NFS) 协议来手动访问网络共享目录。NFS 允许客户端系统通过计算机网络访问文件，其方式类似于访问本地存储。在本练习中，我们将在你的本地机器上模拟 NFS 服务器和客户端，以练习必要的命令。

你的系统上已预先配置了一个 NFS 服务器，用于导出（共享）目录 `/srv/nfs/shared_data`。你的任务是将此共享目录挂载到一个本地文件夹，验证访问权限，然后将其卸载。

### 步骤 1.1：创建本地挂载点

要访问共享的 NFS 目录，你需要一个本地目录作为“挂载点”。这本质上是你客户端系统上的一个空文件夹，远程共享的内容将在挂载后出现在这里。所有操作都将在你的 `~/project` 目录下进行。

在你的项目文件夹中创建一个名为 `nfs_mount` 的目录：

```bash
mkdir ~/project/nfs_mount
```

你可以通过列出项目文件夹的内容来验证目录是否已创建：

```bash
ls -F ~/project
```

```plaintext
nfs_mount/
```

### 步骤 1.2：挂载 NFS 共享

现在你可以使用 `mount` 命令将远程 NFS 共享连接到你新创建的挂载点。该命令需要 `sudo` 权限，因为挂载文件系统是系统级别的操作。

基本语法是 `mount -t nfs <服务器>:<远程目录> <本地挂载点>`。

- `-t nfs`: 指定文件系统类型为 NFS。
- `localhost:/srv/nfs/shared_data`: 源，即服务器及其导出的路径。
- `~/project/nfs_mount`: 目标，即你的本地挂载点。

运行以下命令挂载共享：

```bash
sudo mount -t nfs localhost:/srv/nfs/shared_data ~/project/nfs_mount
```

如果命令成功执行，将不会产生任何输出。

### 步骤 1.3：验证挂载并与共享交互

运行 `mount` 命令后，你应该验证共享是否已正确挂载。你可以通过几种方式进行此操作。

首先，使用 `mount` 命令并通过管道连接 `grep` 来过滤 NFS 挂载：

```bash
mount | grep nfs
```

```plaintext
localhost:/srv/nfs/shared_data on /home/labex/project/nfs_mount type nfs4 (rw,relatime,vers=4.2,rsize=...,wsize=...,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=...,local_lock=none,addr=...)
```

接下来，检查你的挂载点内容。它现在应该显示来自远程 `/srv/nfs/shared_data` 目录的文件。

```bash
ls -l ~/project/nfs_mount
```

```plaintext
total 4
-rw-r--r--. 1 root root 32 Nov 10 14:30 welcome.txt
```

你现在可以像操作本地文件夹一样操作此目录。请注意，在此实验环境中，由于 NFS 服务器配置了 `no_root_squash`，文件所有者为 `root`。在生产环境中，根据 NFS 服务器的设置，你可能会看到所有者为 `nobody`。让我们在挂载的共享中创建一个新文件。由于 NFS 共享可能由 root 所有，你需要使用 `sudo` 和 `tee` 命令来写入文件：

```bash
echo "My test file" | sudo tee ~/project/nfs_mount/my_file.txt > /dev/null
```

验证你的新文件是否与原始文件一起存在：

```bash
ls -l ~/project/nfs_mount
```

```plaintext
total 8
-rw-r--r--. 1 root  root  13 Nov 10 14:35 my_file.txt
-rw-r--r--. 1 root  root  32 Nov 10 14:30 welcome.txt
```

### 步骤 1.4：卸载 NFS 共享

当你完成使用网络共享后，使用 `umount` 命令将其干净地卸载非常重要。这可以确保所有数据都已同步并且连接已正确关闭。你只需要指定挂载点。

```bash
sudo umount ~/project/nfs_mount
```

要确认共享已卸载，请列出 `~/project/nfs_mount` 目录的内容。它现在应该再次为空。

```bash
ls -l ~/project/nfs_mount
```

```plaintext
total 0
```
