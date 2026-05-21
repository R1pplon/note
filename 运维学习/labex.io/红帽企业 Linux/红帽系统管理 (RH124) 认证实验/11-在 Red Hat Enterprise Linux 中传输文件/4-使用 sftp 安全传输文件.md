# 使用 sftp 安全传输文件

本实验将介绍如何使用 `sftp` (安全文件传输程序) 在系统之间安全地传输文件。`sftp` 是一款交互式文件传输程序，它使用 SSH (安全外壳) 进行安全通信，提供加密和身份验证。它属于 OpenSSH 套件的一部分。

在本实验中，我们将使用 `labex` 用户作为“远程”用户模拟远程系统，与同一主机上的用户交互。这允许我们在无需单独的虚拟机的情况下练习 `sftp` 命令。

首先，确保你位于 `~/project` 目录。

```bash
cd ~/project
```

让我们创建一个我们将“上传”到模拟远程用户主目录的文件。

```bash
echo "This file will be uploaded via sftp." > local_file.txt
```

现在，启动到 `localhost` 上 `labex` 用户的 `sftp` 会话。

```bash
sftp labex@localhost
```

系统将提示你输入 `labex@localhost` 的密码。输入 `labex`。

```plaintext
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ED25519 key fingerprint is SHA256:....
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'localhost' (ED25519) to the list of known hosts.
labex@localhost's password: labex
Connected to localhost.
sftp>
```

你现在处于 `sftp` 交互提示符中。

在 `sftp` 提示符中，你可以使用类似于普通 shell 的各种命令。

- `pwd`：显示远程系统当前工作目录。
- `lpwd`：显示本地系统当前工作目录。
- `ls`：列出远程系统上的文件。
- `lls`：列出本地系统上的文件。

让我们尝试一下：

```
sftp> pwd
Remote working directory: /home/labex
sftp> lpwd
Local working directory: /home/labex/project
sftp> ls
```

（`ls` 命令将显示远程 `/home/labex` 目录的内容，即你自己的主目录。）

现在，让我们将 `local_file.txt` 从你本地的 `~/project` 目录上传到远程 `labex` 用户的主目录 (`/home/labex`)。使用 `put` 命令。

```
sftp> put local_file.txt
Uploading local_file.txt to /home/labex/local_file.txt
local_file.txt                               100%   32     0.0KB/s   00:00
sftp>
```

你可以通过列出远程目录来验证文件是否已上传：

```
sftp> ls
```

你应该会在 `/home/labex` 中列出的文件中看到 `local_file.txt`。

接下来，让我们从远程系统“下载”一个文件。我们将从远程 `labex` 用户的主目录下载 `.bashrc` 文件到你的本地 `~/project` 目录。使用 `get` 命令。

```
sftp> get .bashrc
Fetching /home/labex/.bashrc to .bashrc
/home/labex/.bashrc                          100%  193     0.2KB/s   00:00
sftp>
```

你可以通过列出你的本地目录来验证下载：

```
sftp> lls
```

你应该会在你的本地 `~/project` 目录中看到 `.bashrc`。

要退出 `sftp` 会话，请使用 `exit` 或 `bye` 命令。

```
sftp> exit
```

你将返回到你的普通 shell 提示符。
