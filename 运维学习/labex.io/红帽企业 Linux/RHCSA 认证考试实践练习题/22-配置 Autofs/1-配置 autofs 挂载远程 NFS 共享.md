# 配置 autofs 挂载远程 NFS 共享

你的任务是配置 `autofs` 以自动挂载 NFS 共享。实验环境已预先在 `localhost` 上配置了 NFS 服务器，并导出了 `/shared` 目录。你需要创建两个配置文件：一个是告诉 `autofs` 监控哪些目录的主映射文件（Master Map File），另一个是定义挂载细节的具体映射文件。

## 任务

- 在 `/etc/auto.master.d/nfs.autofs` 创建 `autofs` 主映射文件。
- 在 `/etc/auto.nfs` 创建 `autofs` 映射文件以定义 NFS 挂载信息。
- 重启 `autofs` 服务以应用新配置。
- 验证在访问时 NFS 共享是否已自动挂载。

## 要求

- 主映射文件必须创建在 `/etc/auto.master.d/nfs.autofs`。
- `/etc/auto.master.d/nfs.autofs` 的内容必须严格为：

```plaintext
/mnt/nfs  /etc/auto.nfs
```

- 映射文件必须创建在 `/etc/auto.nfs`。
- `/etc/auto.nfs` 的内容必须严格为：

```plaintext
shared  -fstype=nfs,soft,intr  localhost:/shared
```

- 必须使用以下命令重启 `autofs` 服务：

```bash
sudo systemctl restart autofs
```

## 示例

完成配置后，访问 `/mnt/nfs/shared` 应该能够成功并列出远程共享的内容。例如，你应该能看到 `test.txt` 文件。

```bash
ls /mnt/nfs/shared
```

预期输出：

```plaintext
test.txt
```
