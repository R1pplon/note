# 配置并挂载 NFS 共享

在第一步中，你将配置本地机器作为 NFS 服务器。你将导出一个目录，启动必要的 NFS 服务，然后将该目录挂载回本地机器（此时本地机器充当 NFS 客户端）。这是测试 NFS 配置的一种常用方法。

## 任务

- 配置 NFS 服务器以导出 `/nfs_share` 目录。
- 启动所需的 NFS 服务。
- 配置 NFS 客户端将导出的目录挂载到 `/mnt/nfs`。

## 要求

- 必须配置 `/etc/exports` 文件，以便与本地机器共享 `/nfs_share` 目录。
- NFS 共享必须挂载在 `/mnt/nfs` 目录下。
- 所有操作应在本地机器（`localhost`）上执行。

## 提示

- 你需要编辑 `/etc/exports` 文件来定义共享目录及其权限。测试时常用的配置是 `(rw,sync,no_root_squash)`。
- 在没有 `systemd` 的容器环境中，你无法使用 `systemctl`。相反，你必须手动启动 `rpcbind`、`nfsd` 和 `mountd` 服务。请使用 `rpcbind`、`rpc.nfsd` 和 `rpc.mountd` 命令。
- 修改 `/etc/exports` 后，使用 `exportfs -r` 命令使更改生效。
- 在本地机器上挂载时的服务器地址为 `localhost`。
