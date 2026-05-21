# 使用 locate 和 find 命令按文件名查找文件

在这一步中，你将学习如何使用两个强大的命令在你的系统上定位文件：`locate` 和 `find`。这两个命令都可以帮助你搜索文件，但它们的操作方式不同，并且适用于不同的场景。

### 使用 `locate` 命令

`locate` 命令非常快，因为它搜索预先构建的文件名和路径数据库。但是，这意味着它可能找不到自上次数据库更新以来创建或删除的文件。数据库通常由 cron 作业每天更新，但你可以强制更新。

首先，让我们确保安装了提供 `locate` 命令的 `mlocate` 包。

```bash
sudo dnf install -y mlocate
```

在安装过程中，你将看到类似这样的输出：

```plaintext
Last metadata expiration check: 0:00:01 ago on Mon 15 May 2023 08:00:00 AM UTC.
Dependencies resolved.
================================================================================
 Package        Architecture  Version             Repository               Size
================================================================================
Installing:
 mlocate        x86_64        0.26-28.el9         rhel-9-for-x86_64-appstream-rpms 100 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 100 k
Installed size: 230 k
Downloading Packages:
mlocate-0.26-28.el9.x86_64.rpm     100 kB/s | 100 kB     00:01
--------------------------------------------------------------------------------
Total                                            100 kB/s | 100 kB     00:01
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1
  Installing       : mlocate-0.26-28.el9.x86_64                             1/1
  Running scriptlet: mlocate-0.26-28.el9.x86_64                             1/1
  Verifying        : mlocate-0.26-28.el9.x86_64                             1/1
Installed:
  mlocate-0.26-28.el9.x86_64

Complete!
```

安装后，你需要更新 `locate` 数据库。此命令需要 `sudo` 权限。

```bash
sudo updatedb
```

此命令将静默运行，并且可能需要几分钟，具体取决于你的文件系统的大小。

现在，让我们搜索一个常见的系统文件，例如 `passwd`。

```bash
locate passwd
```

你将看到一个包含 "passwd" 的路径列表：

```plaintext
/etc/passwd
/etc/passwd-
/etc/pam.d/passwd
/usr/share/man/man1/passwd.1.gz
/usr/share/man/man5/passwd.5.gz
...output omitted...
```

要执行不区分大小写的搜索，请使用 `-i` 选项。让我们搜索包含 "messages" 的文件，而不必担心大小写。

```bash
locate -i messages
```

你将看到类似这样的结果：

```plaintext
/usr/share/locale/zza/LC_MESSAGES
/usr/share/makedumpfile/eppic_scripts/ap_messages_3_10_to_4_8.c
/usr/share/vim/vim82/ftplugin/msmessages.vim
...output omitted...
```

你还可以使用 `-n` 选项限制结果的数量。让我们找到 "passwd" 的前 5 个匹配项。

```bash
locate -n 5 passwd
```

这将仅显示前 5 个匹配项：

```plaintext
/etc/passwd
/etc/passwd-
/etc/pam.d/passwd
/usr/share/man/man1/passwd.1.gz
/usr/share/man/man5/passwd.5.gz
```

### 使用 `find` 命令

`find` 命令实时搜索文件系统，这使得它比 `locate` 慢，但确保它找到所有符合你条件的文件，包括最近创建的文件。它还提供了更强大的搜索选项。

`find` 的基本语法是 `find [path] [expression]`。如果未指定路径，它将搜索当前目录。

让我们从根目录 (`/`) 开始搜索 `sshd_config` 文件。

```bash
find / -name sshd_config
```

你应该看到配置文件的路径：

```plaintext
/etc/ssh/sshd_config
```

当搜索部分名称或使用通配符时，用引号引起来文件名模式很重要，以防止 shell 提前扩展它。让我们在你的 `~/project` 目录中查找所有以 `.txt` 结尾的文件。

```bash
find ~/project -name '*.txt'
```

如果你在上一步中创建了 `file1.txt`、`file2.txt` 和 `file3.txt`，你应该看到它们被列出：

```plaintext
/home/labex/project/test_data/file1.txt
/home/labex/project/test_data/file2.txt
/home/labex/project/test_data/file3.txt
```

要使用 `find` 执行不区分大小写的搜索，请使用 `-iname` 选项。让我们在 `/usr/share/doc` 目录中搜索包含 "README"（不区分大小写）的文件。

```bash
find /usr/share/doc -iname '*readme*'
```

你将看到很多结果，例如：

```plaintext
/usr/share/doc/libselinux/README
/usr/share/doc/libsepol/README
/usr/share/doc/libsemanage/README
...output omitted...
```

`find` 命令非常通用，可以与其他条件结合使用，你将在后续步骤中探索。
