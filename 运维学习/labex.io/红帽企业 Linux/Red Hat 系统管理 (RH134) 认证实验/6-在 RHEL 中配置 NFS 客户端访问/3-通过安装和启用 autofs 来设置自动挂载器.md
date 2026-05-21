# 通过安装和启用 autofs 来设置自动挂载器

在之前的步骤中，你探索了手动挂载和持久化挂载。虽然 `/etc/fstab` 非常适合永久挂载，但它有一个缺点：它会尝试在启动时挂载所有内容。如果网络共享不可用，它可能会减慢甚至中断启动过程。自动挂载器（由 `autofs` 服务提供）通过按需挂载网络文件系统来解决此问题，仅在首次访问时进行。

`autofs` 服务使用一组称为“映射”（maps）的配置文件来确定要挂载哪些远程共享以及挂载到何处。在本步骤中，你将通过安装必要的软件包并启动其服务来准备你的系统使用自动挂载器。

### 步骤 3.1：安装 `autofs` 软件包

`autofs` 功能不包含在默认的 RHEL 安装中。你需要使用 `dnf` 包管理器来安装它。这需要 `sudo` 权限。

运行以下命令安装 `autofs` 软件包。`-y` 标志会自动回答确认提示中的“是”，这对于本次实验很方便。

```bash
sudo dnf install -y autofs
```

该命令将下载并安装 `autofs` 软件包及其任何必需的依赖项。你将看到类似以下的输出：

```plaintext
Last metadata expiration check: ...
Dependencies resolved.
================================================================================
 Package       Architecture    Version                Repository           Size
================================================================================
Installing:
 autofs        x86_64          1:5.1.7-50.el9         ...                  ...
...

Transaction Summary
================================================================================
Install  1 Package

Total download size: ...
Installed size: ...
...
Complete!
```

### 步骤 3.2：启动 `autofs` 服务

在标准的 RHEL 系统上，你会使用 `systemctl` 来启动和启用服务。然而，本次实验在容器化环境中运行，其中 `systemctl` 不可用。相反，我们将直接使用其命令 `automount` 来启动 `autofs` 守护进程。

此命令启动自动挂载器守护进程，它将在后台运行，并监视对其映射中配置的目录的访问尝试。

执行以下命令启动服务：

```bash
sudo automount
```

如果成功，此命令将不会产生任何输出。它只是启动了守护进程。

### 步骤 3.3：验证服务是否正在运行

由于你无法使用 `systemctl status autofs` 来检查服务状态，你可以使用 `ps` 命令来验证 `automount` 进程是否正在运行。`ps aux` 命令会列出所有正在运行的进程，我们可以通过管道 `|` 将其输出传递给 `grep` 来过滤出 `automount` 进程。

```bash
ps aux | grep automount
```

你应该至少看到一行关于 `automount` 进程本身的输出。第二行显示 `grep automount` 仅仅是你运行的 `grep` 命令本身，可以忽略。

```plaintext
root      ...  0.0  0.0 ...      ?        Ssl  15:30   0:00 /usr/sbin/automount
labex     ...  0.0  0.0 ...      pts/0    S+   15:31   0:00 grep --color=auto automount
```

看到 `/usr/sbin/automount` 进程表明服务正在运行并准备好处理按需挂载。在接下来的步骤中，你将配置告诉 `autofs` 该做什么的映射。
